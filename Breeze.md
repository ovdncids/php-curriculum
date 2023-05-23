# Breeze
* Backend API로만 프로젝트를 만들때 사용
* https://laravel.com/docs/10.x/starter-kits#main-content
* Breeze & Next.js / API

## Breeze와 Next.js
* https://github.com/laravel/breeze-next

## 프로젝트 설치
```sh
laravel new laravel-breeze-api
cd laravel-breeze-api
```

## MySQL 연결
```sh
# root 권한으로 로그인
create database laravel_breeze_api;
```
.env
```env
DB_DATABASE=laravel_breeze_api
```

## Breeze & API 설치
```sh
composer require laravel/breeze --dev

php artisan breeze:install -h
php artisan breeze:install api
php artisan migrate
php artisan serve
```

### 회원 API
* `api/register`는 `MVC용`으로 사용되고 있는 `routes/auth.php`에서 `Route::post('/register', ...)` 부분을 복사해 온다.
* `api/login`, `api/logout`은 `jetstream`과 동일

### 회원 생성 API
* POST http://localhost:8000/api/register
```json
{
    "name": "홍길동",
    "email": "a@a.com",
    "password": "password",
    "password_confirmation": "password"
}
```

### CSRF token mismatch.
app/Http/Middleware/VerifyCsrfToken.php
```php
protected $except = [
    '/api/*'
];
```

## Next Frontend
* https://larainfo.com/blogs/laravel-breeze-api-install-setup-authentication-with-nextjs
* 회원 부분은 `MVC` 방식으로 처리한다.
```sh
git clone https://github.com/laravel/breeze-next.git
npm install
cp .env.example .env
npm run dev
```
