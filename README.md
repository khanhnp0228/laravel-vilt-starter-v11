# VILT Stack Guide

VILT: Vue/Inertia/Laravel/Tailwind

## 1. Install Laravel project

Ref: https://laravel.com/docs/11.x#creating-a-laravel-project

```sh
composer create-project laravel/laravel:11 <project name>
```

## 2. Install & config Vitejs

Ref: https://laravel.com/docs/11.x/vite#vue

- Required node.js

- Vite and Laravel Plugin installed as default

- Install Vue plugin for Vite

```sh
npm install --save-dev @vitejs/plugin-vue
```

- Config Vue plugin for Vite to use

```js
//vite.config.js

import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

//Import vue plugin
import vue from '@vitejs/plugin-vue';

export default defineConfig({
    plugins: [
        laravel({
            input: ['resources/css/app.css', 'resources/js/app.js'],
            refresh: true,
        }),
    ],
    vue({
        template: {

        }
    })
});

```

## 3. Setup Inertia for Server-side

Ref: https://inertiajs.com/server-side-setup

Install package `inertia-laravel`

```sh
composer require inertiajs/inertia-laravel
```

Create `reources/views/app.blade.php`

```php
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    @vite('resoures/js/app.js')
    @inertiaHead
</head>
<body>
    @inertia
</body>
</html>

```

Install Inertia's middleware

```sh
php artisan inertia:middleware
```

Append `HandleInertialRequests` to `bootstrap/app.php`

```php
use App\Http\Middleware\HandleInertiaRequests;

->withMiddleware(function (Middleware $middleware) {
    $middleware->web(append: [
        HandleInertiaRequests::class,
    ]);
})
```

## 4. Setup Inertia for Client-side

Ref: https://inertiajs.com/client-side-setup

Install Intertia Client-side adapter

```sh
npm install @inertiajs/vue3
```

Initialize the Inertia app. Replace whole code bellow to `resources/js/app.js`

```js
import { createApp, h } from 'vue'
import { createInertiaApp } from '@inertiajs/vue3'

createInertiaApp({
  resolve: name => {
    const pages = import.meta.glob('./Pages/**/*.vue', { eager: true })
    return pages[`./Pages/${name}.vue`]
  },
  setup({ el, App, props, plugin }) {
    createApp({ render: () => h(App, props) })
      .use(plugin)
      .mount(el)
  },
})
```

Make new dir name `resources/js/Pages` to store pages view. Because inertia will look and find the `*.vue` file in `'./Pages/**/'` to read as view.

## 5. Load Vue (View) to browser

Create some example vue file `resources/js/Pages/Index/Index.vue`

```vue
<template>
    Hello World! <br>
    Welcome to Laravel VILT Stack Development <br>
    <hr>
    Reactive state for counter check: {{ counter }}
</template>

<script setup>
import { ref } from 'vue';

const counter = ref(0);

setInterval(() => counter.value++, 1000);
</script>
```

Try to replace Laravel's classic view 

```php
/* routes/web.php Laravel's default view */

Route::get('/', function () {
    return view('welcome');
});
```

With new Vue by inerta 

```php
/* routes/web.php Laravel's Vue view by inertia */

Route::get('/', function () {
    return inertia('Index/Index');
});

```

## 5. Run both server Larave and Vite

Run Laravel as Back-end server

```sh
php artisan serve --host=0.0.0.0
```

Run Vite as Front-end server

```sh
npm run dev --host
```
