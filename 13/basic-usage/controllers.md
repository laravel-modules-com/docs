---
title: Controllers
order: 6
---

Module controllers live in `Modules/{ModuleName}/app/Http/Controllers/` and are namespaced as `Modules\{ModuleName}\Http\Controllers`.

## Generating Controllers

```bash
php artisan module:make-controller PostController Blog
```

This creates `Modules/Blog/app/Http/Controllers/PostController.php`:

```php
<?php

namespace Modules\Blog\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Routing\Controller;

class PostController extends Controller
{
    public function index()
    {
        return view('blog::index');
    }
}
```

## Controller Options

### Plain Controller

A plain controller with no boilerplate methods:

```bash
php artisan module:make-controller PostController Blog --plain
```

### Resource Controller

Generates a controller with `index`, `create`, `store`, `show`, `edit`, `update`, and `destroy` methods:

```bash
php artisan module:make-controller PostController Blog
```

Then register it in `routes/web.php`:

```php
Route::resource('posts', PostController::class);
```

### API Controller

Generates a controller without `create` and `edit` (those are only needed for HTML forms):

```bash
php artisan module:make-controller PostController Blog --api
```

Register as an API resource:

```php
Route::apiResource('posts', PostController::class);
```

### Invokable Controller

A single-action controller with one `__invoke` method:

```bash
php artisan module:make-controller PublishPostController Blog --invokable
```

Register in routes:

```php
Route::post('/posts/{post}/publish', PublishPostController::class);
```

## A Full Resource Controller Example

```php
<?php

namespace Modules\Blog\Http\Controllers;

use Illuminate\Http\RedirectResponse;
use Illuminate\View\View;
use Illuminate\Routing\Controller;
use Modules\Blog\Http\Requests\StorePostRequest;
use Modules\Blog\Http\Requests\UpdatePostRequest;
use Modules\Blog\Models\Post;

class PostController extends Controller
{
    public function index(): View
    {
        $posts = Post::latest()->paginate(20);

        return view('blog::posts.index', compact('posts'));
    }

    public function create(): View
    {
        return view('blog::posts.create');
    }

    public function store(StorePostRequest $request): RedirectResponse
    {
        Post::create($request->validated());

        return redirect()->route('blog.posts.index')
            ->with('success', 'Post created.');
    }

    public function show(Post $post): View
    {
        return view('blog::posts.show', compact('post'));
    }

    public function edit(Post $post): View
    {
        return view('blog::posts.edit', compact('post'));
    }

    public function update(UpdatePostRequest $request, Post $post): RedirectResponse
    {
        $post->update($request->validated());

        return redirect()->route('blog.posts.show', $post)
            ->with('success', 'Post updated.');
    }

    public function destroy(Post $post): RedirectResponse
    {
        $post->delete();

        return redirect()->route('blog.posts.index')
            ->with('success', 'Post deleted.');
    }
}
```

## Form Requests

Generate a form request for validation:

```bash
php artisan module:make-request StorePostRequest Blog
php artisan module:make-request UpdatePostRequest Blog
```

These are placed in `Modules/Blog/app/Http/Requests/`:

```php
<?php

namespace Modules\Blog\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class StorePostRequest extends FormRequest
{
    public function authorize(): bool
    {
        return true;
    }

    public function rules(): array
    {
        return [
            'title'   => ['required', 'string', 'max:255'],
            'slug'    => ['required', 'string', 'unique:posts,slug'],
            'body'    => ['required', 'string'],
            'published_at' => ['nullable', 'date'],
        ];
    }
}
```

## API Controllers and JSON Responses

For API controllers, return JSON responses using API resources:

```bash
php artisan module:make-controller PostController Blog --api
php artisan module:make-resource PostResource Blog
```

```php
<?php

namespace Modules\Blog\Http\Controllers;

use Illuminate\Http\Resources\Json\AnonymousResourceCollection;
use Illuminate\Routing\Controller;
use Modules\Blog\Http\Resources\PostResource;
use Modules\Blog\Models\Post;

class PostController extends Controller
{
    public function index(): AnonymousResourceCollection
    {
        return PostResource::collection(Post::paginate(20));
    }
}
```

## Middleware on Controllers

Apply middleware in the constructor:

```php
public function __construct()
{
    $this->middleware('auth')->except(['index', 'show']);
    $this->middleware('can:manage-posts')->only(['create', 'store', 'edit', 'update', 'destroy']);
}
```

## Organising Controllers into Subdirectories

You can organise controllers into subdirectories:

```bash
php artisan module:make-controller Api/PostController Blog
```

This creates `Modules/Blog/app/Http/Controllers/Api/PostController.php` with namespace `Modules\Blog\Http\Controllers\Api`.

Register it in routes:

```php
use Modules\Blog\Http\Controllers\Api\PostController;

Route::apiResource('posts', PostController::class);
```
