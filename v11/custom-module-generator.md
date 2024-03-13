---
title: Build your own module generator
---

The default modules are fine to get started but often you'll have your own structure that you'll use from module to module. It's nice to be able to use your structure as a template for all future modules to be created from.

There's 2 options here. Edit the stub files or create your own base module and custom artisan command.

Lets explore both options

## Stub files

By default, the `config/modules.php` file has a stubs path that points to the laravel-modules package vendor:

```php
'stubs' => [
    'enabled' => false,
    'path' => base_path() . '/vendor/nwidart/laravel-modules/src/Commands/stubs',
```

You can change this path to one were you can edit the files. You should never edit files within the vendor folder so instead lets take a copy of the `vendor/nwidart/laravel-modules/src/Commands/stubs` folder to this path `stubs/module`

If you do not have a `stubs` folder then first publish Laravel's stubs:

```php
php artisan stub:publish
```

This will create a stubs folder containing all the stub files Laravel used when creating classes. Make a folder called `module` inside `stubs`.

Ensure you've copied the contents of `vendor/nwidart/laravel-modules/src/Commands/stubs` into `stubs/module` now open `config/modules.php` and edit the path for stubs:

 ```php
'stubs' => [
    'enabled' => false,
    'path' => base_path() . '/stubs/module',
```

Now you can edit any of the stubs for instance I like to remove all the docblocks from controllers by default the `controllers.stub` file contains:

```php
<?php

namespace $CLASS_NAMESPACE$;

use Illuminate\Contracts\Support\Renderable;
use Illuminate\Http\Request;
use Illuminate\Routing\Controller;

class $CLASS$ extends Controller
{
    /**
     * Display a listing of the resource.
     * @return Renderable
     */
    public function index()
    {
        return view('$LOWER_NAME$::index');
    }

    /**
     * Show the form for creating a new resource.
     * @return Renderable
     */
    public function create()
    {
        return view('$LOWER_NAME$::create');
    }

    /**
     * Store a newly created resource in storage.
     * @param Request $request
     * @return Renderable
     */
    public function store(Request $request)
    {
        //
    }

    /**
     * Show the specified resource.
     * @param int $id
     * @return Renderable
     */
    public function show($id)
    {
        return view('$LOWER_NAME$::show');
    }

    /**
     * Show the form for editing the specified resource.
     * @param int $id
     * @return Renderable
     */
    public function edit($id)
    {
        return view('$LOWER_NAME$::edit');
    }

    /**
     * Update the specified resource in storage.
     * @param Request $request
     * @param int $id
     * @return Renderable
     */
    public function update(Request $request, $id)
    {
        //
    }

    /**
     * Remove the specified resource from storage.
     * @param int $id
     * @return Renderable
     */
    public function destroy($id)
    {
        //
    }
}
```

I always delete the docblocks so lets edit the stub for this file, open `stubs/module/controllers.stub`

```php
<?php

namespace $CLASS_NAMESPACE$;

use Illuminate\Contracts\Support\Renderable;
use Illuminate\Http\Request;
use Illuminate\Routing\Controller;

class $CLASS$ extends Controller
{
    public function index()
    {
        return view('$LOWER_NAME$::index');
    }

    public function create()
    {
        return view('$LOWER_NAME$::create');
    }

    public function store(Request $request)
    {
        //
    }

    public function show($id)
    {
        return view('$LOWER_NAME$::show');
    }

    public function edit($id)
    {
        return view('$LOWER_NAME$::edit');
    }

    public function update(Request $request, $id)
    {
        //
    }

    public function destroy($id)
    {
        //
    }
}
```

Now when you create a module or create a controller:

```php
php artisan module:make-controller CustomerController Customers
```

The stubs for controllers.stub will be used.

As you can see it's simple to edit the default files that are created. Now you could modify lots of files to have a structure you want by default but these stubs are used for both creating modules and files. Meaning if you modify the files to your defaults then generating additional classes they will also have the set structure. You may not always want this.

Often I want a starting structure for an entire module and bare-bones boilerplate when generating classes. 


## Base Module & custom Artisan command

