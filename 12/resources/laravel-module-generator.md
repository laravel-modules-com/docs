---
title: Dcblogdev's Module Generator
order: 2
---

Laravel package for generating Laravel Modules from a template.

[https://github.com/dcblogdev/laravel-module-generator](https://github.com/dcblogdev/laravel-module-generator)

## Install

You can install the package via composer:

```bash
composer require dcblogdev/laravel-module-generator
```

Publish both the `config` and `stubs`:

```bash
php artisan vendor:publish --provider="Dcblogdev\ModuleGenerator\ModuleGeneratorServiceProvider"
```

This will publish a `module-generator.php` config file

This contains:
```php
'template' => [
    'Breeze - Blade - CRUD Web & API' => 'stubs/module-generator/breeze-crud-full',
    'Breeze - Blade - CRUD Web only' => 'stubs/module-generator/breeze-crud-web',
    'Breeze - Blade - CRUD API only' => 'stubs/module-generator/breeze-crud-api'
],
'ignore_files' => ['module.json']
```
By default, the stubs will be located at stubs/module-generator you can add your paths by adding folders and updating the config file.

## Usage

```bash
php artisan module:build
```

<img width="737" alt="300550938-529c214d-a02a-4577-8904-c865b2f41f7e" src="https://github.com/dcblogdev/laravel-module-generator/assets/1018170/82c0828f-b9d6-4eff-b7ca-908b46fe37e7">

{module?} is the name of the module you want to create. If you don't provide a name you will be asked to enter one.

{template?} is the name of the template you want to use. If you don't provide a name you will be asked to enter one.

```bash
php artisan module:build Contacts "Breeze - CRUD API only"
```

Once a module has been created, enable it:

```bash
php artisan module:enable ModuleName
```

Then run:

```bash
composer dump-autoload
```

Create or update the stubs file. The filename and contents should have placeholders for example `ModulesController` will be replaced with your name + Controller. ie `ContactsController` when the command is executed.

## Placeholders:

These placeholders are replaced with the name provided when running `php artisan module:build`

### Used in filenames:

`Module` = Module name ie `Contacts`

`module` = Module name in lowercase ie `contacts`

`module_plural` = Plural module name in lowercase ie demo becomes `demos`

`Model` = Model name ie `Contact`

`model` = Model name in lowercase ie `contact`

### Only used inside files:

`{Module}` = Module name ie `PurchaseOrders`

`{module}` = Module name in lowercase ie `purchaseOrder`

`{module_}` = module name with underscores ie `purchase_orders`

`module_plural` = Plural module name in lowercase ie demo becomes `demos`

`{module-}` = module name with hyphens ie `purchase-orders`

`{module }` = module name puts space between capital letters ie `PurchaseOrders` becomes `Purchase Orders`

`{Model}` = Model name ie `PurchaseOrder`

`{model}` = Model name in lowercase ie `purchaseOrder`

`{model_}` = model name with underscores ie `purchase_orders`

`{model-}` = model name with hyphens ie `purchase-orders`

`{model }` = model name puts space between capital letters ie `PurchaseOrder` becomes `Purchase Order`
