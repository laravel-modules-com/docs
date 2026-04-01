---
title: Seeders and Factories
order: 2
---

Each module can have its own seeders and factories in `database/seeders/` and `database/factories/`.

## Generating Seeders

```bash
php artisan module:make-seed PostSeeder Blog
```

This creates `Modules/Blog/database/seeders/PostSeeder.php`:

```php
<?php

namespace Modules\Blog\Database\Seeders;

use Illuminate\Database\Seeder;
use Modules\Blog\Models\Post;

class PostSeeder extends Seeder
{
    public function run(): void
    {
        Post::factory()->count(50)->create();
    }
}
```

### The Master Database Seeder

When a module is scaffolded, a `BlogDatabaseSeeder.php` is generated. This is the entry-point seeder for the module and should call any other seeders the module needs:

```php
<?php

namespace Modules\Blog\Database\Seeders;

use Illuminate\Database\Seeder;

class BlogDatabaseSeeder extends Seeder
{
    public function run(): void
    {
        $this->call([
            CategorySeeder::class,
            PostSeeder::class,
        ]);
    }
}
```

Generate an explicit master seeder with the `--master` flag:

```bash
php artisan module:make-seed BlogDatabaseSeeder Blog --master
```

## Running Seeders

Run the seeder for a specific module:

```bash
php artisan module:seed Blog
```

This runs the module's master seeder (`BlogDatabaseSeeder`). Run a specific class:

```bash
php artisan module:seed Blog --class=PostSeeder
```

Seed all modules at once:

```bash
php artisan module:seed
```

Seed after migration:

```bash
php artisan module:migrate Blog --seed
php artisan module:migrate-refresh Blog --seed
```

## Generating Factories

```bash
php artisan module:make-factory PostFactory Blog
```

This creates `Modules/Blog/database/factories/PostFactory.php`:

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
            'slug'         => fake()->unique()->slug(),
            'excerpt'      => fake()->paragraph(),
            'body'         => fake()->paragraphs(5, true),
            'status'       => 'published',
            'published_at' => fake()->dateTimeBetween('-1 year', 'now'),
        ];
    }
}
```

## Factory States

Factory states let you create model variations for different test scenarios:

```php
public function definition(): array
{
    return [
        'title'        => fake()->sentence(),
        'slug'         => fake()->unique()->slug(),
        'body'         => fake()->paragraphs(5, true),
        'status'       => 'published',
        'published_at' => now(),
    ];
}

public function draft(): static
{
    return $this->state([
        'status'       => 'draft',
        'published_at' => null,
    ]);
}

public function scheduled(): static
{
    return $this->state([
        'status'       => 'scheduled',
        'published_at' => fake()->dateTimeBetween('+1 day', '+1 month'),
    ]);
}
```

Use states in tests:

```php
// Create a published post
$post = Post::factory()->create();

// Create a draft
$post = Post::factory()->draft()->create();

// Create 10 scheduled posts
$posts = Post::factory()->scheduled()->count(10)->create();
```

## Factory Relationships

Create related models together using `for()` and `has()`:

```php
// Create a post for a specific user
$post = Post::factory()
    ->for($user)
    ->create();

// Create a post with 5 comments
$post = Post::factory()
    ->has(Comment::factory()->count(5), 'comments')
    ->create();
```

Or use `recycle` to reuse an existing model:

```php
$category = Category::factory()->create();

$posts = Post::factory()->count(10)->recycle($category)->create();
```

## Using Factories in Seeders

Factories are designed to be used in seeders for populating a database with realistic data:

```php
<?php

namespace Modules\Blog\Database\Seeders;

use Illuminate\Database\Seeder;
use Modules\Blog\Models\Category;
use Modules\Blog\Models\Post;
use App\Models\User;

class BlogDatabaseSeeder extends Seeder
{
    public function run(): void
    {
        $categories = Category::factory()->count(5)->create();
        $authors = User::factory()->count(3)->create();

        Post::factory()
            ->count(50)
            ->recycle($categories)
            ->recycle($authors)
            ->create();

        Post::factory()
            ->count(10)
            ->draft()
            ->recycle($authors)
            ->create();
    }
}
```

## Calling the Main Application DatabaseSeeder

If you want module seeders to run when the main `php artisan db:seed` is called, add them to your application's `Database\Seeders\DatabaseSeeder`:

```php
<?php

namespace Database\Seeders;

use Illuminate\Database\Seeder;

class DatabaseSeeder extends Seeder
{
    public function run(): void
    {
        $this->call([
            \Modules\Blog\Database\Seeders\BlogDatabaseSeeder::class,
            \Modules\Shop\Database\Seeders\ShopDatabaseSeeder::class,
        ]);
    }
}
```
