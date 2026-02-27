---
name: prepare-wordpress
description: "Use to scaffold or update a WordPress project with dev tooling, agent skills, linting (WPCS), testing (PHPUnit/Pest/Vitest), git hooks (Husky), config files (.editorconfig, .gitignore), and i18n support. Works for both new and existing projects."
compatibility: "macOS/Linux with Node.js 18+, Composer 2+, PHP 8.0+, git. Optional: WP-CLI for i18n commands."
---

# Prepare WordPress Project

## When to use

Use this skill when:

- Starting a new WordPress plugin or theme project from scratch
- Adding standard dev tooling to an existing WordPress project
- Ensuring a WordPress project follows coding standards and best practices
- Setting up testing, linting, git hooks, or i18n scaffolding

## Inputs required

- Repo root (current working directory).
- Whether this is a new or existing project (auto-detected).

## Procedure

### 0) Detect existing project state

Run the detection script to discover what already exists:

```sh
node skills/prepare-wordpress/scripts/detect_project.mjs
```

This outputs JSON with booleans for each component. Use it to skip phases that are already configured. Report to the user what will be added and what will be skipped.

### 1) Initialize base files (if needed)

If `package.json` does not exist:
```sh
npm init -y
```

If `composer.json` does not exist:
```sh
composer init --no-interaction --name=wordpress-project/plugin --description="WordPress plugin" --license=GPL-2.0-or-later
```

If `.git/` does not exist:
```sh
git init
```

### 2) Install agent skills

Install the following skills. Skip any that already exist under `~/.copilot/skills/` or `~/.agents/skills/`.

```sh
npx skills add https://github.com/automattic/agent-skills --skill wp-plugin-development
npx skills add https://github.com/automattic/agent-skills --skill wp-block-development
npx skills add https://github.com/automattic/agent-skills --skill wordpress-router
npx skills add https://github.com/automattic/agent-skills --skill wp-performance
npx skills add https://github.com/automattic/agent-skills --skill wp-wpcli-and-ops
npx skills add https://github.com/jeffallan/claude-skills --skill wordpress-pro
```

### 3) Composer dependencies and scripts

Install all PHP dev dependencies in a single command:

```sh
composer require --dev phpunit/phpunit wp-coding-standards/wpcs dealerdirect/phpcodesniffer-composer-installer pestphp/pest
```

Then merge these scripts into `composer.json` (do not overwrite existing scripts):

```json
{
  "scripts": {
    "test": "phpunit",
    "lint": "phpcs --standard=WordPress --extensions=php ."
  }
}
```

See: `references/composer-setup.md`

### 4) Husky git hooks

**Skip if `.husky/` already exists.**

```sh
npm install --save-dev husky
npx husky init
```

Create `.husky/pre-push` with:

```sh
composer install --no-dev --optimize-autoloader
```

If `.husky/` exists but `pre-push` is missing, only create the hook file.

See: `references/husky-setup.md`

### 5) Config files

**`.editorconfig`** — Skip if it already exists. Create with WordPress-standard settings.

See: `references/config-files.md`

**`.gitignore`** — If it exists, merge missing entries. If not, create it.

See: `references/config-files.md`

### 6) Vitest setup

**Skip if `vitest.config.js` already exists.**

```sh
npm install --save-dev vitest jsdom
```

Create `vitest.config.js` and `tests/setup.js`.

Merge a `test:js` script into `package.json`:

```json
{
  "scripts": {
    "test:js": "vitest run"
  }
}
```

See: `references/vitest-setup.md`

### 7) i18n scaffolding

**Skip if `i18n-map.json` already exists.**

Create `i18n-map.json` with placeholder block paths.

Merge i18n npm scripts into `package.json`, using the **current folder name** (basename of the repo root) as the text domain and .pot filename. For example, if the project is in `~/Projects/my-plugin`, use `my-plugin` as the slug.

Create `languages/` directory.

See: `references/i18n-setup.md`

### 8) Final summary

Print a status table showing each phase as ✅ installed, ⏭ skipped, or 🔀 merged.

Remind the user to:
- Replace `BLOCK-NAME` in `i18n-map.json` with actual block directory names.
- Run `composer install` and `npm install`.

## Verification

- All config files exist and are well-formed.
- `composer validate` passes.
- `npm ls` shows no missing peer dependencies for vitest/husky.
- `.husky/pre-push` is executable.
- Agent skills are present under `~/.copilot/skills/` or `~/.agents/skills/`.

## Failure modes / debugging

- `composer require` fails: PHP version too old, or Composer not installed. Check `php -v` and `composer --version`.
- `npx husky init` fails: not a git repo. Run `git init` first.
- `npx skills add` fails: Node.js < 18 or network issue. Check `node -v`.
- Pest install fails with conflict: PHPUnit version mismatch. Let Composer resolve dependency tree.

## Escalation

If a specific tool or dependency fails, install it manually and re-run the detection script to continue from where you left off.
