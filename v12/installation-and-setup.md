---
title: Installation and Setup
---

## Composer

To install through Composer, by run the following command:

```bash
composer require nwidart/laravel-modules
```

The package will automatically register a service provider.

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

> from v11.0 autoloading `"Modules\\": "modules/",` is no longer required.

By default, the module classes are not loaded automatically. You can autoload your modules by adding merge-plugin to the extra section:

```json
"extra": {
    "laravel": {
        "dont-discover": []
    },
    "merge-plugin": {
        "include": [
            "Modules/*/composer.json"
        ]
    }
},
```

**Tip: don't forget to run `composer dump-autoload` afterwards**
