---
title: Facade Methods
order: 1
---

The `Module` facade provides static access to the module repository. Import it at the top of your file:

```php
use Nwidart\Modules\Facades\Module;
```

## Retrieving Modules

### Get All Modules

Returns an array of all modules (enabled and disabled):

```php
$modules = Module::all();
```

### Get All Enabled Modules

```php
$activeModules = Module::allEnabled();
```

### Get All Disabled Modules

```php
$inactiveModules = Module::allDisabled();
```

### Get Ordered Modules

Returns modules sorted by the `priority` field in `module.json`. Lower numbers come first:

```php
$modules = Module::getOrdered();          // ascending (default)
$modules = Module::getOrdered('desc');    // descending
```

### Get Modules as a Collection

```php
$collection = Module::toCollection();
$collection = Module::collections(); // enabled modules only
```

### Filter by Status

Get modules by status (1 = enabled, 0 = disabled):

```php
$enabled  = Module::getByStatus(1);
$disabled = Module::getByStatus(0);
```

### Scan for Modules

Scan the filesystem for modules (re-reads the disk, bypassing any cache):

```php
$modules = Module::scan();
```

## Finding a Module

### Find by Name

Returns the `Module` instance or `null` if not found:

```php
$module = Module::find('blog');
```

### Find or Fail

Returns the `Module` instance or throws `Nwidart\Modules\Exceptions\ModuleNotFoundException`:

```php
$module = Module::findOrFail('blog');
```

### Check Existence

```php
if (Module::has('blog')) {
    // module exists
}
```

## Checking Module Status

```php
Module::isEnabled('Blog');   // bool
Module::isDisabled('Blog');  // bool
```

## Enabling and Disabling

```php
Module::enable('Blog');
Module::disable('Blog');
```

## Counting Modules

```php
$total = Module::count();
```

## Path Methods

```php
Module::getPath();                  // path to the Modules root directory
Module::getModulePath('Blog');      // path to the Blog module directory
Module::assetPath('Blog');          // path to the Blog module's public assets
Module::getAssetsPath();            // path to the modules asset directory
Module::asset('blog:img/logo.png'); // URL to a module asset
```

## CLI Session Helpers

These methods track which module is "in use" for the current CLI session, allowing you to omit the module name on generator commands after running `module:use`:

```php
Module::setUsed('Blog');
Module::getUsedNow();   // returns 'Blog'
Module::getUsedStoragePath(); // path to the .modulesuse session file
```

## Installing and Updating

Install a module from a Composer package name:

```php
Module::install('nwidart/article-module');
```

Update a module's Composer dependencies:

```php
Module::update('Blog');
```

## Delete a Module

Permanently deletes the module directory:

```php
Module::delete('Blog');
```

## Configuration

Read values from the laravel-modules package configuration:

```php
Module::config('composer.vendor');  // reads config/modules.php
```

## Macros

Extend the module repository with custom methods:

```php
Module::macro('enabledCount', function () {
    return count($this->allEnabled());
});

// Call it
$count = Module::enabledCount();
```

## Practical Examples

### Render a Navigation Menu from Enabled Modules

```php
@foreach (Module::allEnabled() as $module)
    <a href="{{ route($module->getLowerName() . '.index') }}">
        {{ $module->getName() }}
    </a>
@endforeach
```

### Check a Module Before Using Its Features

```php
if (Module::isEnabled('Shop')) {
    $cartCount = app(\Modules\Shop\Services\CartService::class)->count();
}
```

### Get Scan Paths

```php
$paths = Module::getScanPaths(); // directories scanned for modules
```
