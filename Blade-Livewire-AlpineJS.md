# Blade
* https://laravel.com/docs/10.x/blade
* `Laravel`에 기본 탑재된 템플릿 엔진으로 `Php`에서 `views`쪽에 전달된 값을 `표현`하거나 `if 지시어`를 사용할 수 있게 해준다.
```sh
laravel new laravel_blade_livewire_alpinejs
```
resources/views/components/modal.blade.phpresources/views/components/modal.blade.php
```html
<div>Modal</div>
```

resources/views/components/modal.blade.phpresources/views/welcome.blade.php
```html
<x-modal></x-modal>
```

# Livewire
* https://livewire.laravel.com/docs/quickstart
* 복잡해진 javascript 사용을 대신하기 위해 만들어진 (서버 + 클라이언트) 상태관리 라이브러리.
* 페이지의 상태값을 서버에서 초기화 하고 서버와 통신으로 HTML을 다시 렌더링한다.

# AlpineJS
* https://alpinejs.dev
* React, Vue.js보다 가벼운 클라이언트 상태관리 라이브러리.
* Livewire3은 AlpineJS을 포함하고 있어서 바로 사용 가능.

# Todo List
* [Todo 스키마 만들기](https://github.com/ovdncids/php-curriculum/blob/master/Todo-API.md#todo-스키마-만들기)
* [mock-데이터-만들기](https://github.com/ovdncids/php-curriculum/blob/master/Todo-API.md#mock-데이터-만들기)

## 페이지 만들기
```sh
# Livewire 설치
composer require livewire/livewire

# Livewire 컴포넌트 설치
php artisan make:livewire todos

# 기본 레이아웃 설치
php artisan livewire:layout
```

routes/web.php
```php
use App\Livewire\Todos;

Route::get('/todos', Todos::class);
```

### Markup
resources/views/livewire/todos.blade.php
```html
<div>
    <div>
        <input type="text" />
        <input type="text" />
        <button>Update</button>
        <button>Delete</button>
    </div>
    <hr />
    <button>Create</button>
    <hr />
    <button>Login</button>
</div>
```

### Todo Read
app/Livewire/Todos.php
```php
use Livewire\WithPagination;
use App\Models\Todo;

class Todos extends Component
{
    use WithPagination;

    public function render()
    {
        $todos = Todo::orderBy('id', 'desc')->paginate(10);
        return view('livewire.todos', [
            'todos' => $todos
        ]);
    }
}
```

resources/views/livewire/todos.blade.php
```php
@foreach ($todos as $todo)
<div wire:key="{{ $loop->index }}">
    <input type="text" value="{{ $todo->title }}" />
    <input type="text" value="{{ $todo->content }}" />
    <button>Update</button>
    <button>Delete</button>
</div>
@endforeach
{{ $todos->links() }}
```
* https://github.com/ovdncids/php-curriculum/blob/master/Todo-API.md#컬럼-포맷-변경

### Todo Create
app/Livewire/Todos.php
```php
public $title;
public $content;

public function create()
{
    $todo = [
        'title' => $this->title,
        'content' => $this->content
    ];
    Todo::create($todo);
    return redirect()->to('/todos');
}
```

resources/views/livewire/todos.blade.php
```diff
- <button>Create</button>
```
```php
<form wire:submit="create">
    <input type="text" placeholder="Title" wire:model="title" />
    <input type="text" placeholder="Content" wire:model="content" />
    <button type="submit">Create</button>
</form>
```

#### Add [title] to fillable property to allow mass assignment on [App\Models\Todo].
app/Models/Todo.php
```php
protected $fillable = [
    'title',
    'content',
    'deadline',
    'isDone'
];
```

### Todo Delete
app/Livewire/Todos.php
```php
public function delete(Todo $todo)
{
    $todo->delete();
}
```

resources/views/livewire/todos.blade.php
```diff
- <button>Delete</button>
```
```php
<button wire:click="delete({{ $todo->id }})">Delete</button>
```

### Todo Update
app/Livewire/Todos.php
```php
public function update($id, $title, $content)
{
    Todo::where('id', $id)->update([
        'title' => $title,
        'content' => $content
    ]);
}
```

resources/views/livewire/todos.blade.php
```php
<div
    wire:key="{{ $loop->index }}"
    x-data="{ id: '{{ $todo->id }}', title: '{{ $todo->title }}', content: '{{ $todo->content }}' }"
>
    <input type="text" x-model="title" />
    <input type="text" x-model="content" />
    <button x-on:click="$wire.update(id, title, content)">Update</button>
    <button wire:click="delete({{ $todo->id }})">Delete</button>
</div>
```
