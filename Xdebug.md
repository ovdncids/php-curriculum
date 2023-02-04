# Xdebug
* https://xdebug.org
* https://medium.com/@alirazalilani/debugging-php-laravel-with-visual-studio-code-37b756fb6c19

## phpinfo() 보기
routes/web.php
```php
Route::get('/', function () {
    phpinfo();
});
```

* `phpinfo()`에서 받은 모든 정보를 `https://xdebug.org/wizard.php`에 복사
* 해당 사이트에서 지시하는 내용 모두 실행
* `/usr/local/lib/php/pecl/20220829` 폴더는 생성 해야함
* `phpinfo()`에서 `Xdebug` 보이면 완료

## Xcode 설정
* `PHP Debug` 확장 설치
* `PHP Debug` 설명에 명시된 `php.ini` 설정 추가
```ini
; Xdebug
xdebug.mode = debug
xdebug.start_with_request = yes
```

## Xcode 실행 및 디버그
* Xcode 실행 및 디버그에서 `launch.json` 생성
* 터미널에서 `php artisan serve` (Javascript Debug Terminal 아님)
* 디버그 하고 싶은 라인에서 `break point` 찍기
* 확인
