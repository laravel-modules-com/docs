---
title: Installation and Setup
order: 4
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

> from v11.0 autoloading `"Modules\\": "modules/",` is no longer required, and should be removed from your composer.json if present.

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

**Important**

on the first installation you will be asked:

```bash
Do you trust "wikimedia/composer-merge-plugin" to execute code and wish to enable it now? (writes "allow-plugins" to composer.json) [y,n,d,?]
```

Answer `y` to allow the plugin to be executed. Otherwise, you will need to manually enable the following to your composer.json:

```json
"config": {
    "allow-plugins": {
        "wikimedia/composer-merge-plugin": true
    }
```

> if `"wikimedia/composer-merge-plugin": false` modules will not be autoloaded.

**Tip: don't forget to run `composer dump-autoload` afterwards**
