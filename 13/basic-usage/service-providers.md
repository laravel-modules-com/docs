---
title: Service Providers
order: 8
---

Every module has a primary service provider that bootstraps the module into the Laravel application. It is registered automatically via the `providers` array in `module.json`.

## The Generated Service Provider

A new module generates `Modules/Blog/app/Providers/BlogServiceProvider.php`:

```php
<?php

namespace Modules\Blog\Providers;

use Illuminate\Support\ServiceProvider;

class BlogServiceProvider extends ServiceProvider
{
    protected string $moduleName = 'Blog';
    protected string $moduleNameLower = 'blog';

    public function boot(): void
    {
        $this->registerCommands();
        $this->registerCommandSchedules();
        $this->registerTranslations();
        $this->registerConfig();
        $this->registerViews();
        $this->loadMigrationsFrom(module_path($this->moduleName, 'database/migrations'));
    }

    public function register(): void
    {
        $this->app->register(RouteServiceProvider::class);
    }

    protected function registerCommands(): void
    {
        $this->commands([]);
    }

    protected function registerCommandSchedules(): void
    {
        // $this->app->booted(function () {
        //     $schedule = $this->app->make(Schedule::class);
        //     $schedule->command('inspire')->hourly();
        // });
    }

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

    protected function registerViews(): void
    {
        $viewPath = resource_path('views/modules/' . $this->moduleNameLower);
        $sourcePath = module_path($this->moduleName, 'resources/views');

        $this->publishes([$sourcePath => $viewPath], ['views', $this->moduleNameLower . '-module-views']);
        $this->loadViewsFrom(array_merge($this->getPublishableViewPaths(), [$sourcePath]), $this->moduleNameLower);

        $componentNamespace = 'Modules\\' . $this->moduleName . '\\View\\Components';
        Blade::componentNamespace($componentNamespace, $this->moduleNameLower);
    }

    public function provides(): array
    {
        return [];
    }

    private function getPublishableViewPaths(): array
    {
        $paths = [];
        foreach (config('view.paths') as $path) {
            if (is_dir($path . '/modules/' . $this->moduleNameLower)) {
                $paths[] = $path . '/modules/' . $this->moduleNameLower;
            }
        }
        return $paths;
    }
}
```

## The `register` Method

Use `register` to bind services into the container or register additional service providers. This runs before the application is fully booted.

```php
public function register(): void
{
    $this->app->register(RouteServiceProvider::class);

    // Bind an interface to an implementation
    $this->app->bind(
        \Modules\Blog\Contracts\PostRepositoryInterface::class,
        \Modules\Blog\Repositories\EloquentPostRepository::class
    );

    // Register a singleton
    $this->app->singleton(PostService::class, function ($app) {
        return new PostService($app->make(PostRepositoryInterface::class));
    });
}
```

## The `boot` Method

Use `boot` to register routes, views, translations, and anything that needs to run after all service providers have been registered.

```php
public function boot(): void
{
    $this->registerTranslations();
    $this->registerConfig();
    $this->registerViews();
    $this->loadMigrationsFrom(module_path($this->moduleName, 'database/migrations'));

    // Register observers
    Post::observe(PostObserver::class);

    // Share data with all views in this module
    view()->composer('blog::*', function ($view) {
        $view->with('currentUser', auth()->user());
    });
}
```

## Generating Additional Service Providers

Generate a second service provider for a module:

```bash
php artisan module:make-provider EventServiceProvider Blog
```

Register it from the primary service provider's `register` method:

```php
public function register(): void
{
    $this->app->register(RouteServiceProvider::class);
    $this->app->register(EventServiceProvider::class);
}
```

## Generating an Event Service Provider

For modules with many events and listeners, generate a dedicated event service provider:

```bash
php artisan module:make-event-provider EventServiceProvider Blog
```

This creates a provider extending `Illuminate\Foundation\Support\Providers\EventServiceProvider`:

```php
<?php

namespace Modules\Blog\Providers;

use Illuminate\Foundation\Support\Providers\EventServiceProvider as ServiceProvider;
use Modules\Blog\Events\PostPublished;
use Modules\Blog\Listeners\NotifySubscribers;

class EventServiceProvider extends ServiceProvider
{
    protected $listen = [
        PostPublished::class => [
            NotifySubscribers::class,
        ],
    ];
}
```

Register it from the main service provider:

```php
public function register(): void
{
    $this->app->register(RouteServiceProvider::class);
    $this->app->register(EventServiceProvider::class);
}
```

## Registering Artisan Commands

Register module commands in the `registerCommands` method:

```php
protected function registerCommands(): void
{
    $this->commands([
        \Modules\Blog\Console\Commands\PublishScheduledPostsCommand::class,
        \Modules\Blog\Console\Commands\PruneOldDraftsCommand::class,
    ]);
}
```

## Scheduling Commands

Schedule commands from within the module using `registerCommandSchedules`:

```php
protected function registerCommandSchedules(): void
{
    $this->app->booted(function () {
        $schedule = $this->app->make(\Illuminate\Console\Scheduling\Schedule::class);
        $schedule->command('blog:publish-scheduled')->everyMinute();
        $schedule->command('blog:prune-drafts')->daily();
    });
}
```

## Dependency Injection in Service Providers

Service providers have full access to the Laravel container. You can type-hint dependencies in the constructor or inject them via the container in `register` and `boot`:

```php
public function boot(): void
{
    $config = $this->app->make('config');

    if ($config->get('blog.cache.enabled')) {
        $this->app->singleton(PostCache::class, function ($app) {
            return new PostCache($app->make('cache.store'));
        });
    }
}
```
