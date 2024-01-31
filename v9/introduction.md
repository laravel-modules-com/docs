---
title: Introduction
---

`nwidart/laravel-modules` is a Laravel package which was created to manage your large Laravel app using modules. A module is like a Laravel package, it has some views, controllers or models. This package is supported and tested in Laravel 9.

This package is a re-published, re-organised and maintained version of [pingpong/modules](https://github.com/pingpong-labs/modules), which isn't maintained anymore. This package is used in [AsgardCMS](https://asgardcms.com/).

With one big added bonus that the original package didn't have: **tests**.

Find out why you should use this package in the article: [Writing modular applications with laravel-modules](https://nicolaswidart.com/blog/writing-modular-applications-with-laravel-modules).

> Heads up:
    If you upgrade to v6 from previous version, run the following command: `php artisan module:v6:migrate`

## Upgrading from v8.3.0

If you have an existing config file, and you get an error:
```bash
Target class [CommandMakeCommand] does not exist
```
Then the config file will need updating first import the commands class:

```php
use Nwidart\Modules\Commands;
```

Next replace the commands array with:

```php
'commands' => [
    Commands\CommandMakeCommand::class,
    Commands\ComponentClassMakeCommand::class,
    Commands\ComponentViewMakeCommand::class,
    Commands\ControllerMakeCommand::class,
    Commands\DisableCommand::class,
    Commands\DumpCommand::class,
    Commands\EnableCommand::class,
    Commands\EventMakeCommand::class,
    Commands\JobMakeCommand::class,
    Commands\ListenerMakeCommand::class,
    Commands\MailMakeCommand::class,
    Commands\MiddlewareMakeCommand::class,
    Commands\NotificationMakeCommand::class,
    Commands\ProviderMakeCommand::class,
    Commands\RouteProviderMakeCommand::class,
    Commands\InstallCommand::class,
    Commands\ListCommand::class,
    Commands\ModuleDeleteCommand::class,
    Commands\ModuleMakeCommand::class,
    Commands\FactoryMakeCommand::class,
    Commands\PolicyMakeCommand::class,
    Commands\RequestMakeCommand::class,
    Commands\RuleMakeCommand::class,
    Commands\MigrateCommand::class,
    Commands\MigrateRefreshCommand::class,
    Commands\MigrateResetCommand::class,
    Commands\MigrateRollbackCommand::class,
    Commands\MigrateStatusCommand::class,
    Commands\MigrationMakeCommand::class,
    Commands\ModelMakeCommand::class,
    Commands\PublishCommand::class,
    Commands\PublishConfigurationCommand::class,
    Commands\PublishMigrationCommand::class,
    Commands\PublishTranslationCommand::class,
    Commands\SeedCommand::class,
    Commands\SeedMakeCommand::class,
    Commands\SetupCommand::class,
    Commands\UnUseCommand::class,
    Commands\UpdateCommand::class,
    Commands\UseCommand::class,
    Commands\ResourceMakeCommand::class,
    Commands\TestMakeCommand::class,
    Commands\LaravelModulesV6Migrator::class,
],
```

### Quick Example

Generate your first module using `php artisan module:make Blog`. The following structure will be generated.

```
Modules/
  ├── Blog/
      ├── Config/
      ├── Console/
      ├── Database/
          ├── factories/
          ├── Migrations/
          ├── Seeders/
      ├── Entities/
      ├── Http/
          ├── Controllers/
          ├── Middleware/
          ├── Requests/
      ├── Providers/
          ├── BlogServiceProvider.php
          ├── RouteServiceProvider.php
      ├── Resources/
          ├── assets/
          ├── lang/
          ├── views/
      ├── Routes/
          ├── api.php
          ├── web.php
      ├── Tests/
      ├── composer.json
      ├── module.json
      ├── package.json
      ├── webpack.mix.js
```
