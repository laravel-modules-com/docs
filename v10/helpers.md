---
title: Helpers
---

## Module path function

Get the path to the given module.

```php
$path = module_path('Blog');
```

Returns absolute path of project ending with /Modules/Blog

`module_path` can take a string as a second param, which tacks on to the end of the path:

```php
$path = module_path('Blog', 'Http/controllers/BlogController.php');
```

Returns absolute path of project ending with /Modules/Blog/Http/controllers/BlogController.php



