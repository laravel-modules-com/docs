---
title: Languages
---

## Array translation strings

```php
$langPath = resource_path('lang/modules/'.$this->moduleNameLower);

if (is_dir($langPath)) {
    $this->loadTranslationsFrom($langPath, $this->moduleNameLower);
    $this->loadJsonTranslationsFrom($langPath);
} else {
    $this->loadTranslationsFrom(module_path($this->moduleName, 'lang'), $this->moduleNameLower);
    $this->loadJsonTranslationsFrom(module_path($this->moduleName, 'lang'));
}

```

use in blade `{{ __(blog::pages.about) }}` will be searched in:
`/lang/modules/en/pages.php`
`/Modules/Blog/lang/en/pages.php`

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