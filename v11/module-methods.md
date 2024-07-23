---
title: Module Methods
---

Get an entity from a specific module.

```php
$module = Module::find('blog');
```

Get module name.

```php
$module->getName();
```

Get module name in lowercase.

```php
$module->getLowerName();
```

Get module name in studlycase.

```php
$module->getStudlyName();
```

Get name in snake case.

```php
$module->getSnakeName();
```

Get module description.

```php
$module->getDescription();
```

Get module priority.

```php
$module->getPriority();
```

Get a specific data from `module.json` file by given the key.

```php
$module->get('description');
```

Get a specific data from `composer.json` file by given the key.

```php
$module->getComposerAttr('name');
```

Get module path.

```php
$module->getPath();
```

Get extra path.

```php
$module->getExtraPath('Assets');
```

Disable the specified module.

```php
$module->disable();
```

Enable the specified module.

```php
$module->enable();
```

Check if enabled:

```php
$module->isEnabled();
```

Check if disabled:

```php
$module->isDisabled();
```

Check if the status it true or false by passing true or false:

```php
$module->IsStatus(false); //returns true if active false if is not active
```

You can also do a call in one go:

```php
Module::find('Posts')->isEnabled();
```

**Only run the one liner if you've first checked the module exists otherwise you will get an error.**


Delete the specified module.

```php
$module->delete();
```

Set active state for current module.

```php
$module->setActive(false);
```

Get an array of module requirements. Note: these should be aliases of the module.

```php
$module->getRequires();
```
