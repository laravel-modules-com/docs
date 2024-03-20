---
title: Introduction
---

`nwidart/laravel-modules` is a Laravel package which was created to manage your large Laravel app using modules. A module is like a Laravel package, it has some views, controllers or models. This package is supported and tested in Laravel 11.

### Quick Example

Generate your first module using `php artisan module:make Blog`. The following structure will be generated.

```
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
