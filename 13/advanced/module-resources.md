---
title: Module Resources
order: 4
---

Modules register their resources (config, views, translations) in their service provider. The generated service provider handles this automatically when a module is scaffolded with `module:make`. This page explains what each registration does and how to customise it.

## Configuration

The `registerConfig` method merges the module's config into the application and optionally makes it publishable:

```php
protected function registerConfig(): void
{
    $this->publishes([
        module_path($this->moduleName, 'config/config.php') => config_path($this->moduleNameLower . '.php'),
    ], 'config');

    $this->mergeConfigFrom(
        module_path($this->moduleName, 'config/config.php'),
        $this->moduleNameLower
    );
}
```

The `mergeConfigFrom` call makes the module's config available immediately at `config('blog.key')` without requiring the user to publish the config. Publishing allows users to override it.

### Multiple Config Files

From v11.1.5, modules support multiple config files. Place additional files alongside `config/config.php`:

```
config/
├── config.php
├── cache.php
└── api.php
```

Register each file in the service provider:

```php
protected function registerConfig(): void
{
    $this->mergeConfigFrom(module_path($this->moduleName, 'config/config.php'), $this->moduleNameLower);
    $this->mergeConfigFrom(module_path($this->moduleName, 'config/cache.php'), $this->moduleNameLower . '.cache');
    $this->mergeConfigFrom(module_path($this->moduleName, 'config/api.php'), $this->moduleNameLower . '.api');
}
```

Access with: `config('blog.cache.ttl')` or `config('blog.api.key')`.

Nested config directories are also supported:

```
config/
├── admin/
│   └── settings.php
└── config.php
```

Access: `config('blog.admin.settings.value')`

## Views

The `registerViews` method loads views and allows them to be published for application-level overrides:

```php
protected function registerViews(): void
{
    $viewPath = resource_path('views/modules/' . $this->moduleNameLower);
    $sourcePath = module_path($this->moduleName, 'resources/views');

    $this->publishes([
        $sourcePath => $viewPath,
    ], ['views', $this->moduleNameLower . '-module-views']);

    $this->loadViewsFrom(
        array_merge($this->getPublishableViewPaths(), [$sourcePath]),
        $this->moduleNameLower
    );

    $componentNamespace = 'Modules\\' . $this->moduleName . '\\View\\Components';
    Blade::componentNamespace($componentNamespace, $this->moduleNameLower);
}
```

The key behaviour here is that `loadViewsFrom` checks `resources/views/modules/blog` first, then falls back to the module's own views. This means any view can be overridden at the application level without modifying the module.

Publish a module's views:

```bash
php artisan vendor:publish --tag=blog-module-views
```

## Translations

The `registerTranslations` method loads translation files from the module or from a published override path:

```php
protected function registerTranslations(): void
{
    $langPath = resource_path('lang/modules/' . $this->moduleNameLower);

    if (is_dir($langPath)) {
        $this->loadTranslationsFrom($langPath, $this->moduleNameLower);
        $this->loadJsonTranslationsFrom($langPath);
    } else {
        $this->loadTranslationsFrom(module_path($this->moduleName, 'lang'), $this->moduleNameLower);
        $this->loadJsonTranslationsFrom(module_path($this->moduleName, 'lang'));
    }
}
```

Publish translation files:

```bash
php artisan module:publish-translation Blog
```

After publishing, translations in `resources/lang/modules/blog/` take precedence over the module's own `lang/` directory.

## Migrations

Migrations are loaded in the `boot` method using `loadMigrationsFrom`:

```php
public function boot(): void
{
    $this->loadMigrationsFrom(module_path($this->moduleName, 'database/migrations'));
}
```

This means `php artisan migrate` automatically picks up module migrations without any extra configuration.

Publish migration files to the application's main migrations folder:

```bash
php artisan module:publish-migration Blog
```

## Assets

Publish a module's public assets to `public/modules/{module}`:

```php
$this->publishes([
    module_path($this->moduleName, 'resources/assets') => public_path('modules/' . $this->moduleNameLower),
], 'public');
```

```bash
php artisan module:publish Blog
```

## Publishable Group Tags

Use tags to allow selective publishing:

```php
$this->publishes([
    module_path($this->moduleName, 'config/config.php') => config_path('blog.php'),
], 'blog-config');

$this->publishes([
    module_path($this->moduleName, 'resources/views') => resource_path('views/modules/blog'),
], 'blog-views');
```

Users can then publish only what they need:

```bash
php artisan vendor:publish --tag=blog-config
php artisan vendor:publish --tag=blog-views
```
