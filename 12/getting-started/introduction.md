---
title: Introduction
order: 0
---

`nwidart/laravel-modules` is a robust Laravel package designed to manage large-scale applications through a modular architecture.
A module functions similarly to a standalone Laravel package, encapsulating its own Models, Views, and Controllers.

> This package is fully supported and has been rigorously tested for compatibility with Laravel 12.

### Quick Example

#### Create Your First Laravel Module
To generate a new module, use the core generator command. This creates a self-contained directory structure with all the necessary boilerplate.

To generate a **User** module, use the following command.

#### Command
```shell
php artisan module:make User
```

The generated structure includes:

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

### Guidelines
When generating a Laravel module, following a consistent set of standards ensures your application remains scalable and easy to maintain.

Here are the essential guidelines for generating and structuring a new module:

---

### 1. PascalCase Naming Convention

Always use **PascalCase** for your module names. This ensures that the generated namespaces (e.g., `Modules\Blog\Http\Controllers`) remain PSR-4 compliant and consistent with Laravel's core naming conventions.

* **Correct:** `php artisan module:make OrderManager`
* **Incorrect:** `php artisan module:make order_manager`

### 2. Isolate Module Logic

The primary goal of a module is **encapsulation**. Avoid reaching into the `app/` folder for core logic.

* Keep **Models** inside the `Models/` folder of the module.
* Define **Routes** specifically within the module's `routes/` directory.
* Use **Module-specific Configs** to avoid bloating the main `config/` folder.

### 3. Maintain Slim Controllers

Just like in standard Laravel development, keep your module controllers thin. Move complex business logic into **Services** or **Action** classes within the module folder (e.g., `Modules/Blog/App/Services/PostService.php`).

### 4. Database & Migration Management

When generating migrations for a module, ensure they are kept within the module's `database/migrations` path. This allows you to run migrations specifically for that module:

### 5. Use Module-Aware Generators

Avoid using the standard `make:controller` command, as it will place files in the root `app/` directory. Always use the module-aware version:

* **Standard:** `php artisan make:controller BlogController` (Wrong)
* **Module-Aware:** `php artisan module:make-controller BlogController Blog` (Correct)
