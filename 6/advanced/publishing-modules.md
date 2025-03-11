---
title: Publishing Modules
order: 5
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

