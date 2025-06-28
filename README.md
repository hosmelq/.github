# Reusable GitHub Actions

> Reusable GitHub Actions for JavaScript and PHP/Laravel projects.

This repository now exposes reusable **actions** plus one reusable **workflow**:

- Actions under `.github/actions/*`
- Workflow: `.github/workflows/labeler.yml`

The old reusable CI and autofix workflows were removed. Consumers should compose their own workflows directly from these actions.

## What's Included

### JavaScript

- Bun or pnpm dependency installation
- Oxc lint and autofix
- Frontend build action
- Danger JS integration
- Optional Font Awesome Pro auth support

### PHP

- Shared PHP setup action
- Composer install with Composer cache
- Composer dependency bootstrap with optional artifact restore
- Pint, PHPStan, Rector, composer normalize, composer dependency analyser
- Optional reuse of a downloaded Composer artifact instead of reinstalling dependencies

### Workflow

- Pull request labeler workflow

## Optional Secrets

`FONTAWESOME_NPM_AUTH_TOKEN` - only needed if your project uses Font Awesome Pro packages

---

## Reusable Workflow

### `labeler.yml`

File: [`.github/workflows/labeler.yml`](/Users/hosmel/Code/hosmelq-dot-github/.github/workflows/labeler.yml)

Reusable workflow that applies labels to pull requests using this repo's shared [`.github/labeler.yml`](/Users/hosmel/Code/hosmelq-dot-github/.github/labeler.yml) config.

**Inputs**

- None

**Permissions**

- `contents: read`
- `issues: write`
- `pull-requests: write`

**Example**

```yaml
jobs:
  labeler:
    uses: hosmelq/.github/.github/workflows/labeler.yml@main
```

---

## Individual Actions

### Core Actions

#### `php-setup@main`

File: [`.github/actions/php-setup/action.yml`](/Users/hosmel/Code/hosmelq-dot-github/.github/actions/php-setup/action.yml)

Sets up PHP plus optional tools, extensions, and coverage configuration without installing Composer dependencies.

**Inputs**

- `php-coverage` (optional, default: `none`)
- `php-extensions` (optional, default: empty)
- `php-tools` (optional, default: `cs2pr`)
- `php-version` (optional, default: `8.5`)

**Example**

```yaml
- uses: hosmelq/.github/.github/actions/php-setup@main
  with:
    php-coverage: none
    php-extensions: mbstring, xml
    php-tools: composer, cs2pr
    php-version: '8.5'
```

#### `composer-install@main`

File: [`.github/actions/composer-install/action.yml`](/Users/hosmel/Code/hosmelq-dot-github/.github/actions/composer-install/action.yml)

Runs `php-setup`, restores Composer's download cache, and executes `composer install --no-interaction --no-progress --prefer-dist`.

**Inputs**

- `php-coverage` (optional, default: `none`)
- `php-extensions` (optional, default: empty)
- `php-tools` (optional, default: `cs2pr`)
- `php-version` (optional, default: `8.5`)

**Caching**

- Composer cache directory keyed by `composer.lock`

#### `composer-dependencies@main`

File: [`.github/actions/composer-dependencies/action.yml`](/Users/hosmel/Code/hosmelq-dot-github/.github/actions/composer-dependencies/action.yml)

Prepares Composer dependencies for downstream PHP actions by either running `composer-install` or restoring a previously uploaded `vendor` artifact.

**Inputs**

- `composer-artifact-name` (optional, default: empty)
- `php-coverage` (optional, default: `none`)
- `php-extensions` (optional, default: empty)
- `php-tools` (optional, default: `cs2pr`)
- `php-version` (optional, default: `8.5`)

**Behavior**

- If `composer-artifact-name` is empty, installs Composer dependencies
- Otherwise sets up PHP, downloads the named artifact, and extracts `php-vendor.tar`

**Example**

