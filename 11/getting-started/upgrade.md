---
title: Upgrade
order: 3
---

> Heads up:
    If you upgrade to v6 from the previous version, run the following command: `php artisan module:v6:migrate`

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

## Composer Merge Plugin

The first time you upgrade to v11 you will be asked whether to enable the merge plugin, press y to allow. It's now required for merging composer files from modules.

```
Do you trust "wikimedia/composer-merge-plugin" to execute code and wish to enable it now? (writes "allow-plugins" to composer.json) [y,n,d,?] 
```

> **Tip: run `composer dump-autoload` after any composer changes**

## Composer update

There is a new command `php artisan module:composer-update` to update all module's composer.json files.

This will update autoloading paths for existing modules.

## Config

Please update your `config/modules.php` file the generator paths have been updated as well as using internal paths for commands.

> Please note the new paths only affect new modules / generated files.

The easiest way is to delete the file and re-publish it:

```bash
php artisan vendor:publish --provider="Nwidart\Modules\LaravelModulesServiceProvider" --tag="config"
```

## Autoloading

> from v11.0 autoloading `"Modules\\": "modules/",` is no longer required, and should be removed from your composer.json if present.

Please delete the Modules autoloading section:

```diff
     "autoload": {
         "psr-4": {
             "App\\": "app/",
-            "Modules\\": "modules/", <-- delete this
             "Database\\Factories\\": "database/factories/",
             "Database\\Seeders\\": "database/seeders/"
         }
```

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

Modules composer.json files for newly generated modules will contain:

```json
"autoload": {
    "psr-4": {
        "Modules\\Blog\\": "app/",
        "Modules\\Blog\\Database\\Factories\\": "database/factories/",
        "Modules\\Blog\\Database\\Seeders\\": "database/seeders/"
    }
},
"autoload-dev": {
    "psr-4": {
        "Modules\\Blog\\Tests\\": "tests/"
    }
}
```

This allows all classes to be autoloaded from a new folder called app without requiring `App` to be in the classes' namespaces.

### Existing modules that don't contain an App folder can continue to use their autoloading path:

Autoload all classes to the root of the module.

```json
"autoload": {
    "psr-4": {
        "Modules\\Blog\\": ""
    }
}
```

### Existing modules that do contain an App folder will need to adjust the autoloading path:

Autoload all classes to the root of the module.

Please change `"Modules\\Blog\\": "app/",` to point to the root of the module:

```json
"autoload": {
    "psr-4": {
        "Modules\\Blog\\": "",
        "Modules\\Blog\\Database\\Factories\\": "database/factories/",
        "Modules\\Blog\\Database\\Seeders\\": "database/seeders/"
    }
},
"autoload-dev": {
    "psr-4": {
        "Modules\\Blog\\Tests\\": "tests/"
    }
}
```


## Module Structure

Newly generated modules will now have this structure

```
Modules
    └── Blog
        ├── app
        │   ├── Http
        │   │   └── Controllers
        │   │       └── BlogController.php
        │   ├── Models
        │   └── Providers
        │       ├── BlogServiceProvider.php
        │       └── RouteServiceProvider.php
        ├── config
        │   └── config.php
        ├── database
        │   ├── factories
        │   ├── migrations
        │   └── seeders
        │       └── BlogDatabaseSeeder.php
        ├── resources
        │   ├── assets
        │   │   ├── js
        │   │   │   └── app.js
        │   │   └── sass
        │   │       └── app.scss
        │   └── views
        │       ├── layouts
        │       │   └── master.blade.php
        │       └── index.blade.php
        ├── routes
        │   ├── api.php
        │   └── web.php
        ├── tests
        │   ├── Feature
        │   └── Unit
        ├── composer.json
        ├── module.json
        ├── package.json
        └── vite.config.js
```

This can be changed by editing the generator paths in `config/modules.php`
