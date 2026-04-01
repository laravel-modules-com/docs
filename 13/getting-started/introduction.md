---
title: Introduction
order: 0
---

`nwidart/laravel-modules` is a Laravel package for building modular applications. Instead of dumping all your controllers, models, and views into the default Laravel directories, you organise related code into self-contained modules, each with its own routes, controllers, models, views, migrations, and tests.

## Why Use Modules?

As a Laravel application grows, the default directory structure can become difficult to navigate. A single `app/Http/Controllers` folder containing hundreds of controllers belonging to dozens of unrelated features is hard to work with, hard to test, and hard to hand to a new developer.

Modules solve this by grouping everything related to a feature together:

```
Modules/
├── Blog/       ← all blog-related code lives here
├── Shop/       ← all shop-related code lives here
└── Users/      ← all user-management code lives here
```

Benefits include:

- **Clarity** — a developer can find everything related to a feature in one place
- **Separation** — modules can be enabled or disabled independently
- **Reusability** — a module can be extracted into a Composer package and shared across projects
- **Team-friendliness** — different teams can own different modules with minimal merge conflicts

## How It Works

Each module is a mini Laravel application with a predictable structure. The package registers each module's service provider automatically, so routes, migrations, views, translations, and commands are all discovered without manual configuration.

Modules are stored in a top-level `Modules/` directory by default:

```
Modules/
└── Blog/
    ├── app/
    │   ├── Http/
    │   │   └── Controllers/
    │   │       └── BlogController.php
    │   ├── Models/
    │   └── Providers/
    │       ├── BlogServiceProvider.php
    │       └── RouteServiceProvider.php
    ├── config/
    │   └── config.php
    ├── database/
    │   ├── factories/
    │   ├── migrations/
    │   └── seeders/
    │       └── BlogDatabaseSeeder.php
    ├── resources/
    │   ├── assets/
    │   └── views/
    ├── routes/
    │   ├── api.php
    │   └── web.php
    ├── tests/
    │   ├── Feature/
    │   └── Unit/
    ├── composer.json
    ├── module.json
    └── vite.config.js
```

## Quick Start

Install the package:

```bash
composer require nwidart/laravel-modules
```

Generate your first module:

```bash
php artisan module:make Blog
```

Enable the module and run its migrations:

```bash
php artisan module:enable Blog
php artisan module:migrate Blog
```

Visit `/blog` in your browser — the default web route and controller are ready to go.

## What This Package is Not

Laravel Modules is not a micro-services framework and does not enforce strict boundaries between modules. Modules within the same application can freely use each other's models and services. If you need hard isolation between modules (separate deployments, separate databases), that requires additional architectural decisions beyond what this package provides.

## Next Steps

- [Requirements](/docs/13/getting-started/requirements) — PHP and Laravel versions
- [Installation and Setup](/docs/13/getting-started/installation-and-setup) — step-by-step setup guide
- [Creating a Module](/docs/13/basic-usage/creating-a-module) — creating and understanding your first module