What we're going to do is create an artisan command that will take a copy of a module and rename its files and placeholder words inside the module as a new module.

To generate a new module, you will want to create a module normally first ensure it has everything you want as a base. Keep it simple for instance for a CRUD (Create Read Update and Delete) module you'll want routes to list, add, edit and delete their controller methods views and tests.

### Base Module

Once you have a module you're happy as a starting point, you're ready to convert this to a base module. Copy the module to a location. I will be using `stubs/base-module` as the location.

```
stubs/
    base-module
        Config
        Console
        Database
        Http
        Models
        Providers
        Resources
        Routes
        Tests
        composer.json
        module.json
        package.json
        webpack.mix.js
```

Every reference to a module would need to be changed when making a new module, for instance you have a module called contacts, it has a model called contact everywhere in the module that uses the model would use Contact:: that would need to be changed to the name of the new module when creating a new module.

Instead of manually finding and copying all references to controllers, model, namespaces, views etc you would use placeholders.

## Placeholders

These are the placeholders I use:

```
{module_}      
```

Module name all lowercase seperate spaces with underscores.

```
{module-}  
```

Module name all lowercase seperate spaces with hypens.

```
{Module}  
```

Module name in CamelCase.

```
{module} 
```

Module name all in lowercase.

```
{Model}
```

Model name in CamelCase.

```
{model} 
```

Model name all in lowercase.

These placeholders can then be used throughout the module for example a controller:

```php
namespace Modules\{Module}\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Routing\Controller;
use Modules\{Module}\Models\{Model};

class {Module}Controller extends Controller
{
    public function index()
    {
        ${module} = {Model}::get();

        return view('{module}::index', compact('{module}'));
    }
}
```

When the placeholders are swapped out look like

```php
namespace Modules\Contacts\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Routing\Controller;
use Modules\Contacts\Models\Contact;

class {Module}Controller extends Controller
{
    public function index()
    {
        $contacts = Contact::get();

        return view('contacts::index', compact('contacts'));
    }
}
```

This allows for great customisation. You can create any structure you need and later are able to swap out the placeholder for the actual values.

As I've said you would first need to create a base module using these placeholders. In addition, you will want to edit filename such as `Contact.php` for a model to be `Model.php` These would be renamed automatically to the new module name.

### Base Module source code

