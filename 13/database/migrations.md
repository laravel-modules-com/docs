---
title: Migrations
order: 0
---

Each module manages its own database migrations in `Modules/{ModuleName}/database/migrations/`. Migrations are registered automatically by the module's service provider via `loadMigrationsFrom`.

## Generating Migrations

Create a migration for a module using `module:make-migration`:

```bash
php artisan module:make-migration create_posts_table Blog
```

This creates a migration file in `Modules/Blog/database/migrations/`:

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('posts');
    }
};
```

The migration name follows the same conventions as standard Laravel migrations, prefixing with `create_` generates a `Schema::create` stub.

### Generating a Model with a Migration

Generate a model and its migration together:

```bash
php artisan module:make-model Post Blog --migration
```

Or generate the model alongside a migration, factory, seeder, controller, and request all at once:

```bash
php artisan module:make-model Post Blog --all
```

### Add Columns to an Existing Table

```bash
php artisan module:make-migration add_published_at_to_posts_table Blog
```

## Running Migrations

Run all pending migrations for a specific module:

```bash
php artisan module:migrate Blog
```

Run migrations for all modules at once:

```bash
php artisan module:migrate
```

### Additional Options

Specify the database connection:

```bash
php artisan module:migrate Blog --database=pgsql
```

Simulate the migration without executing it:

```bash
php artisan module:migrate Blog --pretend
```

Run seeders after migrating:

```bash
php artisan module:migrate Blog --seed
```

## Rolling Back Migrations

Roll back the last batch of migrations for a module:

```bash
php artisan module:migrate-rollback Blog
```

Roll back a specific migration file using `--subpath`:

```bash
php artisan module:migrate-rollback Blog --subpath="2024_01_15_120000_create_posts_table.php"
```

## Refreshing Migrations

Roll back all migrations for a module and re-run them:

```bash
php artisan module:migrate-refresh Blog
```

Optionally seed after refreshing:

```bash
php artisan module:migrate-refresh Blog --seed
```

## Resetting Migrations

Roll back every migration for a module:

```bash
php artisan module:migrate-reset Blog
```

## Fresh Migration

Drop all tables and re-run all module migrations from scratch:

```bash
php artisan module:migrate-fresh Blog
```

> This will also affect tables created by other modules or your main application if they share the same database.

## Publishing Migrations

Copy a module's migration files to the application's main `database/migrations/` directory:

```bash
php artisan module:publish-migration Blog
```

This is useful when you want to deploy a module's migrations alongside the main application migrations.

## Auto-Discovery of Migrations

By default, module migrations are loaded via `loadMigrationsFrom` in the service provider. If you prefer Laravel's auto-discovery to load them instead (useful for packages), enable it in `config/modules.php`:

```php
'auto_discover' => [
    'migrations' => true,
],
```

## A Complete Migration Example

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->foreignId('user_id')->constrained()->cascadeOnDelete();
            $table->foreignId('category_id')->nullable()->constrained()->nullOnDelete();
            $table->string('title');
            $table->string('slug')->unique();
            $table->text('excerpt')->nullable();
            $table->longText('body');
            $table->string('status')->default('draft');
            $table->timestamp('published_at')->nullable();
            $table->timestamps();
            $table->softDeletes();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('posts');
    }
};
```
