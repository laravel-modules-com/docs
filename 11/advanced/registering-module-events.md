---
title: Registering Module Events
order: 8
---

## Module Events

Laravel Modules provides several event constants that help you hook into the lifecycle of a module. These events allow developers to perform specific actions when certain module-related activities occur.

### Available Event Constants

Here’s a list of available event constants, which are defined in the ModuleEvent class:

```php
class ModuleEvent
{
    const BOOT = 'boot';
    const REGISTER = 'register';
    const DISABLING = 'disabling';
    const DISABLED = 'disabled';
    const ENABLING = 'enabling';
    const ENABLED = 'enabled';
    const CREATING = 'creating';
    const CREATED = 'created';
    const DELETING = 'deleting';
    const DELETED = 'deleted';
    const USED = 'used';
    const UNUSED = 'unused';
}
```

**Explanation of Each Event**

**BOOT**
- Event Trigger: This event is fired when a module is booted.
- Use Case: You can use this event to perform additional setup tasks when a module is fully booted into the application lifecycle.

**REGISTER**
- Event Trigger: This event is fired when a module is registered.
- Use Case: Hook into this event to register custom services, routes, or other functionalities when the module is added to the application.

**DISABLING**
- Event Trigger: This event is fired just before a module is disabled.
- Use Case: Use this event to perform any cleanup or save state before the module is disabled.

**DISABLED**
- Event Trigger: This event is fired when a module is disabled.
- Use Case: This can be used to notify other services or take actions after the module has been disabled.

**ENABLING**
- Event Trigger: This event is fired just before a module is enabled.
- Use Case: Hook into this event to initialize or prepare resources before a module becomes active.

**ENABLED**
- Event Trigger: This event is fired when a module is enabled.
- Use Case: Use this event to perform tasks once the module is successfully enabled, such as logging or setting up additional features.

**CREATING**
- Event Trigger: This event is fired before a new module is created.
- Use Case: Perform pre-creation tasks such as validation or preparation before a new module is added.

**CREATED**
- Event Trigger: This event is fired once a new module has been created.
- Use Case: You can hook into this event to carry out tasks like setting up default configurations for the newly created module.

**DELETING**
- Event Trigger: This event is fired just before a module is deleted.
- Use Case: Use this event to handle tasks such as backing up data or removing dependencies before the module is deleted.

**DELETED**
- Event Trigger: This event is fired after a module is deleted.
- Use Case: Hook into this event to remove related data, clean caches, or perform other post-deletion tasks.

**USED**
- Event Trigger: This event is fired when a module is marked as “used.”
- Use Case: Useful for tracking module usage or incrementing statistics related to the use of the module.

**UNUSED**
- Event Trigger: This event is fired when a module is marked as “unused.”
- Use Case: Perform actions like freeing up resources or reducing logs once the module is no longer actively used.


Here is how you can listen for a module event in your Laravel application:

**EventServiceProvider**: First, register your listeners in the EventServiceProvider like any other Laravel event.

```php
use Nwidart\Modules\Constants\ModuleEvent;

class EventServiceProvider extends ServiceProvider
{
    protected $listen = [
        ModuleEvent::BOOT => [
            ModuleBootListener::class,
        ],
    ];
}
```

**Listener Class**: Define the corresponding listener to handle the event logic.

```php
namespace App\Listeners;

class ModuleBootListener
{
    public function handle($event)
    {
        // Your logic when the module boots
    }
}
```

These events allow developers to extend the functionality of Laravel Modules by hooking into key lifecycle events. Using these constants helps create a more modular and event-driven application.

## Registering Module Events

Your module may contain events and event listeners. You can create these classes manually, or with the following artisan commands:

Generate an event:
```bash
php artisan module:make-event BlogPostWasUpdatedEvent Blog
```

Generate an event listener:
```bash
php artisan module:make-listener NotifyAdminOfNewPostListener Blog
```

Once those are created you need to register them. This can be done in 2 ways:

## Manually registering events 

Register events manually in your module service provider register method:

```php
$this->app['events']->listen(BlogPostWasUpdatedEvent::class, NotifyAdminOfNewPostListener::class);
```

## Creating an EventServiceProvider

Once you have multiple events, you might find it easier to have all events and their listeners in a dedicated service provider. This is what the EventServiceProvider is for.

Create a new class called for instance `EventServiceProvider` in the `Modules/Blog/Providers` folder (Blog being an example name).

This class needs to look like this:

```php
<?php

namespace Modules\Blog\App\Providers;

use Illuminate\Foundation\Support\Providers\EventServiceProvider as ServiceProvider;

class EventServiceProvider extends ServiceProvider
{
    protected $listen = [];
}
```

> Don't forget to load this service provider in the `register` method of the `ModuleServiceProvider` class.

```php
$this->app->register(EventServiceProvider::class);
```

This is now like the regular EventServiceProvider in the `app/` namespace. In our example the `listen` property will look like this:

```php
<?php

namespace Modules\Blog\App\Events;

use Illuminate\Foundation\Support\Providers\EventServiceProvider as ServiceProvider;

class EventServiceProvider extends ServiceProvider
{
    protected $listen = [
        BlogPostWasUpdatedEvent::class => [
            NotifyAdminOfNewPostListener::class,
        ],
    ];
}
```