You can find a complete module boilerplate at [https://github.com/modularlaravel/base-module](https://github.com/modularlaravel/base-module)

Let's build a base module here for completeness.

The module will have this structure:

```
base-module
  Config
    config.php
  Console
  Database
    Factories
        ModelFactory.php
    Migrations
        create_module_table.php
    Seeders
        ModelDatabaseSeeder.php
  Http
    Controllers
        ModuleController.php
    Middleware
    Requests
  Models
    Model.php
  Providers
    ModuleServiceProvider.php
    RouteServiceProvider.php
  Resources
    assets
        js
            app.js
        sass
    lang
    views
        create.blade.php
        edit.blade.php
        index.blade.php
  Routes
    api.php
    web.php
  Tests
    Feature
        ModuleTest.php
    Unit
  composer.json
  module.json
  package.json
  webpack.mix.js
```

**Config.php**

Will hold the name of the module such as Contacts

```php
<?php

return [
    'name' => '{Module}'
];
```

**ModelFactory.php**

```php
<?php
namespace Modules\{Module}\Database\Factories;

use Illuminate\Database\Eloquent\Factories\Factory;
use Modules\{Module}\Models\{Model};

class {Model}Factory extends Factory
{
    protected $model = {Model}::class;

    public function definition(): array
    {
        return [
            'name' => $this->faker->name()
        ];
    }
}
```

**create_model_table.php**

```php
<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class Create{Module}Table extends Migration
{
    public function up()
    {
        Schema::create('{module}', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('{module}');
    }
}
```

**ModuleDatabaseSeeder.php**

```php
<?php

namespace Modules\{Module}\Database\Seeders;

use Illuminate\Database\Seeder;
use Illuminate\Database\Eloquent\Model;

class {Module}DatabaseSeeder extends Seeder
{
    public function run()
    {
        Model::unguard();

        // $this->call("OthersTableSeeder");
    }
}
```

**ModuleController.php**

```php
<?php

namespace Modules\{Module}\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Routing\Controller;
use Modules\{Module}\Models\{Model};

class {Module}Controller extends Controller
{
    public function index()
    {
        ${module} = {Model}::get();

        return view('{module}::index', compact('{module}'));
    }

    public function create()
    {
        return view('{module}::create');
    }

    public function store(Request $request)
    {
        $request->validate([
            'name' => 'required|string'
        ]);

        {Model}::create([
            'name' => $request->input('name')
        ]);

        return redirect(route('app.{module}.index'));
    }

    public function edit($id)
    {
        ${model} = {Model}::findOrFail($id);

        return view('{module}::edit', compact('{model}'));
    }

    public function update(Request $request, $id)
    {
        $request->validate([
            'name' => 'required|string'
        ]);

        {Model}::findOrFail($id)->update([
            'name' => $request->input('name')
        ]);

        return redirect(route('app.{module}.index'));
    }

    public function destroy($id)
    {
        {Model}::findOrFail($id)->delete();

        return redirect(route('app.{module}.index'));
    }
}
```

**Model.php**

```php
<?php

namespace Modules\{Module}\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Modules\{Module}\Database\Factories\{Model}Factory;

class {Model} extends Model
{
    use HasFactory;

    protected $fillable = ['name'];
    
    protected static function newFactory()
    {
        return {Model}Factory::new();
    }
}
```

**ModuleServiceProvider.php**

```php
<?php

namespace Modules\{Module}\Providers;

use Illuminate\Support\ServiceProvider;

class {Module}ServiceProvider extends ServiceProvider
{
    protected $moduleName = '{Module}';
    protected $moduleNameLower = '{module}';

    public function boot()
    {
        $this->registerTranslations();
        $this->registerConfig();
        $this->registerViews();
        $this->loadMigrationsFrom(module_path($this->moduleName, 'Database/Migrations'));
    }

    public function register()
    {
        $this->app->register(RouteServiceProvider::class);
    }

    protected function registerConfig()
    {
        $this->publishes([
            module_path($this->moduleName, 'Config/config.php') => config_path($this->moduleNameLower . '.php'),
        ], 'config');
        $this->mergeConfigFrom(
            module_path($this->moduleName, 'Config/config.php'), $this->moduleNameLower
        );
    }

    public function registerViews()
    {
        $viewPath = resource_path('views/modules/' . $this->moduleNameLower);

        $sourcePath = module_path($this->moduleName, 'Resources/views');

        $this->publishes([
            $sourcePath => $viewPath
        ], ['views', $this->moduleNameLower . '-module-views']);

        $this->loadViewsFrom(array_merge($this->getPublishableViewPaths(), [$sourcePath]), $this->moduleNameLower);
    }

    public function registerTranslations()
    {
        $langPath = resource_path('lang/modules/' . $this->moduleNameLower);

        if (is_dir($langPath)) {
            $this->loadJsonTranslationsFrom($langPath, $this->moduleNameLower);
        } else {
            $this->loadJsonTranslationsFrom(module_path($this->moduleName, 'Resources/lang'), $this->moduleNameLower);
        }
    }

    public function provides()
    {
        return [];
    }

    private function getPublishableViewPaths(): array
    {
        $paths = [];
        foreach (\Config::get('view.paths') as $path) {
            if (is_dir($path . '/modules/' . $this->moduleNameLower)) {
                $paths[] = $path . '/modules/' . $this->moduleNameLower;
            }
        }
        return $paths;
    }
}
```

**RouteServiceProvider.php**

```php
<?php

namespace Modules\{Module}\Providers;

use Illuminate\Support\Facades\Route;
use Illuminate\Foundation\Support\Providers\RouteServiceProvider as ServiceProvider;

class RouteServiceProvider extends ServiceProvider
{
    protected $moduleNamespace = 'Modules\{Module}\Http\Controllers';

    public function boot()
    {
        parent::boot();
    }

    public function map()
    {
        $this->mapApiRoutes();
        $this->mapWebRoutes();
    }

    protected function mapWebRoutes()
    {
        Route::middleware('web')
            ->namespace($this->moduleNamespace)
            ->group(module_path('{Module}', '/Routes/web.php'));
    }

    protected function mapApiRoutes()
    {
        Route::prefix('api')
            ->middleware('api')
            ->namespace($this->moduleNamespace)
            ->group(module_path('{Module}', '/Routes/api.php'));
    }
}
```

**create.blade.php**

```php
@extends('layouts.app')

@section('content')
    <div class="card">
        <h1>Add {Model}</h1>

        <x-form action="{{ route('app.{module}.create') }}">
            <x-form.input name="name" />
            <x-form.button>Submit</x-form.button>
        </x-form>
    </div>
@endsection
```

**edit.blade.php**

```php
@extends('layouts.app')

@section('content')
    <div class="card">
        <h1>Edit {Model}</h1>

        <x-form action="{{ route('app.{module}.update', ${model}->id) }}" method="patch">
            <x-form.input name="name">{{ ${model}->name }}</x-form.input>
            <x-form.button>Update</x-form.button>
        </x-form>
    </div>
@endsection
```

**index.blade.php**

```php
@extends('layouts.app')

@section('content')
    <div class="card">
    <h1>{Module}</h1>

    <p><a href="{{ route('app.{module}.create') }}">Add {Model}</a> </p>

    <table>
        <tr>
            <td>Name</td>
            <td>Action</td>
        </tr>
        @foreach(${module} as ${model})
            <tr>
                <td>{{ ${model}->name }}</td>
                <td>
                    <a href="{{ route('app.{module}.edit', ${model}->id) }}">Edit</a>

                    <a href="#" onclick="event.preventDefault(); document.getElementById('delete-form').submit();">Delete</a>
                    <x-form id="delete-form" method="delete" action="{{ route('app.{module}.delete', ${model}->id) }}" />
                </td>
            </tr>
        @endforeach
    </table>
    </div>
@endsection
```

**api.php**

```php
<?php

use Illuminate\Http\Request;

Route::middleware('auth:api')->get('/{module}', function (Request $request) {
    return $request->user();
});
```

**web.php**

```php
<?php

use Modules\{Module}\Http\Controllers\{Module}Controller;

Route::middleware('auth')->prefix('app/{module}')->group(function() {
    Route::get('/', [{Module}Controller::class, 'index'])->name('app.{module}.index');
    Route::get('create', [{Module}Controller::class, 'create'])->name('app.{module}.create');
    Route::post('create', [{Module}Controller::class, 'store'])->name('app.{module}.store');
    Route::get('edit/{id}', [{Module}Controller::class, 'edit'])->name('app.{module}.edit');
    Route::patch('edit/{id}', [{Module}Controller::class, 'update'])->name('app.{module}.update');
    Route::delete('delete/{id}', [{Module}Controller::class, 'destroy'])->name('app.{module}.delete');
});
```

**ModuleTest.php**

```php
<?php

use Modules\{Module}\Models\{Model};

uses(Tests\TestCase::class);

test('can see {model} list', function() {
    $this->authenticate();
   $this->get(route('app.{module}.index'))->assertOk();
});

test('can see {model} create page', function() {
    $this->authenticate();
   $this->get(route('app.{module}.create'))->assertOk();
});

test('can create {model}', function() {
    $this->authenticate();
   $this->post(route('app.{module}.store', [
       'name' => 'Joe'
   ]))->assertRedirect(route('app.{module}.index'));

   $this->assertDatabaseCount('{module}', 1);
});

test('can see {model} edit page', function() {
    $this->authenticate();
    ${model} = {Model}::factory()->create();
    $this->get(route('app.{module}.edit', ${model}->id))->assertOk();
});

test('can update {model}', function() {
    $this->authenticate();
    ${model} = {Model}::factory()->create();
    $this->patch(route('app.{module}.update', ${model}->id), [
        'name' => 'Joe Smith'
    ])->assertRedirect(route('app.{module}.index'));

    $this->assertDatabaseHas('{module}', ['name' => 'Joe Smith']);
});

test('can delete {model}', function() {
    $this->authenticate();
    ${model} = {Model}::factory()->create();
    $this->delete(route('app.{module}.delete', ${model}->id))->assertRedirect(route('app.{module}.index'));

    $this->assertDatabaseCount('{module}', 0);
});
```

**composer.json**

Ensure you edit the author details here, all future modules will use these details.

```php
{
    "name": "dcblogdev/{module}",
    "description": "",
    "authors": [
        {
            "name": "David Carr",
            "email": "dave@dcblog.dev"
        }
    ],
    "extra": {
        "laravel": {
            "providers": [],
            "aliases": {

            }
        }
    },
    "autoload": {
        "psr-4": {
            "Modules\\{Module}\\": ""
        }
    }
}

```

**module.json**

```php
{
    "name": "{Module}",
    "label": "{Module}",
    "alias": "{module}",
    "description": "manage all {module}",
    "keywords": ["{module}"],
    "priority": 0,
    "providers": [
        "Modules\\{Module}\\Providers\\{Module}ServiceProvider"
    ],
    "aliases": {},
    "files": [],
    "requires": []
}
```

**package.json**

```php
{
    "private": true,
    "scripts": {
        "dev": "npm run development",
        "development": "cross-env NODE_ENV=development node_modules/webpack/bin/webpack.js --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js",
        "watch": "cross-env NODE_ENV=development node_modules/webpack/bin/webpack.js --watch --progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js",
        "watch-poll": "npm run watch -- --watch-poll",
        "hot": "cross-env NODE_ENV=development node_modules/webpack-dev-server/bin/webpack-dev-server.js --inline --hot --config=node_modules/laravel-mix/setup/webpack.config.js",
        "prod": "npm run production",
        "production": "cross-env NODE_ENV=production node_modules/webpack/bin/webpack.js --no-progress --hide-modules --config=node_modules/laravel-mix/setup/webpack.config.js"
    },
    "devDependencies": {
        "cross-env": "^7.0",
        "laravel-mix": "^5.0.1",
        "laravel-mix-merge-manifest": "^0.1.2"
    }
}
```

**webpack.mix.js**

```php
const dotenvExpand = require('dotenv-expand');
dotenvExpand(require('dotenv').config({ path: '../../.env'/*, debug: true*/}));

const mix = require('laravel-mix');
require('laravel-mix-merge-manifest');

mix.setPublicPath('../../public').mergeManifest();

mix.js(__dirname + '/Resources/assets/js/app.js', 'js/{module}.js')
    .sass( __dirname + '/Resources/assets/sass/app.scss', 'css/{module}.css');

if (mix.inProduction()) {
    mix.version();
}
```

### Artisan make:module command

Now we have a base module and its placeholders in place its time to write an artisan command that will take this as blueprints to create a new module from.

create a new command with this command:

```
php artisan make:command MakeModuleCommand
```

This will generate a new command class inside `app/Console/Commands/MakeModuleCommand.php`

```php
<?php

declare(strict_types=1);

namespace App\Console\Commands;

use Illuminate\Console\Command;

class MakeModule2Command extends Command
{
    protected $signature = 'command:name';
    protected $description = 'Command description';

    public function __construct()
    {
        parent::__construct();
    }

    public function handle()
    {
        return 0;
    }
}
```

We want to call the command using make:module, replace the signature and description:

```php
protected $signature = 'make:module';
protected $description = 'Create starter CRUD module';
```

Import Str and Symfony Filesystem classes:

```php
use Illuminate\Support\Str;
use Symfony\Component\Filesystem\Filesystem as SymfonyFilesystem;
```

For the Filesystem you would need to install the class via composer:

```php
composer require symfony/filesystem
```

Inside the handle method we want the command to ask for a name of the module, we can use `$this->ask` for this.

Ensure the module name is not empty with validation.

When the name of the module is provided, we will try to guess the name of the model and then ask for confirmation if the model name is correct.

If the name is correct confirmation of the module name and model will be printed and ask for final confirmation before moving to the next step.

If confirmation fails the process will restart.

Once the final confirmation has occurred a method called `generate` will be called.

```php
public function handle()
{
  $this->container['name'] = ucwords($this->ask('Please enter the name of the Module'));

  if (strlen($this->container['name']) == 0) {
      $this->error("\nModule name cannot be empty.");
  } else {
      $this->container['model'] = ucwords(Str::singular($this->container['name']));

      if ($this->confirm("Is '{$this->container['model']}' the correct name for the Model?", 'yes')) {
          $this->comment('You have provided the following information:');
          $this->comment('Name:  ' . $this->container['name']);
          $this->comment('Model: ' . $this->container['model']);

          if ($this->confirm('Do you wish to continue?', 'yes')) {
              $this->comment('Success!');
              $this->generate();
          } else {
              return false;
          }

          return true;
      } else {
          $this->handle();
      }
  }
  
  $this->info('Starter '.$this->container['name'].' module installed successfully.');
}
```

Now we need to add a generate method, this will need to add a few other methods let's add these first.

Add a method called rename that accepts a path, target and a type. This is used to rename file and save the renamed file into a new `$target` location.

If the `$type` is set to migration then create a timestamp that will be added to the filename and prefixed to the migration file. Otherwise do a direct rename.

```php
protected function rename($path, $target, $type = null)
{
    $filesystem = new SymfonyFilesystem;
    if ($filesystem->exists($path)) {
        if ($type == 'migration') {
            $timestamp = date('Y_m_d_his_');
            $target = str_replace("create", $timestamp."create", $target);
            $filesystem->rename($path, $target, true);
            $this->replaceInFile($target);
        } else {
            $filesystem->rename($path, $target, true);
        }
    }
}
```

Next we want to be able to copy an entire folder and into contents to a new location.

```php
protected function copy($path, $target)
{
    $filesystem = new SymfonyFilesystem;
    if ($filesystem->exists($path)) {
        $filesystem->mirror($path, $target);
    }
}
```

The final helper method will be to replace all the placeholders from the files.

Inside this method is where we define the placeholders and their values.

The types defined all the type of placeholders this is case and character sensitive, next each type is looped over. If the key from the type is module_ then all names with spaces will be switched to be underscored separated, likewise for the module- will be hyphened separated for spaces.

Finally, the file's contents will be replaced with the values of the placeholders name of model contents. 

```php
protected function replaceInFile($path)
{
    $name = $this->container['name'];
    $model = $this->container['model'];
    $types = [
        '{module_}' => null,
        '{module-}' => null,
        '{Module}' => $name,
        '{module}' => strtolower($name),
        '{Model}' => $model,
        '{model}' => strtolower($model)
    ];

    foreach($types as $key => $value) {
        if (file_exists($path)) {

            if ($key == "module_") {
                $parts = preg_split('/(?=[A-Z])/', $name, -1, PREG_SPLIT_NO_EMPTY);
                $parts = array_map('strtolower', $parts);
                $value = implode('_', $parts);
            }

            if ($key == 'module-') {
                $parts = preg_split('/(?=[A-Z])/', $name, -1, PREG_SPLIT_NO_EMPTY);
                $parts = array_map('strtolower', $parts);
                $value = implode('-', $parts);
            }

            file_put_contents($path, str_replace($key, $value, file_get_contents($path)));
        }
    }
}
```

Now we have these helpers methods we can create the generate method.

First we create local variables of name and model for easy referencing. The `$targetPath` variable stores the final module path.

Next we need to copy the base module into `Modules` folder.

The new module at this point is in the modules folder with all the placeholders and temporary file names, now we need to list all files that need the contents examining to place the placeholders.

The last chunk lists all filename that need to be renamed.

```php
protected function generate()
{
    $module     = $this->container['name'];
    $model      = $this->container['model'];
    $Module     = $module;
    $module     = strtolower($module);
    $Model      = $model;
    $targetPath = base_path('Modules/'.$Module);

    //copy folders
    $this->copy(base_path('stubs/base-module'), $targetPath);

    //replace contents
    $this->replaceInFile($targetPath.'/Config/config.php');
    $this->replaceInFile($targetPath.'/Database/Factories/ModelFactory.php');
    $this->replaceInFile($targetPath.'/Database/Migrations/create_module_table.php');
    $this->replaceInFile($targetPath.'/Database/Seeders/ModelDatabaseSeeder.php');
    $this->replaceInFile($targetPath.'/Http/Controllers/ModuleController.php');
    $this->replaceInFile($targetPath.'/Models/Model.php');
    $this->replaceInFile($targetPath.'/Providers/ModuleServiceProvider.php');
    $this->replaceInFile($targetPath.'/Providers/RouteServiceProvider.php');
    $this->replaceInFile($targetPath.'/Resources/views/create.blade.php');
    $this->replaceInFile($targetPath.'/Resources/views/edit.blade.php');
    $this->replaceInFile($targetPath.'/Resources/views/index.blade.php');
    $this->replaceInFile($targetPath.'/Routes/api.php');
    $this->replaceInFile($targetPath.'/Routes/web.php');
    $this->replaceInFile($targetPath.'/Tests/Feature/ModuleTest.php');
    $this->replaceInFile($targetPath.'/composer.json');
    $this->replaceInFile($targetPath.'/module.json');
    $this->replaceInFile($targetPath.'/webpack.mix.js');

    //rename
    $this->rename($targetPath.'/Database/Factories/ModelFactory.php', $targetPath.'/Database/Factories/'.$Model.'Factory.php');
    $this->rename($targetPath.'/Database/migrations/create_module_table.php', $targetPath.'/Database/migrations/create_'.$module.'_table.php', 'migration');
    $this->rename($targetPath.'/Database/Seeders/ModelDatabaseSeeder.php', $targetPath.'/Database/Seeders/'.$Module.'DatabaseSeeder.php');
    $this->rename($targetPath.'/Http/Controllers/ModuleController.php', $targetPath.'/Http/Controllers/'.$Module.'Controller.php');
    $this->rename($targetPath.'/Models/Model.php', $targetPath.'/Models/'.$Model.'.php');
    $this->rename($targetPath.'/Providers/ModuleServiceProvider.php', $targetPath.'/Providers/'.$Module.'ServiceProvider.php');
    $this->rename($targetPath.'/Tests/Feature/ModuleTest.php', $targetPath.'/Tests/Feature/'.$Module.'Test.php');
}
```

## Putting it all together:

```php
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;
use Illuminate\Support\Str;
use Symfony\Component\Filesystem\Filesystem as SymfonyFilesystem;

class MakeModuleCommand extends Command
{
    protected $signature = 'make:module';
    protected $description = 'Create starter CRUD module';

    public function handle()
    {
        $this->container['name'] = ucwords($this->ask('Please enter the name of the Module'));

        if (strlen($this->container['name']) == 0) {
            $this->error("\nModule name cannot be empty.");
        } else {
            $this->container['model'] = ucwords(Str::singular($this->container['name']));

            if ($this->confirm("Is '{$this->container['model']}' the correct name for the Model?", 'yes')) {
                $this->comment('You have provided the following information:');
                $this->comment('Name:  ' . $this->container['name']);
                $this->comment('Model: ' . $this->container['model']);

                if ($this->confirm('Do you wish to continue?', 'yes')) {
                    $this->comment('Success!');
                    $this->generate();
                } else {
                    return false;
                }

                return true;
            } else {
                $this->handle();
            }
        }

        $this->info('Starter '.$this->container['name'].' module installed successfully.');
    }

    protected function generate()
    {
        $module     = $this->container['name'];
        $model      = $this->container['model'];
        $Module     = $module;
        $module     = strtolower($module);
        $Model      = $model;
        $targetPath = base_path('Modules/'.$Module);

        //copy folders
        $this->copy(base_path('stubs/base-module'), $targetPath);

        //replace contents
        $this->replaceInFile($targetPath.'/Config/config.php');
        $this->replaceInFile($targetPath.'/Database/Factories/ModelFactory.php');
        $this->replaceInFile($targetPath.'/Database/Migrations/create_module_table.php');
        $this->replaceInFile($targetPath.'/Database/Seeders/ModelDatabaseSeeder.php');
        $this->replaceInFile($targetPath.'/Http/Controllers/ModuleController.php');
        $this->replaceInFile($targetPath.'/Models/Model.php');
        $this->replaceInFile($targetPath.'/Providers/ModuleServiceProvider.php');
        $this->replaceInFile($targetPath.'/Providers/RouteServiceProvider.php');
        $this->replaceInFile($targetPath.'/Resources/views/create.blade.php');
        $this->replaceInFile($targetPath.'/Resources/views/edit.blade.php');
        $this->replaceInFile($targetPath.'/Resources/views/index.blade.php');
        $this->replaceInFile($targetPath.'/Routes/api.php');
        $this->replaceInFile($targetPath.'/Routes/web.php');
        $this->replaceInFile($targetPath.'/Tests/Feature/ModuleTest.php');
        $this->replaceInFile($targetPath.'/composer.json');
        $this->replaceInFile($targetPath.'/module.json');
        $this->replaceInFile($targetPath.'/webpack.mix.js');

        //rename
        $this->rename($targetPath.'/Database/Factories/ModelFactory.php', $targetPath.'/Database/Factories/'.$Model.'Factory.php');
        $this->rename($targetPath.'/Database/migrations/create_module_table.php', $targetPath.'/Database/migrations/create_'.$module.'_table.php', 'migration');
        $this->rename($targetPath.'/Database/Seeders/ModelDatabaseSeeder.php', $targetPath.'/Database/Seeders/'.$Module.'DatabaseSeeder.php');
        $this->rename($targetPath.'/Http/Controllers/ModuleController.php', $targetPath.'/Http/Controllers/'.$Module.'Controller.php');
        $this->rename($targetPath.'/Models/Model.php', $targetPath.'/Models/'.$Model.'.php');
        $this->rename($targetPath.'/Providers/ModuleServiceProvider.php', $targetPath.'/Providers/'.$Module.'ServiceProvider.php');
        $this->rename($targetPath.'/Tests/Feature/ModuleTest.php', $targetPath.'/Tests/Feature/'.$Module.'Test.php');
    }

    protected function rename($path, $target, $type = null)
    {
        $filesystem = new SymfonyFilesystem;
        if ($filesystem->exists($path)) {
            if ($type == 'migration') {
                $timestamp = date('Y_m_d_his_');
                $target = str_replace("create", $timestamp."create", $target);
                $filesystem->rename($path, $target, true);
                $this->replaceInFile($target);
            } else {
                $filesystem->rename($path, $target, true);
            }
        }
    }

    protected function copy($path, $target)
    {
        $filesystem = new SymfonyFilesystem;
        if ($filesystem->exists($path)) {
            $filesystem->mirror($path, $target);
        }
    }

    protected function replaceInFile($path)
    {
        $name = $this->container['name'];
        $model = $this->container['model'];
        $types = [
            '{module_}' => null,
            '{module-}' => null,
            '{Module}' => $name,
            '{module}' => strtolower($name),
            '{Model}' => $model,
            '{model}' => strtolower($model)
        ];

        foreach($types as $key => $value) {
            if (file_exists($path)) {

                if ($key == "module_") {
                    $parts = preg_split('/(?=[A-Z])/', $name, -1, PREG_SPLIT_NO_EMPTY);
                    $parts = array_map('strtolower', $parts);
                    $value = implode('_', $parts);
                }

                if ($key == 'module-') {
                    $parts = preg_split('/(?=[A-Z])/', $name, -1, PREG_SPLIT_NO_EMPTY);
                    $parts = array_map('strtolower', $parts);
                    $value = implode('-', $parts);
                }

                file_put_contents($path, str_replace($key, $value, file_get_contents($path)));
            }
        }
    }
}
```

This seems like a lot of work but this gives you complete control over what a module will contain when generated. For simple CRUD modules you can build an application incredibly quickly with this approach.
