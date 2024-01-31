---
title: Livewire 
---

## Livewire components 

Laravel modules has no make-livewire command. You can manually write the Livewire classes and views.

In order to use Livewire components you'll need to register them in the module service provider, Inside the boot method define the Livewire component and its path.

`component()` takes 2 parameters:

1) The path to the blade views prefixed by the module name
2) The path to the Livewire class

```php
Livewire::component('contacts::add', Add::class);
```

To render a Livewire component in a blade view:

```php
<livewire:contacts::add />
```

For multi-word class name use hyphens for example a Livewire component called ContactForm:

```php
Livewire::component('contacts::contact-form', ContactForm::class);
```

Then render it in a view:

```php
<livewire:contacts::contact-form />
```

### Livewire Module Package

Whist this will work, it would be far better if you could generate livewire components for modules, good news, there's a package for that by **Mehediul Hassan Miton** called laravel-modules-livewire. [https://github.com/mhmiton/laravel-modules-livewire](https://github.com/mhmiton/laravel-modules-livewire)

This package automatically registered livewire components, no longer will you have to register all your Livewire components inside service providers.

Better yet you can generate new Livewire components using a `make-livewire` command.

#### Installation:

Install through composer:

```php
composer require mhmiton/laravel-modules-livewire
```

#### Config

Publish the package's configuration file:

```php
php artisan vendor:publish --provider="Mhmiton\LaravelModulesLivewire\LaravelModulesLivewireServiceProvider"
```

#### Making Components:

**Command Signature:**

```php
php artisan module:make-livewire <Component> <Module> --view= --force --inline --custom
```

**Example:**

```php
php artisan module:make-livewire Pages/AboutPage Core
php artisan module:make-livewire Pages\\AboutPage Core
php artisan module:make-livewire pages.about-page Core
```

**Force create component if the class already exists:**

```php
php artisan module:make-livewire Pages/AboutPage Core --force
```

**Output:**

```php
COMPONENT CREATED

CLASS: Modules/Core/Http/Livewire/Pages/AboutPage.php
VIEW:  Modules/Core/Resources/views/livewire/pages/about-page.blade.php
TAG: <livewire:core::pages.about-page />
```

**Inline Component:**

```php
php artisan module:make-livewire Core Pages/AboutPage --inline
```

**Output:**

```php
COMPONENT CREATED

CLASS: Modules/Core/Http/Livewire/Pages/AboutPage.php
TAG: <livewire:core::pages.about-page />
```

**Extra Option (--view):**

**You're able to set a custom view path for component with (--view) option.**

**Example:**

```php
php artisan module:make-livewire Pages/AboutPage Core --view=pages/about
php artisan module:make-livewire Pages/AboutPage Core --view=pages.about
```

**Output:**

```php
COMPONENT CREATED

CLASS: Modules/Core/Http/Livewire/Pages/AboutPage.php
VIEW:  Modules/Core/Resources/views/livewire/pages/about.blade.php
TAG: <livewire:core::pages.about-page />
```

#### Rendering Components:

```php
<livewire:{module-lower-name}::component-class-kebab-case />
```

**Example:**

```php
<livewire:core::pages.about-page />
```

#### Custom Module:

**To create components for the custom module, add custom modules in the config file.**

The config file is located at `config/modules-livewire.php` after publishing the config file.

Remove comment for these lines & add your custom modules.

```
    /*
    |--------------------------------------------------------------------------
    | Custom modules setup
    |--------------------------------------------------------------------------
    |
    */

    // 'custom_modules' => [
    //     'Chat' => [
    //         'path' => base_path('libraries/Chat'),
    //         'module_namespace' => 'Libraries\\Chat',
    //         // 'namespace' => 'Http\\Livewire',
    //         // 'view' => 'Resources/views/livewire',
    //         // 'name_lower' => 'chat',
    //     ],
    // ],
```

**Custom module config details**

> **path:** Add module full path (required).
>
> **module_namespace:** Add module namespace (required).
>
> **namespace:** By default using `config('modules-livewire.namespace')` value. You can set a different value for the specific module.
>
> **view:** By default using `config('modules-livewire.view')` value. You can set a different value for the specific module.
>
> **name_lower:** By default using module name to lowercase. If you set a custom name, module components will be registered by custom name.

### Livewire full-page components

With Livewire you can render components as full pages, instead of using controllers you would only use the Livewire class. Let's go over how to set up a full page component.

Create a Livewire component called Feedback inside a contacts module.

```php
php artisan module:make-livewire Feedback Contacts
```

This would output:

```php
 COMPONENT CREATED

CLASS: Modules/Contacts/Http/Livewire/Feedback.php
VIEW:  Modules/Contacts/Resources/views/livewire/feedback.blade.php
TAG: <livewire:contacts::feedback />
```

Now setup the route open the module `web.php` file 

```php
<?php

use Modules\Contacts\Http\Livewire\Feedback;

Route::middleware(['web', 'auth'])->prefix('app/contacts')->group(function() {
    Route::get('feedback', Feedback::class)->name('app.contacts.feedback');
});
```

Create a route for app/contacts/feedback. Routing directly to `Feedback::class` we do not specify a method only the class.

The Livewire class is a standard livewire class:

```php
<?php

namespace Modules\Contacts\Http\Livewire;

use Livewire\Component;

class Feedback extends Component
{
    public function render()
    {
        return view('contacts::livewire.feedback');
    }
}
```

the view feedback.blade.php:

```php
<div>
    <h3>The <code>Feedback</code> livewire component is loaded from the  <code>Contacts</code> module.</h3>
</div>
```

At this point there's not much different than embedded Livewire components except you don't have to render the component into a parent view file as its rendered directly.

By default, Livewire will use a layout from `layouts/app` If you want to use a layout from a module you can use `->layout()` on the render method.

```php
public function render()
{
    return view('contacts::livewire.feedback')->layout('contacts::layouts.app');
}
```