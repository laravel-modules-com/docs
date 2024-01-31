---
title: Publishing Modules
---

After creating a module and you are sure your module will be used by other developers. You can push your module to [github](https://github.com), [gitlab](https://gitlab.com) or [bitbucket](https://bitbucket.org) and after that you can submit your module to the packagist website.

You can follow this step to publish your module.

1. Create A Module.
2. Make sure that you mentioned the `type` of the module in the `composer.json` as `laravel-module` 
3. Push the module to github, bitbucket, gitlab etc. Make sure the repository name follows the convention then it will be moved to the right directory automatically. The repo name should be like `<namespace>/<name>-module`, a `-module` at the end. Example: `https://github.com/nWidart/article-module`. This module will be installed in `Module/Article` directory.
4. Submit your module to the packagist website.
Submit to packagist is very easy, just give your github repository, click submit and you done.

## Have modules be installed in the `Modules/` folder

Published modules can be installed like other composer packages. In any Laravel project install the [nwidart/laravel-modules](https://github.com/nwidart/laravel-modules) package by following the instruction and then you can install your own modules. One extra step you need to take to install the module into the `Modules` directory of the project. 

The extra step is to install an additional composer plugin, [joshbrw/laravel-module-installer](https://github.com/joshbrw/laravel-module-installer) which will move the module files automatically. If you need to install the modules other than the `Modules` directory then add the following in your module composer.json. 

```php
"extra": {
    "module-dir": "Custom"
}
```

After installing the composer plugin onces, now to install the module you have to use the composer command as like other regular packages, 

```php
composer require nwidart/article-module
```

## A closer look

**Important**

A module will need to be in its own directly in order to publish only the single module to GIT. Work on the module in a project, when its ready to be converted into a package extract the module to its own folder and rename the module to be all lowercase and ensure it has -module on the end. For example, a module called `Quotes` would be renamed to `quotes-module`.

## GIT

Now it's time to set up GIT.

initialise git by typing in terminal:

```php
git init
```

This creates a new git instance.

Then add all your files and commit them:

```php
git add .
git commit -m 'first commit'
```

From this point onwards any changes you make can be committed to GIT.

## Setup repo on BitBucket

A repository needs to be created in Bitbucket click the + button in the sidebar and click repository. direct URL is [https://bitbucket.org/repo/create](https://bitbucket.org/repo/create)

Enter a project and repository name. Name the repository in the format of `quotes-module`.

For the branch enter `main` and for gitignore select No, you don’t want any files being generated.

Add the remote origin in GIT, go back to your package and in terminal enter replace quotes with the name of the module. Also change the username to match your own.

This will connect Bitbucket to the package.

```php
git remote add origin git@bitbucket.org:username/quotes-module.git
```

To upload the package type:

```php
git push -u origin main
```

This is only required for the first time, after then a `git push` can be used to push up changes.

Now the module has been pushed to Bitbucket as a package and can be installed into other projects.


## Setup repo on GitHub

A repository needs to be created in GitHub by going to [https://github.com/new](https://github.com/new)

Enter a repository name. Name the repository in the format of `quotes-module`

Don't check any of the boxes for Initialise this repository with, you don’t want any files being generated.

Click Create Repository.

Now you're ready to upload your local module.

Add the remote origin in GIT, go back to your package and in terminal enter replace quotes with the name of the module. Also change the username to match your own.

This will connect GitHub to the package.

```php
git remote add origin git@github.com:username/quotes-module.git
```

To upload the package type:

```php
git push -u origin main
```

This is only required for the first time, after then a `git push` can be used to push up changes.

Now the module has been pushed to Bitbucket as a package and can be installed into other projects.

# Install a module package (for private packages)

To install a module package open `composer.json`

In the `require` section type: (replace `moduleName` with the name of the package)

```php
"vendorname/moduleName-module": "@dev"
```

Next, add a repositories section again replace `moduleName` with the name of the package.

```php
"repositories": [
  {
      "type": "vcs",
      "url": "git@bitbucket.org:vendorname/moduleName-module.git"
  }
]
```

Now you can run `composer update` to install the package.

To have modules installed into the Modules directory install this package [https://github.com/joshbrw/laravel-module-installer](https://github.com/joshbrw/laravel-module-installer)

which will move the module files automatically. If you need to install the modules other than the `Modules` directory then add the following in your module composer.json.

```php
"extra": {
    "module-dir": "Custom"
}
```

## Setup Module

To activate the module type:

```php
php artisan module:enable moduleName
```

Now the module is ready to be migrated and seeded:

```php
php atisan module:migrate moduleName
php artisan module:seed moduleName
```

## Module updates

You are free to modify the module once installed, these changes are project-specific but if they are generic enough can be added to the package with the normal pull request flow the same as with projects.

If a package has been updated and you need to update the local copy you can do so by doing `composer update` be warned if any of the files have been modified you will get a warning:

```php
 Syncing toppackages/suppliers-module (dev-master 852c6b7) into cache
    toppackages/suppliers-module has modified files:
    M Resources/views/livewire/edit.blade.php
    Discard changes [y,n,v,d,s,?]?
```

Type a letter to continue:

y - discard changes and apply the update
n - abort the update and let you manually clean things up
v - view modified files
d - view local modifications (diff)
s - stash changes and try to reapply them after the update

## Uninstall Packages

If you uninstall a package from `composer.json` the module **WILL** be removed from the modules directory.



