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
composer create-project laravel/laravel laravel-study
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

