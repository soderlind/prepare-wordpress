# Husky Setup

## Installation

```sh
npm install --save-dev husky
npx husky init
```

## Pre-push hook

Create `.husky/pre-push` with:

```sh
#!/usr/bin/env sh
composer install --no-dev --optimize-autoloader
```

Make sure the file is executable:

```sh
chmod +x .husky/pre-push
```

## Notes

- `npx husky init` creates `.husky/` and a sample `pre-commit` hook.
- The `pre-push` hook ensures production Composer dependencies are installed before pushing.
- If `.husky/` already exists but `pre-push` is missing, only create the hook file — do not re-init.
