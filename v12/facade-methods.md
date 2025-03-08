---
title: Facade methods
---

Import facade:

```php
use Nwidart\Modules\Facades\Module;
```

Get all modules.

```php
Module::all();
```

Get all cached modules.

```php
Module::getCached()
```

Get ordered modules. The modules will be ordered by the `priority` key in `module.json` file.

```php
Module::getOrdered();
```

Get scanned modules.

```php
Module::scan();
```

Find a specific module.

```php
Module::find('name');
// OR
Module::get('name');
```

Find a module, if there is one, return the `Module` instance, otherwise throw `Nwidart\Modules\Exeptions\ModuleNotFoundException`.

```php
Module::findOrFail('module-name');
```

Get scanned paths.

```php
Module::getScanPaths();
```

Get all modules as a collection instance.

```php
Module::toCollection();
```

Get modules by the status. 1 for active and 0 for inactive.

```php
Module::getByStatus(1);
```

Check the specified module. If it exists, will return `true`, otherwise `false`.

```php
Module::has('blog');
```

Get all enabled modules.

```php
Module::allEnabled();
```

Check if module is enabled

```php
Module::isEnabled('ModuleName');
```

Get all disabled modules.

```php
Module::allDisabled();
```

Check if module is disabled

```php
Module::isDisabled('ModuleName');
```

Enable module
```php
Module::enable('ModuleName');
```

Disable module
```php
Module::disable('ModuleName');
```

Delete module
```php
Module::delete('ModuleName');
```

Update dependencies for the specified module.

```php
Module::update('hello');
```

Install the specified module by given module name.

```php
Module::install('nwidart/hello');
```

Get count of all modules.

```php
Module::count();
```

Get module root path.

```php
Module::getPath();
```

Get the module's `app` path. If the module doesn't have an `app` folder, the result is the same as `getPath` method.

```php
Module::getAppPath();
```

Register the modules.

```php
Module::register();
```

Boot all available modules.

```php
Module::boot();
```

Get all enabled modules as collection instance.

```php
Module::collections();
```

Get module path from the specified module.

```php
Module::getModulePath('name');
```

Get assets path from the specified module.

```php
Module::assetPath('name');
```

Get config value from this package.

```php
Module::config('composer.vendor');
```

Get used storage path.

```php
Module::getUsedStoragePath();
```

Get used module for cli session.

```php
Module::getUsedNow();
// OR
Module::getUsed();
```

Set used module for cli session.

```php
Module::setUsed('name');
```

Get modules's assets path.

```php
Module::getAssetsPath();
```

Get asset url from specific module.

```php
Module::asset('blog:img/logo.img');
```

Add a macro to the module repository.

```php
Module::macro('hello', function() {
    echo "I'm a macro";
});
```

Call a macro from the module repository.

```php
Module::hello();
```

Get all required modules of a module

```php
Module::getRequirements('module name');
```