```yaml
- uses: hosmelq/.github/.github/actions/composer-dependencies@main
  with:
    composer-artifact-name: php-dependencies
    php-coverage: pcov
    php-extensions: mbstring, xml
    php-tools: composer, cs2pr
    php-version: '8.5'
```

#### `npm-install@main`

File: [`.github/actions/npm-install/action.yml`](/Users/hosmel/Code/hosmelq-dot-github/.github/actions/npm-install/action.yml)

Installs JavaScript dependencies with Bun or pnpm.

**Inputs**

- `bun-version` (optional, default: `latest`)
- `cache-dependency-path` (optional, default: `pnpm-lock.yaml`)
- `fontawesome-npm-auth-token` (optional)
- `node-version` (optional, default: `22`)
- `package-json-file` (optional, default: `package.json`)
- `package-manager` (optional, default: `bun`)
- `working-directory` (optional, default: `.`)

**Caching**

- pnpm cache via `actions/setup-node` when `package-manager: pnpm`
- Bun install uses Bun itself; this action does not add a separate Bun cache step

**Example**

```yaml
- uses: hosmelq/.github/.github/actions/npm-install@main
  with:
    cache-dependency-path: resources/js/pnpm-lock.yaml
    fontawesome-npm-auth-token: ${{ secrets.FONTAWESOME_NPM_AUTH_TOKEN }}
    node-version: '22'
    package-manager: bun
    package-json-file: resources/js/package.json
    working-directory: resources/js
```

### JavaScript Actions

#### `danger-js@main`

File: [`.github/actions/danger-js/action.yml`](/Users/hosmel/Code/hosmelq-dot-github/.github/actions/danger-js/action.yml)

Runs Danger JS for pull request review automation.

**Inputs**

- `danger-args` (optional, default: empty)
- `dangerfile` (optional, default: `dangerfile.js`)
- `node-version` (optional, default: `22`)
- `package-manager` (optional, default: `bun`)
- `working-directory` (optional, default: `.`)

**Behavior**

- Installs JS dependencies
- Runs `bunx danger ci` for Bun projects
- Runs `pnpm run danger ci` for pnpm projects

#### `js-autofix@main`

File: [`.github/actions/js-autofix/action.yml`](/Users/hosmel/Code/hosmelq-dot-github/.github/actions/js-autofix/action.yml)

Runs JavaScript autofix steps and then `autofix-ci`.

**Inputs**

- `fontawesome-npm-auth-token` (optional)
- `node-version` (optional, default: `22`)
- `package-manager` (optional, default: `bun`)
- `working-directory` (optional, default: `.`)

**Behavior**

- Installs JS dependencies
- Runs `bun run fix` or `pnpm run fix`
- Runs `bun run format` or `pnpm run format`
- Runs `autofix-ci/action@v1`

#### `js-frontend-build@main`

File: [`.github/actions/js-frontend-build/action.yml`](/Users/hosmel/Code/hosmelq-dot-github/.github/actions/js-frontend-build/action.yml)

Builds frontend assets using the project's configured build script.

**Inputs**

- `fontawesome-npm-auth-token` (optional)
- `node-version` (optional, default: `22`)
- `package-manager` (optional, default: `bun`)
- `working-directory` (optional, default: `.`)

**Behavior**

- Installs JS dependencies
- Runs `bun run build` or `pnpm run build`

#### `js-lint-check@main`

File: [`.github/actions/js-lint-check/action.yml`](/Users/hosmel/Code/hosmelq-dot-github/.github/actions/js-lint-check/action.yml)

Runs Oxc linting.

**Inputs**

- `fontawesome-npm-auth-token` (optional)
- `node-version` (optional, default: `22`)
- `package-manager` (optional, default: `bun`)
- `working-directory` (optional, default: `.`)

**Behavior**

- Installs JS dependencies
- Runs `bun run lint` or `pnpm run lint`

### PHP Actions

#### `php-autofix@main`

