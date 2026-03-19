---
title: Inertia
order: 1
---

> From version 12.0.5

## Table of Contents

1. [Prerequisites — Installing Inertia](#prerequisites--installing-inertia)
2. [Configuration](#configuration)
3. [Setting Up the Application Entry Point](#setting-up-the-application-entry-point)
4. [Setting Up Your Application vite.config.js](#setting-up-your-application-viteconfigjs)
5. [Generating an Inertia Module](#generating-an-inertia-module)
6. [Generating Inertia Pages](#generating-inertia-pages)
7. [Generating Inertia Components](#generating-inertia-components)
8. [Vite Config Stub (Per-Module)](#vite-config-stub-per-module)
9. [Frontend Framework Flag Precedence](#frontend-framework-flag-precedence)
10. [Changed Files Reference](#changed-files-reference)

---

## Demo Applications

The following demo applications show Laravel Modules with Inertia in action:

| Framework | Repository |
|---|---|
| React | [laravel-modules-com/fuse-react](https://github.com/laravel-modules-com/fuse-react) |

## Prerequisites — Installing Inertia

This guide assumes Inertia.js is **already installed** in your Laravel application. If it is not, run the following commands first.

### 1. Install the server-side adapter

```bash
composer require inertiajs/inertia-laravel
```

### 2. Publish the Inertia middleware and add it to your stack

```bash
php artisan inertia:middleware
```

Then register it in `bootstrap/app.php` (Laravel 11+):

```php
->withMiddleware(function (Middleware $middleware) {
    $middleware->web(append: [
        \App\Http\Middleware\HandleInertiaRequests::class,
    ]);
})
```

### 3. Install the client-side adapter for your framework

**Vue**

```bash
npm install @inertiajs/vue3
npm install --save-dev @vitejs/plugin-vue
```

**React**

```bash
npm install @inertiajs/react
npm install --save-dev @vitejs/plugin-react
```

**Svelte**

```bash
npm install @inertiajs/svelte
npm install --save-dev @sveltejs/vite-plugin-svelte svelte
```

### 4. Create the root Blade template

Inertia requires a single root `resources/views/app.blade.php` that bootstraps every page. Create it if it does not already exist:

```bash
touch resources/views/app.blade.php
```

Paste the following content:

```blade
<!DOCTYPE html>
<html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <meta name="csrf-token" content="{{ csrf_token() }}">
        @inertiaHead
        @vite(['resources/css/app.css', 'resources/js/app.js'])
    </head>
    <body>
        @inertia
    </body>
</html>
```

- `@inertiaHead` — renders the `<title>` and any `<Head>` tags set by your Inertia pages.
- `@inertia` — renders the root `<div id="app">` that Inertia mounts your frontend framework into.
- `@vite(...)` — injects the compiled JS (and CSS if you add it to the array). Point this at the `app.js` published by `module:publish-inertia`.

> For full Inertia installation details see the [official Inertia docs](https://inertiajs.com/installation).

---

## Configuration

A new top-level `inertia` key has been added to `config/modules.php`.

```php
'inertia' => [
    'frontend' => 'vue', // Supported: "vue", "react", "svelte"
],
```

This sets the **default frontend framework** used by all Inertia-related commands when no explicit `--vue`, `--react`, or `--svelte` flag is passed. Set it once for your project and every command will pick it up automatically.

```php
// config/modules.php — switch your whole project to React:
'inertia' => [
    'frontend' => 'react',
],
```

---

## Setting Up the Application Entry Point

The `module:publish-inertia` command publishes a ready-made `resources/js/app.js` that knows how to resolve pages from both your application and every module.

```bash
php artisan module:publish-inertia
```

Add `--force` to overwrite an existing file. The command reads `config('modules.inertia.frontend')` to pick the right template — or pass `--vue`, `--react`, or `--svelte` to override it for this one run.

### What the published file looks like

All three variants wrap `createInertiaApp` in an `if (el && el.dataset.page)` guard so the app initialises only on pages that are actually served by Inertia — avoiding errors when the same `app.js` is loaded on non-Inertia routes.

**Vue**

```js
import { createApp, h } from 'vue'
import { createInertiaApp } from '@inertiajs/vue3'
import { resolvePageComponent } from 'laravel-vite-plugin/inertia-helpers'

const el = document.getElementById('app')

if (el && el.dataset.page) {
    createInertiaApp({
        resolve: (name) => {
            const appPages = import.meta.glob('./Pages/**/*.vue')
            const modulePages = import.meta.glob('/Modules/*/resources/js/Pages/**/*.vue')

            const parts = name.split('/')
            const modulePage = `/Modules/${parts[0]}/resources/js/Pages/${parts.slice(1).join('/')}.vue`

            if (modulePages[modulePage]) {
                return modulePages[modulePage]()
            }

            return resolvePageComponent(`./Pages/${name}.vue`, appPages)
        },
        setup({ el, App, props, plugin }) {
            createApp({ render: () => h(App, props) })
                .use(plugin)
                .mount(el)
        },
        progress: { color: '#4B5563' },
    })
}
```

**React**

```js
import { createRoot } from 'react-dom/client'
import { createInertiaApp } from '@inertiajs/react'
import { resolvePageComponent } from 'laravel-vite-plugin/inertia-helpers'

const el = document.getElementById('app')

if (el && el.dataset.page) {
    createInertiaApp({
        resolve: (name) => {
            const appPages = import.meta.glob('./Pages/**/*.jsx')
            const modulePages = import.meta.glob('/Modules/*/resources/js/Pages/**/*.jsx')

            const parts = name.split('/')
            const modulePage = `/Modules/${parts[0]}/resources/js/Pages/${parts.slice(1).join('/')}.jsx`

            if (modulePages[modulePage]) {
                return modulePages[modulePage]()
            }

            return resolvePageComponent(`./Pages/${name}.jsx`, appPages)
        },
        setup({ el, App, props }) {
            createRoot(el).render(<App {...props} />)
        },
        progress: { color: '#4B5563' },
    })
}
```

**Svelte**

```js
import { createInertiaApp } from '@inertiajs/svelte'
import { resolvePageComponent } from 'laravel-vite-plugin/inertia-helpers'

const el = document.getElementById('app')

if (el && el.dataset.page) {
    createInertiaApp({
        resolve: (name) => {
            const appPages = import.meta.glob('./Pages/**/*.svelte')
            const modulePages = import.meta.glob('/Modules/*/resources/js/Pages/**/*.svelte')

            const parts = name.split('/')
            const modulePage = `/Modules/${parts[0]}/resources/js/Pages/${parts.slice(1).join('/')}.svelte`

            if (modulePages[modulePage]) {
                return modulePages[modulePage]()
            }

            return resolvePageComponent(`./Pages/${name}.svelte`, appPages)
        },
        setup({ el, App, props }) {
            new App({ target: el, props })
        },
        progress: { color: '#4B5563' },
    })
}
```

The key things in all three versions:
- **`el && el.dataset.page` guard** — Inertia sets `data-page` on the root `<div>` when rendering. The guard prevents the app from booting on non-Inertia pages.
- **Module-aware resolver** — page names are expected in `ModuleName/PageName` format (e.g. `Blog/Index`). The resolver checks the module glob first and falls back to the app pages glob.

---

## Setting Up Your Application vite.config.js

Your **application-level** `vite.config.js` (in the project root, not inside a module) needs the right plugin for your chosen framework. Add the plugin import and uncomment it in the plugins array:

**Vue**

```js
import { defineConfig } from 'vite'
import laravel from 'laravel-vite-plugin'
import vue from '@vitejs/plugin-vue'

export default defineConfig({
    plugins: [
        laravel({
            input: ['resources/js/app.js'],
            refresh: true,
        }),
        vue({
            template: {
                transformAssetUrls: {
                    base: null,
                    includeAbsolute: false,
                },
            },
        }),
    ],
})
```

**React**

```js
import { defineConfig } from 'vite'
import laravel from 'laravel-vite-plugin'
import react from '@vitejs/plugin-react'

export default defineConfig({
    plugins: [
        laravel({
            input: ['resources/js/app.js'],
            refresh: true,
        }),
        react(),
    ],
})
```

**Svelte**

```js
import { defineConfig } from 'vite'
import laravel from 'laravel-vite-plugin'
import { svelte } from '@sveltejs/vite-plugin-svelte'

export default defineConfig({
    plugins: [
        laravel({
            input: ['resources/js/app.js'],
            refresh: true,
        }),
        svelte(),
    ],
})
```

> **Note:** The `input` path must point to the `app.js` published by `module:publish-inertia`. If you already have an `app.js` entry in your Vite config, update it to use the module-aware version rather than adding a second entry.

---

## Generating an Inertia Module

### Command

```bash
php artisan module:make <ModuleName> --inertia
```

### What it generates

A standard web module **minus** the Blade views, **plus** Inertia-specific files:

| Generated | Path |
|---|---|
| Inertia controller | `app/Http/Controllers/<Name>Controller.php` |
| Index page | `resources/js/Pages/Index.{vue,jsx,svelte}` |
| Create page | `resources/js/Pages/Create.{vue,jsx,svelte}` |
| Show page | `resources/js/Pages/Show.{vue,jsx,svelte}` |
| Edit page | `resources/js/Pages/Edit.{vue,jsx,svelte}` |

Blade files (`resources/views/index.blade.php` and `resources/views/components/layouts/master.blade.php`) are **skipped**.

The controller stub (`controller-inertia.stub`) returns `Inertia::render()` responses for each resource action, pre-wired to the module's page paths.

### Frontend selection

The pages are generated using the frontend set in `config/modules.php → inertia.frontend`. There is no per-command override on `module:make` — configure the default in config before running.

### Example

```bash
# With config set to 'vue' (default):
php artisan module:make Blog --inertia
# Generates Blog/resources/js/Pages/{Index,Create,Show,Edit}.vue

# With config set to 'react':
php artisan module:make Blog --inertia
# Generates Blog/resources/js/Pages/{Index,Create,Show,Edit}.jsx

# With config set to 'svelte':
php artisan module:make Blog --inertia
# Generates Blog/resources/js/Pages/{Index,Create,Show,Edit}.svelte
```

---

## Generating Inertia Pages

### Command

```bash
php artisan module:make-inertia-page <Name> <Module> [--vue] [--react] [--svelte]
```

### Arguments & options

| | Description |
|---|---|
| `name` | Page name. Supports subdirectories: `Contacts/Index` |
| `module` | The module to generate the page in |
| `--vue` | Force Vue output |
| `--react` | Force React output |
| `--svelte` | Force Svelte output |
| *(none)* | Falls back to `config('modules.inertia.frontend')` |

### Output location

`<ModulePath>/resources/js/Pages/<Name>.{vue,jsx,svelte}`

The path respects `config('modules.paths.generator.inertia.path')` if customised.

### Examples

```bash
# Uses config default
php artisan module:make-inertia-page Index Blog

# Explicit framework override
php artisan module:make-inertia-page Index Blog --react

# Subdirectory
php artisan module:make-inertia-page Contacts/Index Blog
# → resources/js/Pages/Contacts/Index.vue

# Studly-cases the name automatically
php artisan module:make-inertia-page my-dashboard Blog
# → resources/js/Pages/MyDashboard.vue
```

### Stubs

| Framework | Stub file |
|---|---|
| Vue | `src/Commands/stubs/inertia/page-vue.stub` |
| React | `src/Commands/stubs/inertia/page-react.stub` |
| Svelte | `src/Commands/stubs/inertia/page-svelte.stub` *(new)* |

**Vue page stub** — uses `<script setup>` and `@inertiajs/vue3` `<Head>`.

**React page stub** — named export with `@inertiajs/react` `<Head>`.

**Svelte page stub** — uses `<svelte:head>` and imports `page` from `@inertiajs/svelte`.

---

## Generating Inertia Components

```bash
php artisan module:make-inertia-component <Name> <Module> [--vue] [--react] [--svelte]
```

### Arguments & options

| | Description |
|---|---|
| `name` | Component name. Supports subdirectories: `UI/Button` |
| `module` | The module to generate the component in |
| `--vue` | Force Vue output |
| `--react` | Force React output |
| `--svelte` | Force Svelte output |
| *(none)* | Falls back to `config('modules.inertia.frontend')` |

### Output location

`<ModulePath>/resources/js/Components/<Name>.{vue,jsx,svelte}`

The path respects `config('modules.paths.generator.inertia-components.path')` if customised.

### Examples

```bash
php artisan module:make-inertia-component Button Blog
# → resources/js/Components/Button.vue

php artisan module:make-inertia-component UI/Modal Blog --react
# → resources/js/Components/UI/Modal.jsx

php artisan module:make-inertia-component DataTable Blog --svelte
# → resources/js/Components/DataTable.svelte
```

### Stubs

| Framework | Stub file |
|---|---|
| Vue | `src/Commands/stubs/inertia/component-vue.stub` *(new)* |
| React | `src/Commands/stubs/inertia/component-react.stub` *(new)* |
| Svelte | `src/Commands/stubs/inertia/component-svelte.stub` *(new)* |

Components are intentionally minimal — no `<Head>` or Inertia-specific imports, just a plain component shell ready to build on.

### Config keys used

```php
// config/modules.php
'paths' => [
    'generator' => [
        'inertia'            => ['path' => 'resources/js/Pages',      'generate' => false],
        'inertia-components' => ['path' => 'resources/js/Components', 'generate' => false],
    ],
],
```

Both `path` values can be customised. Set `generate` to `true` if you want the directory created upfront when scaffolding a module.

---

## Vite Config Stub (Per-Module)

Each module has its own `vite.config.js` generated from a stub. This is **separate** from the application-level `vite.config.js` covered above — it handles the module's own assets (SCSS, JS) for independent builds.

The per-module stub (`src/Commands/stubs/vite.stub`) now includes commented-out entries for all three frameworks:

```js
// Uncomment the import for your frontend framework:
// import vue from '@vitejs/plugin-vue';
// import react from '@vitejs/plugin-react';
// import { svelte } from '@sveltejs/vite-plugin-svelte';   ← new

// ...plugins array:
// vue({ template: { transformAssetUrls: { base: null, includeAbsolute: false } } }),
// react(),
// svelte(),   ← new
```

Uncomment the relevant lines for whichever framework you are using.
