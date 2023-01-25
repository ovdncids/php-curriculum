# php-curriculum (Laravel)
* https://laravel.com/docs/9.x
* https://laravel.kr/docs/9.x/installation

## 설치
### Mac
```sh
brew install php
php -v
# php 8.x 버전이 아니면 터미널 재시작

brew install composer
```

### Windows
* https://windows.php.net/download
```sh
PHP 8.2 > VS16 x64 Non Thread Safe > Zip > 압축 풀고 > C:\Program Files로 이동
```

* https://getcomposer.org
```sh
Download > 설치 > PHP 경로 선택 > 완료 되면 알아서 환경 변수에 php, composer 경로 추가 됨
```

### VSCode 확장 프로그램
```sh
# PHP 라이브러리에 접근 가능
PHP Intelephense

# Laravel 라이브러리에 접근 가능
Laravel Extra Intellisense
```

### 프로젝트 생성
```sh
composer global require laravel/installer
laravel new laravel-study

cd laravel-study
php artisan serve
```

#### 설치 오류
* https://joorang.tistory.com/28
```sh
laravel/framework[v8.12.0, ..., 8.x-dev] require league/flysystem ^1.1 -> satisfiable by league/flysystem[1.1.0, ..., 1.x-dev]
```
php.ini
```ini
extension=php_fileinfo
```

## MySQL 연결
```sh
# root 권한으로 로그인
create database laravel_study;
```
.env
```env
DB_DATABASE=laravel_study
```

## Jetstream (인증 스캐폴딩), fortify (Backend 인증), sanctum (토큰), inertia (Frontend Vue.js), livewire (Frontend php)
* https://jetstream.laravel.com/2.x/installation.html
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

### API 회원 연동
#### 생성
```sh
# route 목록
php artisan route:list
```

config/jetstream.php
```diff
- // Features::api(),
+ Features::api(),
```

routes/api.php
```php
use Laravel\Fortify\Http\Controllers\RegisteredUserController;

Route::middleware(['guest:'.config('fortify.guard')])
    ->post('/register', [RegisteredUserController::class, 'store']);
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
* https://laravel.com/docs/9.x/sanctum#issuing-api-tokens

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
* 로그안 하면 `토큰`를 출력 하고 `personal_access_tokens` 테이블에 `device_name`으로 데이터가 생성 된다.

#### 토큰을 이용한 사용자 정보 받기
Postman
```sh
Method: GET
URL: http://localhost:8000/api/user
Authorization: Type > Bearer > Token > http://localhost:8000/api/login에서 받은 토큰 넣기
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
```diff
- return $request->user();
```
```php
use App\Http\Resources\UserResource;

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
* https://laravel.com/docs/9.x/sanctum#revoking-tokens

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

##### 로그 출력
routes/api.php
```php
use Illuminate\Support\Facades\Log;

Log::info('로그 출력');
```

* `storage/logs/laravel.log` 파일에 로그 쌓임

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
Email Testing > Inboxes > My Inbox > SMTP Settings > Integrations > Laravel 7+ > 설정 복사
.env 파일에 설정 적용

# 회원 가입 하면, 인증 이메일 자동 발송 > My Inbox에서 확인
```
