---
title: Languages and Translations
order: 5
---

Module translations are stored in `Modules/{ModuleName}/lang/` and are loaded with the module's alias as the namespace.

## Directory Structure

```
Modules/Blog/
└── lang/
    ├── en/
    │   ├── messages.php
    │   └── validation.php
    └── fr/
        ├── messages.php
        └── validation.php
```

## Loading Translations

The generated service provider registers translations automatically:

```php
protected function registerTranslations(): void
{
    $langPath = resource_path('lang/modules/' . $this->moduleNameLower);

    if (is_dir($langPath)) {
        $this->loadTranslationsFrom($langPath, $this->moduleNameLower);
        $this->loadJsonTranslationsFrom($langPath);
    } else {
        $this->loadTranslationsFrom(module_path($this->moduleName, 'lang'), $this->moduleNameLower);
        $this->loadJsonTranslationsFrom(module_path($this->moduleName, 'lang'));
    }
}
```

The method checks whether the application has published translations to `resources/lang/modules/blog`. If so, those take priority, allowing users to override module translations without modifying the module itself.

## Array Translation Files

Create translation files in `lang/en/messages.php`:

```php
<?php

return [
    'welcome'      => 'Welcome to the Blog',
    'post_created' => 'Post created successfully.',
    'post_deleted' => 'Post deleted.',
    'not_found'    => 'The requested post could not be found.',
];
```

Use the `moduleAlias::file.key` syntax:

```php
// In PHP
__('blog::messages.welcome')
trans('blog::messages.post_created')

// In Blade
{{ __('blog::messages.welcome') }}
@lang('blog::messages.post_deleted')
```

With parameters:

```php
// messages.php
'post_count' => 'You have :count posts.',

// Usage
__('blog::messages.post_count', ['count' => $user->posts()->count()])
```

## Nested Translation Groups

Organise translation strings into groups within a file:

```php
// lang/en/posts.php
return [
    'actions' => [
        'create'  => 'Create Post',
        'edit'    => 'Edit Post',
        'delete'  => 'Delete Post',
        'publish' => 'Publish',
    ],
    'status' => [
        'draft'     => 'Draft',
        'published' => 'Published',
        'scheduled' => 'Scheduled',
    ],
];
```

Access nested keys:

```php
__('blog::posts.actions.create')
__('blog::posts.status.published')
```

## JSON Translation Files

JSON translations allow you to use the original string as the key, which is especially useful for front-end and multi-language sites.

Create `lang/fr.json`:

```json
{
    "Welcome to the Blog": "Bienvenue sur le Blog",
    "Create Post": "Créer un article",
    "Post created successfully.": "Article créé avec succès."
}
```

Use them exactly like any other translation string — no namespace required:

```php
__('Create Post')
__('Post created successfully.')
```

The `loadJsonTranslationsFrom` call in the service provider enables this.

## Checking Missing Translation Keys

Identify missing translations across module language files:

```bash
php artisan module:lang Blog
```

## Publishing Translations

Publish module translation files to `resources/lang/modules/blog/` so they can be customised at the application level:

```bash
php artisan module:publish-translation Blog
```

## Pluralisation

```php
// lang/en/messages.php
'posts' => '{0} No posts|{1} One post|[2,*] :count posts',
```

Use with `trans_choice`:

```php
trans_choice('blog::messages.posts', $count, ['count' => $count])
```

## Locale Fallback

Laravel's translation system handles locale fallback automatically. If a key is not found in the current locale, it falls back to the `fallback_locale` set in `config/app.php`.