File: [`.github/actions/php-autofix/action.yml`](/Users/hosmel/Code/hosmelq-dot-github/.github/actions/php-autofix/action.yml)

Runs PHP autofix using Pint and then `autofix-ci`.

**Inputs**

- `php-coverage` (optional, default: `none`)
- `php-extensions` (optional, default: empty)
- `php-tools` (optional, default: `cs2pr`)
- `php-version` (optional, default: `8.5`)

**Behavior**

- Installs Composer dependencies
- Runs `composer pint`
- Runs `autofix-ci/action@v1`

#### `composer-normalize-check@main`

File: [`.github/actions/composer-normalize-check/action.yml`](/Users/hosmel/Code/hosmelq-dot-github/.github/actions/composer-normalize-check/action.yml)

Checks whether `composer.json` is normalized.

**Inputs**

- `composer-artifact-name` (optional, default: empty)
- `php-coverage` (optional, default: `none`)
- `php-extensions` (optional, default: empty)
- `php-tools` (optional, default: `cs2pr`)
- `php-version` (optional, default: `8.5`)

**Behavior**

- Uses `composer-dependencies` to install or restore `vendor`
- Runs `composer normalize --dry-run`

#### `composer-unused-dependencies-check@main`

File: [`.github/actions/composer-unused-dependencies-check/action.yml`](/Users/hosmel/Code/hosmelq-dot-github/.github/actions/composer-unused-dependencies-check/action.yml)

Runs `composer-dependency-analyser`.

**Inputs**

- `composer-artifact-name` (optional, default: empty)
- `php-coverage` (optional, default: `none`)
- `php-extensions` (optional, default: empty)
- `php-tools` (optional, default: `cs2pr`)
- `php-version` (optional, default: `8.5`)

**Behavior**

- Uses `composer-dependencies` to install or restore `vendor`
- Runs `vendor/bin/composer-dependency-analyser`

#### `php-pint-check@main`

File: [`.github/actions/php-pint-check/action.yml`](/Users/hosmel/Code/hosmelq-dot-github/.github/actions/php-pint-check/action.yml)

Runs Pint in check mode and emits annotations on failure.

**Inputs**

- `composer-artifact-name` (optional, default: empty)
- `php-coverage` (optional, default: `none`)
- `php-extensions` (optional, default: empty)
- `php-tools` (optional, default: `cs2pr`)
- `php-version` (optional, default: `8.5`)

**Behavior**

- Uses `composer-dependencies` to install or restore `vendor`
- Runs `./vendor/bin/pint --test`
- On failure, reruns Pint with `--format=checkstyle` piped to `cs2pr`

#### `php-rector-check@main`

File: [`.github/actions/php-rector-check/action.yml`](/Users/hosmel/Code/hosmelq-dot-github/.github/actions/php-rector-check/action.yml)

Runs Rector in dry-run mode.

**Inputs**

- `composer-artifact-name` (optional, default: empty)
- `php-coverage` (optional, default: `none`)
- `php-extensions` (optional, default: empty)
- `php-tools` (optional, default: `cs2pr`)
- `php-version` (optional, default: `8.5`)

**Behavior**

- Uses `composer-dependencies` to install or restore `vendor`
- Runs `vendor/bin/rector --ansi --dry-run`

#### `phpstan-check@main`

File: [`.github/actions/phpstan-check/action.yml`](/Users/hosmel/Code/hosmelq-dot-github/.github/actions/phpstan-check/action.yml)

Runs PHPStan and emits GitHub annotations.

**Inputs**

- `composer-artifact-name` (optional, default: empty)
- `php-coverage` (optional, default: `none`)
- `php-extensions` (optional, default: empty)
- `php-tools` (optional, default: `cs2pr`)
- `php-version` (optional, default: `8.5`)

**Behavior**

- Uses `composer-dependencies` to install or restore `vendor`
- Runs `vendor/bin/phpstan analyse --error-format=checkstyle | cs2pr`
