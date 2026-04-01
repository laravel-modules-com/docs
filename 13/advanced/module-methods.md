---
title: Module Methods
order: 3
---

Each module is represented by a `Module` instance. Retrieve one using the facade:

```php
use Nwidart\Modules\Facades\Module;

$module = Module::find('blog');
```

> Always check that the module exists before calling instance methods to avoid a `null` error. Use `Module::findOrFail` if you want an exception on a missing module.

## Name Methods

```php
$module->getName();        // "Blog"
$module->getLowerName();   // "blog"
$module->getStudlyName();  // "Blog"
$module->getKebabName();   // "blog" (or "my-blog" for multi-word names)
$module->getSnakeName();   // "blog" (or "my_blog" for multi-word names)

// Casting to string returns the StudlyCase name
(string) $module;          // "Blog"
```

## Description and Metadata

```php
$module->getDescription();   // value of "description" in module.json
$module->getPriority();      // value of "priority" in module.json (int)
```

Read any key from `module.json`:

```php
$module->get('alias');          // "blog"
$module->get('keywords');       // []
$module->get('missing', 'default');  // returns 'default' if key not found
```

Read any key from the module's `composer.json`:

```php
$module->getComposerAttr('name');     // "nwidart/blog"
$module->getComposerAttr('version');
```

## Path Methods

```php
$module->getPath();              // full path to the module directory
$module->getAppPath();           // path to the module's app/ subdirectory
$module->getExtraPath('routes'); // path to module/routes
$module->getExtraPath('resources/views'); // path to module/resources/views
```

`module_path()` helper is equivalent:

```php
module_path('Blog', 'resources/views') // same as $module->getExtraPath('resources/views')
```

## Status Methods

```php
$module->isEnabled();       // true if the module is active
$module->isDisabled();      // true if the module is inactive
$module->isStatus(true);    // true if enabled
$module->isStatus(false);   // true if disabled
```

Chain from the facade if you are confident the module exists:

```php
Module::find('Blog')->isEnabled();
```

Change status:

```php
$module->enable();
$module->disable();
$module->setActive(true);   // enable
$module->setActive(false);  // disable
```

## Lifecycle Methods

```php
$module->register();    // register the module's service providers and aliases
$module->boot();        // boot the module (fire 'boot' event)
```

Fire a module event manually:

```php
$module->fireEvent('custom-event');
```

## Delete a Module

```php
$module->delete();
```

## JSON File Access

Access the parsed contents of any JSON file within the module:

```php
$moduleJson   = $module->json();             // module.json
$composerJson = $module->json('composer.json');
```

## Practical Examples

### Display Module Information

```php
@foreach (Module::allEnabled() as $module)
    <div>
        <strong>{{ $module->getName() }}</strong>
        <span>{{ $module->getDescription() }}</span>
        <code>Priority: {{ $module->getPriority() }}</code>
    </div>
@endforeach
```

### Conditionally Disable a Module

```php
$module = Module::findOrFail('Blog');

if ($someCondition) {
    $module->disable();
    logger()->info("Blog module disabled due to condition.");
}
```

### Get All Module Paths for IDE Indexing

```php
$paths = collect(Module::all())->map(fn ($m) => $m->getPath())->values()->all();
```
