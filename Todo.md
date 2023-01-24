# Todo List

## API 만들기
```sh
mkdir todo-list
cd todo-list

laravel new api
cd api

# Todo 모델 만들기
php artisan make:model Todo -a
```

### Todo 스키마 만들기
database/migrations/날짜_create_todos_table.php
```php
$table->id();
```
```php
$table->string('title');
$table->longText('content')->nullable();
$table->date('deadline')->nullable();
$table->boolean('isDone')->default(false);
```

```sh
# 모든 테이블 삭제
php artisan db:wipe

php artisan migrate
```

### Mock 데이터 만들기
database/factories/TodoFactory.php
```php
return [
    'title' => $this->faker->text(15),
    'content' => $this->faker->text(100)
];
```

database/seeders/TodoSeeder.php
```diff
- //
```
```php
use App\Models\Todo;

Todo::factory(50)->create();
```

database/seeders/DatabaseSeeder.php
```php
$this->call(TodoSeeder::class);
```

```sh
# Mock 데이터 넣기 (DatabaseSeeder.php 호출)
php artisan db:seed

# 특정 Seeder만 호출
php artisan db:seed --class=TodoSeeder
```

### api/v1 경로 생성
파일 삭제
```diff
- app/Http/Controllers/TodoController.php
```
```sh
# -r은 Resoure를 뜻하고 CRUD를 생성 해준다.
php artisan make:controller API/TodoController -r
```

routes/api.php
```php
Route::prefix(env('APP_VERSION').'/')->group(function () {
    Route::apiResource('todo', \App\Http\Controllers\API\TodoController::class);
});
```

.env
```env
APP_VERSION=v1
```

* `app/Providers/RouteServiceProvider.php` 파일은 `api`와 `web` 설정 파일

