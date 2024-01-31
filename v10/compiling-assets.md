---
title: Compiling Assets 
---

# Vite

When you create a new module it also creates assets for CSS/JS and the `vite.config.js` configuration file.

```bash
php artisan module:make Blog
```

Change directory to the module:

```bash
cd Modules/Blog
```

The default `package.json` file includes everything you need to get started. You may install the dependencies it references by running:

```bash
npm install
```

Or for updating:

```bash
npm update
```

To learn more about Vite see https://laravel.com/docs/10.x/vite

Next choose between loading vite from a module or main vite.config.js you should not use both.

---

> From version 10.0.3

# Vite load from main vite.config.js

When loading assets from all `enabled` modules together follow these steps.

## vite.config.js 
the default `vite.config.js` needs to be updated. Copy and paste the below configuration.

```js
import {defineConfig} from 'vite';
import laravel from 'laravel-vite-plugin';
import collectModuleAssetsPaths from './vite-module-loader.js';

async function getConfig() {
    const paths = [
        'resources/css/app.css',
        'resources/js/app.js',
    ];
    const allPaths = await collectModuleAssetsPaths(paths, 'Modules');

    return defineConfig({
        plugins: [
            laravel({
                input: allPaths,
                refresh: true,
            })
        ]
    });
}

export default getConfig();
```

Inside the paths array set paths to any css/js files from within the resources folder.
Next if your modules folder has been changed update the line below to look in the modules folder.

```js
const allPaths = await collectModuleAssetsPaths(paths, 'Modules');
```

This config will load all `enabled` modules and read their `vite.config.js` files and compile them into one collection.

This will store the compiled assets into a `public/build` folder.

## Module vite.config.js 

to load assets from a module edit its `vite.config.php` and replace its contents with:

> Update the paths as needed.
```js
export const paths = [
    'Modules/Blog/resources/assets/sass/app.scss',
    'Modules/Blog/resources/assets/js/app.js',
];
```

## Render

Inside your layout file use the following to load the assets

```php
@vite(\Nwidart\Modules\Module::getAssets())
```

This will include the script and css tags automatically.

## Running Vite

To compile the assets run 

```bash
npm run build
```

To make use of hot-reloading / watching for changes run
```bash
npm run dev
```

---

# Vite load from modules

When using layout files within a module to render its assets follow these steps.

Ensure `vite-module-loader.js` has been published to the project root by running:

```bash
php artisan vendor:publish --provider="Nwidart\Modules\LaravelModulesServiceProvider" --tag="vite"
```

## vite.config.js 
the default `vite.config.js`:

```js
import { defineConfig } from 'vite';
import laravel from 'laravel-vite-plugin';

export default defineConfig({
    build: {
        outDir: '../../public/build-blog',
        emptyOutDir: true,
        manifest: true,
    },
    plugins: [
        laravel({
            publicDirectory: '../../public',
            buildDirectory: 'build-blog',
            input: [
                __dirname + '/resources/assets/sass/app.scss',
                __dirname + '/resources/assets/js/app.js'
            ],
            refresh: true,
        }),
    ],
});
```

This will store `app.scss` and `app.js` from the module into a `public/build-*` folder set by the module name. 
For a blog module the build folder will be called `build-blog` the build folders contain the paths vite needs to render the compiled files.

## Render

Inside the layout file use `module_vite()` to load the assets

```php
{{ module_vite('build-blog', 'resources/assets/sass/app.scss') }}
{{ module_vite('build-blog', 'resources/assets/js/app.js') }}
```

## Running Vite

To compile the assets run from the module's directory

```bash
npm run build
```

To make use of hot-reloading / watching for changes run from the module's directory
```bash
npm run dev
```

---

# Mix

> Mix is no longer recommended as Laravel now uses Vite by default. 

When you create a new module it also creates assets for CSS/JS and the `webpack.mix.js` configuration file.

```bash
php artisan module:make Blog
```

Change directory to the module:

```bash
cd Modules/Blog
```

The default `package.json` file includes everything you need to get started. You may install the dependencies it references by running:

```bash
npm install
```

## Running Mix

Mix is a configuration layer on top of [Webpack](https://webpack.js.org/), so to run your Mix tasks you only need to execute one of the NPM scripts that is included with the default `laravel-modules` `package.json` file

```bash
// Run all Mix tasks...
npm run dev

// Run all Mix tasks and minify output...
npm run production
```

After generating the versioned file, you won't know the exact file name. So, you should use Laravel's global mix function within your views to load the appropriately hashed asset. The  mix function will automatically determine the current name of the hashed file:

```html
// Modules/Blog/Resources/views/layouts/master.blade.php

<link rel="stylesheet" href="{{ mix('css/blog.css') }}">

<script src="{{ mix('js/blog.js') }}"></script>
```

For more info on Laravel Mix view the documentation here: [https://laravel.com/docs/mix](https://laravel.com/docs/mix)

> Note: to prevent the main Laravel Mix configuration from overwriting the `public/mix-manifest.json` file:


Install `laravel-mix-merge-manifest`

```bash
npm install laravel-mix-merge-manifest --save-dev
```

Modify `webpack.mix.js` main file

```js
let mix = require('laravel-mix');


/* Allow multiple Laravel Mix applications*/
require('laravel-mix-merge-manifest');
mix.mergeManifest();
/*----------------------------------------*/

mix.js('resources/assets/js/app.js', 'public/js')
   .sass('resources/assets/sass/app.scss', 'public/css');

```
