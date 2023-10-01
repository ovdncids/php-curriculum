# php-curriculum (Laravel)
* https://laravel.com/docs/10.x
* https://laravel.kr/docs/10.x/installation

## 설치
### Mac
```sh
brew install php
php -v
# php 8.x 버전이 아니면 터미널 재시작 (현재 8.2.6)

# composer (현재 2.5.5)
brew install composer
```
```sh
vi ~/.zshrc

# composer
export PATH="$HOME/.composer/vendor/bin:$PATH"
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
* [vcruntime140.dll' 14.0 is not compatible with this PHP build linked with 14.16 in Unknown on line 0](https://phodobit.kr/43)

### VSCode 확장 프로그램
```sh
# PHP 라이브러리에 접근 가능
PHP Intelephense

# Laravel 라이브러리에 접근 가능
Laravel Extra Intellisense
```

### 프로젝트 생성
```sh
# laravel/installer (현재 4.5.0)
composer global require laravel/installer

# laravel (현재 10.2.1)
laravel new laravel_study

# 프로젝트 실행
cd laravel_study
code .
php artisan serve

# route 목록
php artisan route:list
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

## 오류들
### Class "Laravel\Fortify\Features" not found
```sh
# git 이동등으로 라이브러리가 변경 되는 경우 vender 폴더에 있는 라이브러리를 초기화 한다.
composer install
```

### 'laravel_study.sessions' doesn't exist
.env
```diff
- SESSION_DRIVER=database
SESSION_DRIVER=file
```

## 로그 출력
routes/api.php
```php
use Illuminate\Support\Facades\Log;

Log::info('로그 출력');
```

## 서버 로그 출력
```php
error_log('서버 로그 출력');
```

* `storage/logs/laravel.log` 파일에 로그 쌓임

## Tailwind
* https://tailwindcss.com/docs/guides/laravel
