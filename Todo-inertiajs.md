# Todo inertiajs

routes/web.php
```php
use Illuminate\Support\Facades\Log;
use App\Http\Resources\TodoResource;
use App\Models\Todo;

Route::get('/todo', function () {
    $todoList = TodoResource::collection(Todo::orderBy('id', 'desc')->paginate(10));
    Log::info(json_encode($todoList));
    return Inertia::render('Todo', [
        'todoList' => $todoList
    ]);
});
```

resources/js/Pages/Todo.vue
```vue
<script setup>
import { router } from '@inertiajs/vue3';

defineProps({
    todoList: Object
});
const create = () => {
    axios.post('/api/v1/todo', {
        "title": "Title",
        "content": "Content",
        "isDone": true
    }).then((response) => {
        router.reload();
    });
};
const del = (id) => {
  axios.delete(`/api/v1/todo/${id}`).then((response) => {
      router.reload();
  });
}
const update = (todo) => {
  axios.patch(`/api/v1/todo/${todo.id}`, {
    "title": todo.title,
    "content": todo.content,
    "isDone": true
}).then((response) => {
      router.reload();
  });
}
</script>

<template>
    <div v-for="(todo, index) in todoList.data" :key="index">
        <input type="text" v-model="todo.title" />
        <button @click="update(todo)">Update</button>
        <button @click="del(todo.id)">Delete</button>
    </div>
    <hr />
    <button @click="create()">Create</button>
    <hr />
    <button @click="router.get('/login')">Login</button>
</template>
```

```sh
npm run build
```
