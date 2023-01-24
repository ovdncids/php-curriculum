# php-curriculum (Laravel)
* https://laravel.com/docs/9.x
* https://laravel.kr/docs/9.x/installation

## 설치
### Mac
```sh
brew install php
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
