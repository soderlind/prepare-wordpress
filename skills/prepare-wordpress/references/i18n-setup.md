# i18n Setup

## i18n-map.json

Maps source JS files to their build output paths. Required by `wp i18n make-json` to correctly associate translation strings with built JS files.

Create `i18n-map.json` with placeholder entries:

```json
{
  "blocks/BLOCK-NAME/save.js": "build/blocks/BLOCK-NAME/index.js"
}
```

The user must replace `BLOCK-NAME` with actual block directory names. Add one entry per block or JS source file that contains translatable strings.

## npm scripts

Merge into `package.json` scripts. Use `PLUGIN-SLUG` as placeholder for the text domain and .pot filename.

```json
{
  "scripts": {
    "i18n": "npm run i18n:make-pot && npm run i18n:update-po && npm run i18n:make-mo && npm run i18n:make-json && npm run i18n:make-php",
    "i18n:make-pot": "wp i18n make-pot . languages/PLUGIN-SLUG.pot --exclude=node_modules,vendor,tests,build --domain=PLUGIN-SLUG",
    "i18n:update-po": "wp i18n update-po languages/PLUGIN-SLUG.pot languages/",
    "i18n:make-mo": "wp i18n make-mo languages/",
    "i18n:make-json": "wp i18n make-json languages/ --no-purge --use-map=i18n-map.json",
    "i18n:make-php": "wp i18n make-php languages/"
  }
}
```

## Directory

```sh
mkdir -p languages
```

Create the `languages/` directory if it does not exist. This is where all generated `.pot`, `.po`, `.mo`, `.json`, and `.php` translation files are stored.

## Prerequisites

These scripts require [WP-CLI](https://wp-cli.org/) with the `i18n-command` package:

```sh
wp package install wp-cli/i18n-command
```

Or if WP-CLI ships with it bundled (v2.5+), it is already available.

## After scaffolding

The user must:
1. Replace `PLUGIN-SLUG` with the actual plugin text domain (e.g., `my-plugin`).
2. Replace `BLOCK-NAME` entries in `i18n-map.json` with real block directory names.
3. Ensure block source files use `__()`, `_e()`, `_n()`, etc. from `@wordpress/i18n`.
