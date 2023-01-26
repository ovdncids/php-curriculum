# Breeze
* Backend API로만 프로젝트를 만들때 사용
* https://laravel.com/docs/9.x/starter-kits#main-content
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

## Breeze 설치
```sh
composer require laravel/breeze --dev

php artisan breeze:install -h
php artisan breeze:install api
php artisan migrate
php artisan serve
```
