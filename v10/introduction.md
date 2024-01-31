---
title: Introduction
---

`nwidart/laravel-modules` is a Laravel package which was created to manage your large Laravel app using modules. A module is like a Laravel package, it has some views, controllers or models. This package is supported and tested in Laravel 10.

Find out why you should use this package in the article: [Writing modular applications with laravel-modules](https://nicolaswidart.com/blog/writing-modular-applications-with-laravel-modules).

> Heads up:
    If you upgrade to v6 from previous version, run the following command: `php artisan module:v6:migrate`

## Upgrading from v8.3.0

If you have an existing config file, and you get an error:
```bash
Target class [CommandMakeCommand] does not exist
```

replace the commands array with:

```php
 'commands' => \Nwidart\Modules\Providers\ConsoleServiceProvider::defaultCommands()
        ->merge([
            // New commands go here
        ])->toArray(),
```

### Quick Example

Generate your first module using `php artisan module:make Blog`. The following structure will be generated.

```
Modules/
  ├── Blog/
      ├──app
          ├── Http/
          ├── Models/
              ├── Controllers/
              ├── Middleware/
              ├── Requests/
          ├── Providers/
              ├── BlogServiceProvider.php
              ├── RouteServiceProvider.php
     ├── config/
     ├── database/
          ├── factories/
          ├── migrations/
          ├── seeders/
      ├── lang
      ├── resources/
          ├── assets/
          ├── views/
      ├── routes/
          ├── api.php
          ├── web.php
      ├── tests/
      ├── composer.json
      ├── module.json
      ├── package.json
      ├── vite.config.js
```
