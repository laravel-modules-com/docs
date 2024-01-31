---
title: Creating a module
---

To make modules use the artisan command `php artisan module:make ModuleName` to create a module called Posts:

```bash 
php artisan module:make posts
```

This will create a module in the path `Modules/Posts`

You can create multiple modules in one command by specifying the names separately:

```bash
php artisan module:make customers contacts users invoices quotes
```

Which would create each module.

## Flags

By default when you create a new module, the command will add some resources like a controller, seed class, service provider, etc. automatically. If you don't want these, you can add `--plain` flag, to generate a plain module.

```bash
php artisan module:make Blog --plain
```

or

```bash
php artisan module:make Blog -p
```

Additional flags are as follows:

Generate an api module.

```bash
php artisan module:make Blog --api
```

Do not enable the module at creation.

```bash
php artisan module:make Blog --disabled
```

or

```bash
php artisan module:make Blog -d
```

## Naming convention

Because we are autoloading the modules using [psr-4](http://www.php-fig.org/psr/psr-4/), we strongly recommend using StudlyCase convention.

## Folder structure


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

## Composer.json

Each module has its own composer.json file, this sets the name of the module, its description and author. You normally only need to change this file if you need to change the vendor name or have its own composer dependencies. 

For instance say you wanted to install a package into this module: 

```php
"require": {
    "dcblogdev/laravel-box": "^2.0"
}
```

This would require the package for this module, but it won't be loaded for the main Laravel composer.json file. For that you would have to put the dependency in the Laravel composer.json file. The main reason this exists is for when extracting a module to a package.

## Module.json

This file details the name alias and description / options:

```php
{
    "name": "Blog",
    "alias": "blog",
    "description": "",
    "keywords": [],
    "priority": 0,
    "providers": [
        "Modules\\Blog\\Providers\\BlogServiceProvider"
    ],
    "aliases": {},
    "files": [],
    "requires": []
}
```

Modules are loaded in the priority order, change the priority number to have modules booted / seeded in a custom order.

The files option can be used to include files:

```php
"files": [
  "start.php"
]
```

