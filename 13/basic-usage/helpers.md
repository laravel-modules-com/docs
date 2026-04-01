---
title: Helpers
order: 4
---

Laravel Modules provides a set of global helper functions for working with module paths and assets.

## `module_path('Blog')`

Returns the absolute path to a module's directory on disk.

```php
$path = module_path('Blog');
// /var/www/your-app/Modules/Blog
```

Append a relative path as the second argument:

```php
module_path('Blog', 'resources/views')
// /var/www/your-app/Modules/Blog/resources/views

module_path('Blog', 'app/Http/Controllers/PostController.php')
// /var/www/your-app/Modules/Blog/app/Http/Controllers/PostController.php
```

Common usage in service providers:

```php
$this->loadMigrationsFrom(module_path($this->moduleName, 'database/migrations'));
$this->loadViewsFrom(module_path($this->moduleName, 'resources/views'), 'blog');
$this->loadTranslationsFrom(module_path($this->moduleName, 'lang'), 'blog');
```

## `module_vite()`

Generates the HTML `<script>` and `<link>` tags for a module's Vite-compiled assets when modules compile their own assets independently (i.e. each module runs `npm run build` from its own directory).

```php
// In a Blade layout file
{!! module_vite('build-blog', 'resources/assets/sass/app.scss') !!}
{!! module_vite('build-blog', 'resources/assets/js/app.js') !!}
```

The first argument is the build directory name (matches `buildDirectory` in the module's `vite.config.js`). The second argument is the input file path as defined in the Vite config.

> If you are using the centralised approach (all modules compiled from the root `vite.config.js`), use the standard `@vite()` directive instead.

See [Compiling Assets](/docs/13/basic-usage/compiling-assets) for full setup instructions.
