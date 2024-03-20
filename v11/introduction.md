---
title: Introduction
---

`nwidart/laravel-modules` is a Laravel package which was created to manage your large Laravel app using modules. A module is like a Laravel package, it has some views, controllers or models. This package is supported and tested in Laravel 11.

### Quick Example

Generate your first module using `php artisan module:make Blog`. The following structure will be generated.

```
Modules/
  ├── Blog/
      ├──app
          ├── Http/
          ├── Models/
              ├── Controllers/
          ├── Providers/
              ├── BlogServiceProvider.php
              ├── RouteServiceProvider.php
     ├── config/
     ├── database/
          ├── factories/
          ├── migrations/
          ├── seeders/
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
