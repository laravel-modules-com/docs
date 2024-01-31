---
title: Languages
---

## Array translation strings

```php
 $langPath = base_path('resources/lang/modules/blog');

if (is_dir($langPath)) {
    $this->loadTranslationsFrom($langPath, 'blog');
} else {
    $this->loadTranslationsFrom(__DIR__ .'/../Resources/lang', 'blog');
}
```

use in blade `{{ __(blog::foo) }}` will searched in:
`/lang/modules/en/foo.php`
`/Modules/Blog/Resources/lang/en/foo.php`

## JSON translation strings

To use the translation strings as keys you will need to place a JSON lang file in `Modules/ModuleName/resources/lang` ie fr.json for French.

The file can contain your language variants:

```php
{
  "Hello World": "Bonjour le monde"
}
```

By default, modules look for `lang/en/messages.php` file to tell the module to use JSON translations instead.

Find

```php
if (is_dir($langPath)) {
    $this->loadTranslationsFrom($langPath, $this->moduleNameLower);
} else {
    $this->loadTranslationsFrom(module_path($this->moduleName, ‘Resources/lang’), $this->moduleNameLower);
}
```

And swap the `loadTranslationsFrom`  to `loadJsonTranslationsFrom`

Now you can use JSON translations in your controllers and views:

```php
<div>
   <lable for="name">{{ __('Name') }}</lable>
   <input type="text" name="name" id="name" value="{{ old('name') }}">
</div>
     
<div>
   <lable for="subject">{{ __('Subject') }}</lable>
   <input type="text" name="subject" id="subject" value="{{ old('subject') }}">
</div>
```

The French fr.json file would contain:

```php
{
  "Name": "Nom",
  "Subject": "Sujette",
}
```

## 