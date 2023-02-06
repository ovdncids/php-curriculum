# File Upload
* https://laravel.kr/docs/9.x/filesystem#Public%20%EB%94%94%EC%8A%A4%ED%81%AC
* https://github.com/ovdncids/react-curriculum/blob/master/FileUpload.md

```php
public function store(Request $request)
{
    // storage/app/public/avatars 경로에 파일을 업로드 한다. 파일명은 임의로 만든다.
    // public/avatars 경로가 없으면 자동으로 만든다.
    $path = $request->file('file')->store('public/avatars');
    // asset은 서버경로를 뜻한다. http://localhost:8000/storage/avatars/파일명
    return asset('storage/avatars/' . str_replace('public/avatars/', '', $path));
}
```

## `public/storage`를 `storage/app/public`로 연결
```sh
php artisan storage:link
```
