---
title: Middleware
order: 9
---

Module middleware works exactly like Laravel middleware. You can create middleware scoped to a module and apply it to the module's routes.

## Generating Middleware

```bash
php artisan module:make-middleware EnsurePostIsPublished Blog
```

This creates `Modules/Blog/app/Http/Middleware/EnsurePostIsPublished.php`:

```php
<?php

namespace Modules\Blog\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;

class EnsurePostIsPublished
{
    public function handle(Request $request, Closure $next): Response
    {
        $post = $request->route('post');

        if ($post && ! $post->isPublished()) {
            abort(404);
        }

        return $next($request);
    }
}
```

## Registering Middleware

### As a Route Middleware Alias

Register the middleware in your module's `RouteServiceProvider` so it can be applied by alias in route definitions:

```php
// Modules/Blog/app/Providers/RouteServiceProvider.php

use Modules\Blog\Http\Middleware\EnsurePostIsPublished;

public function boot(): void
{
    $this->app['router']->aliasMiddleware('post.published', EnsurePostIsPublished::class);

    parent::boot();
}
```

Then apply it in your route file:

```php
Route::get('/posts/{post}', [PostController::class, 'show'])
    ->middleware('post.published');
```

### Directly on Routes

You can also reference the middleware class directly without registering an alias:

```php
use Modules\Blog\Http\Middleware\EnsurePostIsPublished;

Route::get('/posts/{post}', [PostController::class, 'show'])
    ->middleware(EnsurePostIsPublished::class);
```

### In the Global Kernel

To apply middleware globally or as part of a middleware group (e.g. `web` or `api`), register it in `app/Http/Kernel.php`:

```php
// app/Http/Kernel.php

protected $middlewareGroups = [
    'web' => [
        // ...
        \Modules\Blog\Http\Middleware\TrackBlogVisits::class,
    ],
];
```

## Middleware on Route Groups

Apply middleware to a group of module routes:

```php
// Modules/Blog/routes/web.php

Route::middleware(['web', 'auth', 'post.published'])
    ->prefix('blog')
    ->name('blog.')
    ->group(function () {
        Route::get('/posts/{post}', [PostController::class, 'show'])->name('posts.show');
    });
```

## Middleware Parameters

Middleware can accept parameters. Define them after the middleware name separated by a colon:

```php
// Modules/Blog/app/Http/Middleware/RequireRole.php

public function handle(Request $request, Closure $next, string $role): Response
{
    if (! $request->user()->hasRole($role)) {
        abort(403);
    }

    return $next($request);
}
```

Register the alias and use it with a parameter:

```php
Route::middleware('role:editor')->group(function () {
    Route::get('/posts/create', [PostController::class, 'create']);
    Route::post('/posts', [PostController::class, 'store']);
});
```

## Terminable Middleware

Middleware can run code after the response has been sent to the browser by implementing the `terminate` method:

```php
public function handle(Request $request, Closure $next): Response
{
    return $next($request);
}

public function terminate(Request $request, Response $response): void
{
    // Log post views after the response has been sent
    if ($request->route('post')) {
        PostView::record($request->route('post'), $request->ip());
    }
}
```
