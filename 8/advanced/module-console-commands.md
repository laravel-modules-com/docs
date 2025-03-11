---
title: Module Console Commands
order: 2
---

> Module names should not be typed in UPPERCASE but CamelCase. ie instead of SERIALCODE type SerialCode

Your module may contain console commands. You can generate these commands manually, or with the following helper:

```bash
php artisan module:make-command CreatePostCommand Blog
```

This will create a `CreatePostCommand` inside the Blog module. By default this will be `Modules/Blog/Console/CreatePostCommand`.

Please refer to the [laravel documentation on artisan commands](https://laravel.com/docs/5.8/artisan) to learn all about them.

## Registering the command

You can register the command with the laravel method called `commands` that is available inside a service provider class.

```php
$this->commands([
    \Modules\Blog\Console\CreatePostCommand::class,
]);
```

You can now access your command via `php artisan` in the console.
