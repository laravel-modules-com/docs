---
title: Registering Module Events
order: 8
---

Your module may contain events and event listeners. You can create these classes manually, or with the following artisan commands:

Generate an event:
```bash
php artisan module:make-event BlogPostWasUpdatedEvent Blog
```

Generate an event listener:
```bash
php artisan module:make-listener NotifyAdminOfNewPostListener Blog
```

Once those are create you need to register them in laravel. This can be done in 2 ways:

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
