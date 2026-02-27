# prepare-wordpress

> Feel free to fork and customize this skill for your specific needs. The provided code is a starting point for me when I need to quickly set up a WordPress project with best practices. It can be extended to include additional tools, configurations, or support for specific frameworks (like React, Vue, etc.) as needed.

An agent skill that scaffolds (or updates) a WordPress project with dev tooling, coding standards, testing, git hooks, and i18n support.

Works with [Claude Code](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview), [VS Code Copilot](https://code.visualstudio.com/docs/copilot/overview), and other agents that support the [skills](https://github.com/vercel-labs/skills) format.

## Install

```sh
npx skills add https://github.com/soderlind/prepare-wordpress --skill prepare-wordpress -g
```

`-g` installs globally (available in all projects). Without it, the skill is installed into the current project only.

## What it does

When triggered, the skill runs a detection script to check your project's current state, then only adds what's missing:

| Phase | What it sets up |
|-------|----------------|
| **Init** | `git init`, `npm init -y`, `composer init` (if missing) |
| **Agent skills** | Installs 6 WordPress dev skills from [automattic/agent-skills](https://github.com/automattic/agent-skills) and [jeffallan/claude-skills](https://github.com/jeffallan/claude-skills) |
| **Composer** | PHPUnit, Pest, WPCS, PHPCS installer + `test` and `lint` scripts |
| **Husky** | Git hooks with a `pre-push` hook running `composer install --no-dev --optimize-autoloader` |
| **Config files** | `.editorconfig` (4-space, UTF-8, LF) and `.gitignore` (vendor, node_modules, .env) |
| **Vitest** | `vitest` + `jsdom`, config file, test setup, and `test:js` npm script |
| **i18n** | `i18n-map.json` template, `languages/` directory, and WP-CLI i18n npm scripts |

### Smart detection

The skill's detection script checks for existing files and configs before each phase. If something already exists, it's skipped (or merged in the case of `.gitignore`).

## Usage

After installation, the skill is triggered automatically when you ask your agent to prepare or scaffold a WordPress project. Examples:

```
Prepare this project for WordPress plugin development
```

```
Set up dev tooling for this WordPress project
```

```
Add testing, linting, and i18n to this WordPress plugin
```

## After scaffolding

Replace the placeholders in the generated files:

1. **`i18n-map.json`** — Replace `BLOCK-NAME` with your actual block directory names
2. Run `composer install` and `npm install`

The i18n scripts automatically use the current folder name as the text domain.

## Prerequisites

- Node.js 18+
- Composer 2+
- PHP 8.0+
- git
- [WP-CLI](https://wp-cli.org/) (for i18n commands)

## Other commands

```sh
# List installed skills
npx skills list -g

# Update to latest version
npx skills update

# Remove
npx skills remove prepare-wordpress -g
```

## License

GPL-2.0-or-later
