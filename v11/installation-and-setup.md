---
title: Installation and Setup
---

## Composer

To install through Composer, by run the following command:

```bash
composer require nwidart/laravel-modules
```

The package will automatically register a service provider and alias.

Optionally, publish the package's configuration and publish stubs by running:

```bash
php artisan vendor:publish --provider="Nwidart\Modules\LaravelModulesServiceProvider"
```

To publish only the config:

```bash
php artisan vendor:publish --provider="Nwidart\Modules\LaravelModulesServiceProvider" --tag="config"
```

To publish only the stubs

```bash
php artisan vendor:publish --provider="Nwidart\Modules\LaravelModulesServiceProvider" --tag="stubs"
```

>From V10.0.3
To publish only vite-modules-loader.js

```bash
php artisan vendor:publish --provider="Nwidart\Modules\LaravelModulesServiceProvider" --tag="vite"
```

## Autoloading

By default the module classes are not loaded automatically. You can autoload your modules using `psr-4`. For example :

```json
{
  "autoload": {
    "psr-4": {
      "App\\": "app/",
      "Modules\\": "Modules/",
      "Database\\Factories\\": "database/factories/",
      "Database\\Seeders\\": "database/seeders/"
    }
  }
}
```

**Tip: don't forget to run `composer dump-autoload` afterwards**
