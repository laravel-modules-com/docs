---
title: Module Console Commands
---

> Module names should not be typed in UPPERCASE but CamelCase. ie instead of SERIALCODE type SerialCode

Your module may contain console commands. You can make these commands manually, or generate them with the following command:

```bash
php artisan module:make-command CommandName ModuleName
```

ie

```bash
php artisan module:make-command ImportTicketsCommand Tickets
```

This will create an `ImportTicketsCommand` command inside the `Tickets` module located at `Modules/Tickets/Console/ImportTicketsCommand`.

Please refer to the [laravel documentation on artisan commands](https://laravel.com/docs/9.x/artisan) to learn all about them.

## Registering commands

In order to use custom module commands, they first need to be registered inside the module service providers `boot` method:

```php
$this->commands([
    \Modules\Tickets\Console\ImportTicketsCommand::class,
]);
```

You can now access your command via `php artisan` in the console.

## Schedule commands

To use the Artisan's scheduler 

Import `Schedule`

```php
use Illuminate\Console\Scheduling\Schedule;
```

Now inside the `boot` method called `app->booted` inside the closure make an instance of schedule then register the command to be called and its frequency.

```php
$this->app->booted(function () {
    $schedule = $this->app->make(Schedule::class);
    $schedule->command('tickets:import')->everyMinute();
});
```
