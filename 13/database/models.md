---
title: Models
order: 1
---

Module models live in `Modules/{ModuleName}/app/Models/` and are namespaced as `Modules\{ModuleName}\Models`.

## Generating Models

```bash
php artisan module:make-model Post Blog
```

This creates `Modules/Blog/app/Models/Post.php`:

```php
<?php

namespace Modules\Blog\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;

class Post extends Model
{
    use HasFactory;

    protected $guarded = [];
}
```

## Generating a Model with Related Files

Generate a model alongside a migration, factory, and seeder in one command:

```bash
php artisan module:make-model Post Blog --migration --factory --seed
```

Or generate everything at once:

```bash
php artisan module:make-model Post Blog --all
```

The `--all` flag generates: migration, factory, seeder, controller, request, resource, and policy.

## Filling Attributes

Define `$fillable` or `$guarded` as usual:

```php
<?php

namespace Modules\Blog\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\SoftDeletes;

class Post extends Model
{
    use HasFactory, SoftDeletes;

    protected $fillable = [
        'user_id',
        'category_id',
        'title',
        'slug',
        'excerpt',
        'body',
        'status',
        'published_at',
    ];

    protected $casts = [
        'published_at' => 'datetime',
    ];
}
```

You can also pass fillable attributes when generating the model:

```bash
php artisan module:make-model Post Blog --fillable="title,slug,body,status"
```

## Relationships

Define relationships exactly as you would in a standard Laravel model. Models from different modules can have relationships with one another by using the fully-qualified class name:

```php
<?php

namespace Modules\Blog\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Illuminate\Database\Eloquent\Relations\HasMany;
use Modules\Blog\Models\Category;
use Modules\Blog\Models\Comment;
use Modules\Comments\Models\Comment;
use App\Models\User;

class Post extends Model
{
    // Relationship to a model in the main app
    public function author(): BelongsTo
    {
        return $this->belongsTo(User::class, 'user_id');
    }

    // Relationship to another model in the same module
    public function category(): BelongsTo
    {
        return $this->belongsTo(Category::class);
    }

    // Relationship to a model in a different module
    public function comments(): HasMany
    {
        return $this->hasMany(Comment::class);
    }
}
```

## Scopes

Define query scopes to encapsulate common query logic:

```php
use Illuminate\Database\Eloquent\Builder;

public function scopePublished(Builder $query): Builder
{
    return $query->where('status', 'published')
        ->whereNotNull('published_at')
        ->where('published_at', '<=', now());
}

public function scopeDraft(Builder $query): Builder
{
    return $query->where('status', 'draft');
}
```

Use them in controllers:

```php
$posts = Post::published()->latest('published_at')->paginate(20);
```

## Generating a Query Scope Class

For reusable query scopes, generate a dedicated scope class:

```bash
php artisan module:make-scope PublishedScope Blog
```

## Casts and Accessors

```php
protected $casts = [
    'published_at' => 'datetime',
    'metadata'     => 'array',
];

// Accessor
public function getExcerptAttribute(): string
{
    return $this->excerpt ?? Str::limit(strip_tags($this->body), 150);
}
```

Or using PHP 8 attribute syntax with Laravel 9+:

```php
use Illuminate\Database\Eloquent\Casts\Attribute;

protected function excerpt(): Attribute
{
    return Attribute::make(
        get: fn () => $this->attributes['excerpt'] ?? Str::limit(strip_tags($this->body), 150),
    );
}
```

## Factories

When a model is generated with `--factory`, the factory class is created in `database/factories/`:

```php
<?php

namespace Modules\Blog\Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;
use Modules\Blog\Models\Post;

class PostFactory extends Factory
{
    protected $model = Post::class;

    public function definition(): array
    {
        return [
            'title'        => fake()->sentence(),
            'slug'         => fake()->slug(),
            'excerpt'      => fake()->paragraph(),
            'body'         => fake()->paragraphs(5, true),
            'status'       => 'published',
            'published_at' => fake()->dateTimeBetween('-1 year', 'now'),
        ];
    }

    public function draft(): static
    {
        return $this->state([
            'status'       => 'draft',
            'published_at' => null,
        ]);
    }
}
```

The `HasFactory` trait on the model automatically resolves the factory from the module's namespace. Use it in tests:

```php
Post::factory()->create();
Post::factory()->draft()->count(5)->create();
```

## Policies

Generate an authorization policy for a model:

```bash
php artisan module:make-policy PostPolicy Blog
```

Register the policy in the module's service provider or in `App\Providers\AuthServiceProvider`:

```php
// In AuthServiceProvider
use Modules\Blog\Models\Post;
use Modules\Blog\Policies\PostPolicy;

protected $policies = [
    Post::class => PostPolicy::class,
];
```

## Observers

Generate a model observer:

```bash
php artisan module:make-observer PostObserver Blog
```

Register it in the module service provider's `boot` method:

```php
use Modules\Blog\Models\Post;
use Modules\Blog\Observers\PostObserver;

public function boot(): void
{
    Post::observe(PostObserver::class);
}
```

## Showing Model Information

Inspect a module model's attributes, relationships, and more with:

```bash
php artisan module:show-model Blog
```
