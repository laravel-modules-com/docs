---
title: Views
order: 7
---

Module views live in `Modules/{ModuleName}/resources/views/` and are referenced using the module's alias as a namespace: `modulealias::view.name`.

## Referencing Module Views

Use the double-colon namespace syntax to load a module's views:

```php
// From a controller
return view('blog::index');
return view('blog::posts.show', compact('post'));
return view('blog::layouts.master');
```

The alias (`blog`) comes from the `alias` field in `module.json`. The rest of the path is relative to `resources/views/` within the module.

## Generated Views

A new module (without `--plain`) generates two views:

**`resources/views/layouts/master.blade.php`** — a basic HTML layout:

```blade
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Blog Module</title>
</head>
<body>
    @yield('content')
</body>
</html>
```

**`resources/views/index.blade.php`** — a simple index view:

```blade
@extends('blog::layouts.master')

@section('content')
    <h1>Hello World</h1>
@endsection
```

## Generating Views

Generate additional views using the artisan command:

```bash
php artisan module:make-view posts.index Blog
php artisan module:make-view posts.show Blog
php artisan module:make-view posts.create Blog
```

This creates files at the corresponding paths inside `resources/views/`.

## Passing Data to Views

Pass data to views as the second argument to `view()`:

```php
return view('blog::posts.index', [
    'posts' => Post::latest()->paginate(20),
    'categories' => Category::all(),
]);
```

Or use `compact()`:

```php
$posts = Post::latest()->paginate(20);
$categories = Category::all();

return view('blog::posts.index', compact('posts', 'categories'));
```

## Extending the Application Layout

Your module views can extend a layout from the main application just as they would from within the main app:

```blade
@extends('layouts.app')

@section('content')
    <div class="container">
        @foreach ($posts as $post)
            <article>
                <h2>{{ $post->title }}</h2>
                <p>{{ $post->excerpt }}</p>
            </article>
        @endforeach
    </div>
@endsection
```

## View Composers

Register view composers in your module's service provider to share data across multiple views:

```php
// Modules/Blog/app/Providers/BlogServiceProvider.php

public function boot(): void
{
    $this->app['view']->composer('blog::*', function ($view) {
        $view->with('categories', Category::all());
    });
}
```

This passes `$categories` to every view in the Blog module.

## Blade Components

### Class-Based Components

Generate a class-based Blade component:

```bash
php artisan module:make-component Alert Blog
```

This creates:
- `Modules/Blog/app/View/Components/Alert.php`
- `Modules/Blog/resources/views/components/alert.blade.php`

Use it in a Blade template:

```blade
<x-blog::alert type="success" :message="$message" />
```

### Anonymous Components

Create a view file directly in `resources/views/components/`:

```bash
php artisan module:make-component-view card Blog
```

Use it in a template:

```blade
<x-blog::card>
    <p>Card content here</p>
</x-blog::card>
```

## Overriding Module Views

You can override any module view by placing a file at the corresponding path inside your application's `resources/views/modules/{alias}/` directory.

For example, to override `Modules/Blog/resources/views/posts/show.blade.php`, create:

```
resources/views/modules/blog/posts/show.blade.php
```

The module's service provider loads views from the application's `resources/views/modules/blog` directory first, before falling back to the module's own views:

```php
$this->loadViewsFrom([
    resource_path('views/modules/blog'),
    __DIR__ . '/../../resources/views',
], 'blog');
```

This means you can customise any module view without modifying the module itself.

## Sharing Views Between Modules

Module views can include views from other modules using their namespace:

```blade
@include('shop::partials.cart-summary')
```

Or extend another module's layout:

```blade
@extends('admin::layouts.dashboard')
```
