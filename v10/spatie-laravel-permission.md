---
title: Using Spatie permissions package with modules
---

Spatie provide a powerful roles and permissions package for Laravel. it's a great way to manage complete roles each with their own permissions. 

Consult their docs for complete details https://spatie.be/docs/laravel-permission/v5/

## Installation

Install the package with composer:

```php
composer require spatie/laravel-permission
```

Publish the migrations and config file:

```php
php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider"
```

This shows these file have been published:

```php
Copied File [/vendor/spatie/laravel-permission/config/permission.php] To [/config/permission.php]
Copied File [/vendor/spatie/laravel-permission/database/migrations/create_permission_tables.php.stub] To [/database/migrations/2021_12_22_111730_create_permission_tables.php]
Publishing complete.
```

At this point the docs recommending migrating the database, we're not ready yet. We need to alter a migration to include modules but first we want to move all roles and permissions related files to a new module called `Roles`.

## Create a roles module:

```php
php artisan module:make Roles
```

Now move the `create_permission_tables.php` file from `database/migrations` to the new Roles module `Modules/Roles/Database/Migrations`

Open the `create_permission_tables.php` file add a modules enter to the permissions schema:

```php
Schema::create($tableNames['permissions'], function (Blueprint $table) {
    $table->bigIncrements('id');
    $table->string('name');       // For MySQL 8.0 use string('name', 125);
    $table->string('module');     // For MySQL 8.0 use string('module', 125);
    $table->string('guard_name'); // For MySQL 8.0 use string('guard_name', 125);
    $table->timestamps();

    $table->unique(['name', 'guard_name']);
});
```

When creating permission a module key should be used in order to group permissions to their modules.

For example:

> This would come from the seeder classes from each module, this example is from an admin module

Create permission to view dashboard, the module to an admin module and use the web guard.

```php
use Spatie\Permission\Models\Permission;

Permission::firstOrCreate(['name' => 'View Dashboard', 'module' => 'Admin', 'guard_name' => 'web']);
```

Then to check the permissions the usage is the same as the package documentation, you do not check the module setting here. The Module setting is used only to group the permissions into an UI for managing the permissions by their modules.

```php
auth()->user()->hasPermissionTo('View Dashboard')
```

## Seed a user with a role

Often you'll want to create a default user with an application and have them set as an Admin, to do this you can create a user inside a seed class. For this I use `Modules/Roles/Database/Seeders/RolesDatabaseSeeder.php` inside the `run` method.

> Roles should already exist before trying to assign them:

```php
use Spatie\Permission\Models\Role;

Role::firstOrCreate(['name' => 'Admin']);
Role::firstOrCreate(['name' => 'User']);
```

Using firstOrCreate means the seeder can run multiple times, only one user would ever be created this way.

Once the user is created a role of Admin is assigned to the user.

```php
public function run()
{
    app()['cache']->forget('spatie.permission.cache');

    $admin = User::firstOrCreate([
        'name' => 'Joe',
        'slug' => 'Bloggs',
        'email' => 'j.bloggs@domain.com'
    ],
    [
        'password' => bcrypt('a-random-password')
    ]);

    $admin->assignRole('Admin');
}
```

## Assign all permissions to a role

If you need to assign all permissions to a role, collect an array of the permission ids:

```php
$permissions = Permission::all()->pluck('id')->toArray();
```

Then select a role and sync the permissions array:

```php
$role = Role::find(1);
$role->syncPermissions($permissions);
```

## To group permissions by their roles

Get a specific role.

Get all modules from permissions, use distinct to remove duplicate module names.

```php
$role             = Role::findOrFail($id);
$permissionGroups = Permission::orderBy('module')->get()->groupBy('module');
```

Then in a view loop over the modules array, 

```php
@foreach($permissionGroups as $module => $permissions)
```

Inside each loop extract their permissions, to display the module name:

```php
Str::camel($module)
```

To show all permissions for the module:

```php 
@foreach ($permissions as $perm)
```

Then using the `$perm` to show the permission name `{{ $perm->name }}`

Putting it all together without any styling:

```php
@foreach($permissionGroups as $module => $permissions)
    <h3>{{ Str::camel($module) }}</h3>
        
    <table>
        <thead>
        <tr>
            <th>Permisson</th>
            <th>Action</th>
        </tr>
        </thead>
        @foreach ($permissions as $perm)
            <tr>
                <td>{{ $perm->name }}</td>
                <td><input type="checkbox" name="permission[]" value="{{ $perm->id }}" @checked($role->hasPermissionTo($perm->name)) /></td>
            </tr>
        @endforeach
    </table>
@endforeach
```

## Updating permissions to roles using syncPermissions

Once you've got an array of permissions you can update the permissions that are saved with a role. This will delete any permissions from the pivot table for the role that are not in the `$permissions` array.

```php
$role->syncPermissions($permissions);
```