# Todo List

## API 프로젝트 만들기
```sh
mkdir todo_list
cd todo_list

laravel new api
cd api
```

### Todo 스키마 만들기
```sh
php artisan make:model Todo -a
```

database/migrations/{날짜}_create_todos_table.php
```php
$table->id();
```
밑으로
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
```diff
- //
```
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

#### SQLSTATE[42S22]: Column not found: 1054 Unknown column 'updated_at' in 'field list'
app/Models/Todo.php
```php
public $timestamps = false;
```

### api/v1/todo 경로 생성
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

```sh
# route 목록에 api/v1/todo 있는지 확인
php artisan route:list
```

Postman
```sh
Method: GET
URL: http://localhost:8000/api/v1/todo
```
* `GET` 호출은 할 수 있지만 Body에 아무것도 없다.
* `app/Providers/RouteServiceProvider.php` 파일은 `api`와 `web` 설정 파일

### Read (Response JSON 스키마 만들기)
```sh
php artisan make:resource TodoResource
```

app/Http/Resources/TodoResource.php
```diff
- return parent::toArray($request);
```
```php
return [
    'id' => $this->id,
    'title' => $this->title,
    'content' => $this->content,
    'deadline' => ($this->deadline == null) ? 'Free' : date('Y-m-d', strtotime($this->deadline)),
    'isDone' => $this->isDone,
    'updated_at' => $this->updated_at->diffForHumans()
];
```
* `diffForHumans`: 지금부터 몇 분전

app/Http/Controllers/API/TodoController.php
```php
use App\Models\Todo;
use App\Http\Resources\TodoResource;

public function index()
{
    // return TodoResource::collection(Todo::all());
    return TodoResource::collection(Todo::orderBy('id', 'desc')->paginate(10));
}
```

Postman
```sh
Method: GET
URL: http://localhost:8000/api/v1/todo?page=1
```

### Create
```sh
php artisan make:request TodoRequest
```

app/Http/Requests/TodoRequest.php
```php
public function authorize()
{
    return true;
}

public function rules()
{
    return [
        'title' => 'required:max:255',
        'content' => 'max:1000',
        'deadline' => 'date',
        'isDone' => 'required|boolean'
    ];
}
```
* `authorize: true` 인증이 완료된 상태

app/Models/Todo.php
```php
use HasFactory;
protected $fillable = [
    'title',
    'content',
    'deadline',
    'isDone'
];
```

app/Http/Controllers/API/TodoController.php
```php
use App\Http\Requests\TodoRequest;

public function store(TodoRequest $request)
{
    $createTodo = Todo::create($request->all());
    return new TodoResource($createTodo);
}
```

Postman
```sh
Method: POST
URL: http://localhost:8000/api/v1/todo
Headers: Accept application/json
Body: raw JSON
```
```json
{
    "title": "Title",
    "content": "Content",
    "isDone": true
}
```

### Read/{id}
app/Http/Controllers/API/TodoController.php
```php
public function show($id)
{
    if (Todo::where('id', $id)->exists()) {
        return new TodoResource(Todo::find($id));
    } else {
        return response()->json([
            "message" => "Not found"
        ], \Symfony\Component\HttpFoundation\Response::HTTP_NOT_FOUND);
    }
}
```

Postman
```sh
Method: GET
URL: http://localhost:8000/api/v1/todo/1
```

### Delete/{id}
app/Http/Controllers/API/TodoController.php
```php
public function destroy($id)
{
    if (Todo::where('id', $id)->exists()) {
        $deleteTodo = Todo::find($id);
        $deleteTodo->delete();
        return response()->json([
            "message" => "Deleted"
        ]);
    } else {
        return response()->json([
            "message" => "Not found"
        ], \Symfony\Component\HttpFoundation\Response::HTTP_NOT_FOUND);
    }
}
```

Postman
```sh
Method: DELETE
URL: http://localhost:8000/api/v1/todo/1
```

### Update/{id}
app/Http/Controllers/API/TodoController.php
```php
public function update(TodoRequest $request, $id)
{
    if (Todo::where('id', $id)->exists()) {
        $updateTodo = Todo::find($id);
        $updateTodo->update($request->all());
        return new TodoResource($updateTodo);
    } else {
        return response()->json([
            "message" => "Not found"
        ], \Symfony\Component\HttpFoundation\Response::HTTP_NOT_FOUND);
    }
}
```

Postman
```sh
Method: PUT or PATCH
URL: http://localhost:8000/api/v1/todo/2
Headers: Accept application/json
Body: raw JSON
```
```json
{
    "title": "Title",
    "content": "Content",
    "isDone": true
}
```
