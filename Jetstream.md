# Jetstream
## Jetstream (인증 스캐폴딩), fortify (Backend 인증), sanctum (토큰), inertia (Frontend Vue.js), livewire (Frontend php)
* https://jetstream.laravel.com/installation.html
* `Jetstream`은 회원 등록, 로그인, 로그아웃, 회원 정보 등 필수 페이지들을 제공한다.

### 프로젝트에 Jetstream 설치
```sh
composer require laravel/jetstream

# jetstream:install -h 설명 보기
php artisan jetstream:install -h
php artisan jetstream:install inertia
# or
php artisan jetstream:install livewire

npm install
npm run build
php artisan migrate
php artisan serve
```

#### PDOException::("could not find driver")
* https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=meelong0&logNo=140126617258

php.ini
```ini
extension=php_pdo_mysql.dll
```

### 회원 등록, 로그인, 로그아웃, 회원 정보 동작 확인
* http://127.0.0.1:8000/register
* http://127.0.0.1:8000/user/profile

### API 회원 연동
#### 생성
```sh
# route 목록이 많이 늘었는지 확인
php artisan route:list
```

routes/api.php
```php
use Laravel\Fortify\Http\Controllers\RegisteredUserController;

Route::middleware(['guest:'.config('fortify.guard')])
    ->post('/register', [RegisteredUserController::class, 'store']);
```

```sh
# route 목록에서 api/register 있는지 확인
php artisan route:list
```

Postman
```sh
Method: POST
URL: http://localhost:8000/api/register
Headers: Accept application/json
Body: raw JSON
```
```json
{
    "name": "홍길동",
    "email": "a@a.com",
    "password": "password",
    "password_confirmation": "password"
}
```

#### 로그인과 토큰 생성
* https://laravel.com/docs/10.x/sanctum#issuing-api-tokens

routes/api.php
```php
use Illuminate\Support\Facades\Hash;
use Illuminate\Validation\ValidationException;
use App\Models\User;

Route::post('/login', function (Request $request) {
    $request->validate([
        'email' => 'required|email',
        'password' => 'required',
        'device_name' => 'required'
    ]);
    $user = User::where('email', $request->email)->first();
    if (!$user || !Hash::check($request->password, $user->password)) {
        throw ValidationException::withMessages([
            'email' => ['이메일 또는 비밀번호가 일치하지 않습니다.']
        ]);
    }
    return $user->createToken($request->device_name)->plainTextToken;
});
```

Postman
```sh
Method: POST
URL: http://localhost:8000/api/login
Headers: Accept application/json
Body: raw JSON
```
```json
{
    "email": "a@a.com",
    "password": "password",
    "device_name": "iPhone"
}
```
* 로그인 하면 `토큰`를 출력 하고 `personal_access_tokens` 테이블에 `device_name`으로 데이터가 생성 된다.

#### 토큰을 이용한 사용자 정보 받기
Postman
```sh
Method: GET
URL: http://localhost:8000/api/user
Authorization: Type > Bearer Token > Token > http://localhost:8000/api/login에서 받은 토큰 넣기
```
* 해당 User의 모든 정보가 출력 된다.
* Headers > 'Authorization', 'Bearer {토큰}' 이렇게 Headers 값이 생긴다.

#### User 정보 필터링 하기
```sh
php artisan make:resource UserResource
```

app/Http/Resources/UserResource.php
```diff
- return parent::toArray($request);
```
```php
return [
    'id' => $this->id,
    'name' => $this->name,
    'email' => $this->email,
    'profile_photo_url' => $this->profile_photo_url
];
```

routes/api.php
```php
use App\Http\Resources\UserResource;
```
```diff
- return $request->user();
return (new UserResource($request->user()))->response();
```
* Postman 확인

##### 응답 Header 추가
app/Http/Resources/UserResource.php
```php
public function withResponse($request, $response)
{
    $response->header('abc', '123');
}
```

#### 로그아웃
* https://laravel.com/docs/10.x/sanctum#revoking-tokens

routes/api.php
```php
Route::middleware('auth:sanctum')
    ->get('/logout', function (Request $request) {
        // 해당 User가 로그인된 모든 device에 로그아웃
        $request->user()->tokens()->delete();
        // 해당 User의 현재 토큰만 로그아웃
        // $request->user()->currentAccessToken()->delete();
        return response()->json([
            'message' => '토큰만료 성공',
            'array' => [1, 2, 3]
        ]);
        // response('토큰만료 성공', 200)
        //     ->header('Content-Type', 'text/plain');
    }
);
```
Postman
```sh
Method: GET
URL: http://localhost:8000/api/logout
Authorization: Type > Bearer > Token > http://localhost:8000/api/login에서 받은 토큰 넣기
```

### 토큰 생성 페이지
config/jetstream.php
```diff
- // Features::api(),
+ Features::api(),
```
* `user/api-tokens` 경로가 `route:list`에 추가 됨
* http://localhost:8000/user/api-tokens

### 이메일 인증
app/Models/User.php
```diff
- class User extends Authenticatable
+ class User extends Authenticatable implements MustVerifyEmail
```

config/fortify.php
```diff
- // Features::emailVerification(),
+ Features::emailVerification(),
```

#### Mailtrap (테스트 이메일 서비스: smtp 제공, Inbox: 1개 무료 사용 가능)
* https://mailtrap.io

```sh
Email Testing > Inboxes > My Inbox > SMTP Settings > Integrations > Laravel 9+ > 설정 복사
.env 파일에 설정 적용

# 회원 가입 하면, 인증 이메일 자동 발송 > My Inbox에서 확인
```
