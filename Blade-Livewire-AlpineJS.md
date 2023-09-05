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

# Tailwind
* https://tailwindcss.com/docs/guides/laravel
