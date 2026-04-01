---
title: Installation and Setup
order: 4
---

## Install via Composer

```bash
composer require nwidart/laravel-modules
```

The package registers its service provider automatically via Laravel's package discovery.

## Publish Configuration

Publish the package configuration file to `config/modules.php`:

```bash
php artisan vendor:publish --provider="Nwidart\Modules\LaravelModulesServiceProvider" --tag="config"
```

Publish the module stubs (optional, for customisation):

```bash
php artisan vendor:publish --provider="Nwidart\Modules\LaravelModulesServiceProvider" --tag="stubs"
```

Publish the Vite module loader helper:

```bash
php artisan vendor:publish --provider="Nwidart\Modules\LaravelModulesServiceProvider" --tag="vite"
```

Or publish everything at once:

```bash
php artisan vendor:publish --provider="Nwidart\Modules\LaravelModulesServiceProvider"
```

## Autoloading

From v11.0 onwards, adding `"Modules\\": "Modules/"` to your `composer.json` autoload section is **no longer required** and should be removed if present.

Modules are autoloaded by their own `composer.json` files using the `wikimedia/composer-merge-plugin`. Add the following to the `extra` section of your project's `composer.json`:

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
}
```

On the first installation, you will be prompted:

```
Do you trust "wikimedia/composer-merge-plugin" to execute code and wish to enable it now? (writes "allow-plugins" to composer.json) [y,n,d,?]
```

Answer `y`. If you miss this prompt, add the following to your `composer.json` manually:

```json
"config": {
    "allow-plugins": {
        "wikimedia/composer-merge-plugin": true
    }
}
```

> If `wikimedia/composer-merge-plugin` is set to `false` in `allow-plugins`, module classes will not be autoloaded.

After updating `composer.json`, regenerate the autoloader:

```bash
composer dump-autoload
```

## Verify Installation

List all modules to confirm the package is working:

```bash
php artisan module:list
```

With no modules created yet, this will output an empty table. Create your first module to verify end-to-end:

```bash
php artisan module:make Blog
php artisan module:enable Blog
```

Run `php artisan module:list` again and you should see the `Blog` module listed as enabled.

## Module Status File

Module enable/disable state is tracked in `modules_statuses.json` at the project root. This file is created automatically the first time you enable or disable a module:

```json
{
    "Blog": true
}
```

Commit this file to version control so that your team shares the same module activation state across environments. You can also set a module to be disabled by default when creating it:

```bash
php artisan module:make Blog --disabled
```
