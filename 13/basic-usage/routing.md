---
title: Routing
order: 5
---

Each module gets its own `routes/web.php` and `routes/api.php` files. These are registered automatically by the module's `RouteServiceProvider`.

## Web Routes

`Modules/Blog/routes/web.php` contains standard Laravel web routes. By default, it includes a single example route:

```php
<?php

use Illuminate\Support\Facades\Route;
use Modules\Blog\Http\Controllers\BlogController;

Route::middleware(['web'])
    ->prefix('blog')
    ->name('blog.')
    ->group(function () {
        Route::get('/', [BlogController::class, 'index'])->name('index');
    });
```

The `prefix` groups all routes under `/blog` and `name` prefixes all named routes with `blog.` so you can refer to them as `route('blog.index')`.

## API Routes

`Modules/Blog/routes/api.php` registers routes under the `api` middleware group:

```php
<?php

use Illuminate\Support\Facades\Route;
use Modules\Blog\Http\Controllers\BlogController;

Route::middleware(['api'])
    ->prefix('api/blog')
    ->name('api.blog.')
    ->group(function () {
        Route::apiResource('posts', BlogController::class);
    });
```

## Route Groups and Middleware

Apply middleware to a group of routes exactly as you would in a standard Laravel application:

```php
Route::middleware(['web', 'auth'])
    ->prefix('admin/blog')
    ->name('admin.blog.')
    ->group(function () {
        Route::get('/', [BlogController::class, 'index'])->name('index');
        Route::get('/create', [BlogController::class, 'create'])->name('create');
        Route::post('/', [BlogController::class, 'store'])->name('store');
        Route::get('/{post}', [BlogController::class, 'show'])->name('show');
        Route::get('/{post}/edit', [BlogController::class, 'edit'])->name('edit');
        Route::put('/{post}', [BlogController::class, 'update'])->name('update');
        Route::delete('/{post}', [BlogController::class, 'destroy'])->name('destroy');
    });
```

## Resource Routes

Register a full CRUD resource with a single line:

```php
Route::resource('posts', PostController::class);
```

For API-only resource routes (omits `create` and `edit`):

```php
Route::apiResource('posts', PostController::class);
```

## Route Model Binding

Route model binding works exactly the same as in standard Laravel. Type-hint the model in your controller method and Laravel resolves it automatically:

```php
// routes/web.php
Route::get('/posts/{post}', [PostController::class, 'show']);

// PostController.php
public function show(Post $post): View
{
    return view('blog::posts.show', compact('post'));
}
```

## Named Routes and URL Generation

With the `name('blog.')` prefix on your route group, generate URLs using named routes:

```php
// In controllers or views
route('blog.index')           // /blog
route('blog.posts.show', $post) // /blog/posts/1
```

In Blade templates:

```blade
<a href="{{ route('blog.index') }}">All Posts</a>
```

## Accessing Routes Across Modules

Modules can freely reference each other's named routes. If the `Shop` module defines `route('shop.checkout')`, the `Blog` module can use that route name without any special configuration.

## The RouteServiceProvider

Routes are registered by the `RouteServiceProvider` generated inside your module at `Modules/Blog/app/Providers/RouteServiceProvider.php`. If you need to customise how routes are loaded (e.g. add rate limiting, change the prefix logic, or load additional route files), edit this provider:

```php
<?php

namespace Modules\Blog\Providers;

use Illuminate\Support\Facades\Route;
use Illuminate\Foundation\Support\Providers\RouteServiceProvider as ServiceProvider;

class RouteServiceProvider extends ServiceProvider
{
    protected string $moduleNamespace = 'Modules\Blog\Http\Controllers';

    public function boot(): void
    {
        parent::boot();
    }

    public function map(): void
    {
        $this->mapApiRoutes();
        $this->mapWebRoutes();
    }

    protected function mapWebRoutes(): void
    {
        Route::middleware('web')
            ->namespace($this->moduleNamespace)
            ->group(module_path('Blog', '/routes/web.php'));
    }

    protected function mapApiRoutes(): void
    {
        Route::prefix('api')
            ->middleware('api')
            ->namespace($this->moduleNamespace)
            ->group(module_path('Blog', '/routes/api.php'));
    }
}
```

To regenerate a route service provider for a module:

```bash
php artisan module:route-provider Blog
```
