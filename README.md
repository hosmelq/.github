# Reusable GitHub Actions & Workflows

> Reusable GitHub Actions for **JavaScript** and **PHP/Laravel** projects.

## What's Included

### JavaScript Support

- **Package Manager**: Bun (default) with optional pnpm support and intelligent caching
- **Linting**: Oxc for fast, modern JavaScript/TypeScript linting
- **Formatting**: Prettier for consistent code formatting
- **Build System**: Frontend asset building with pnpm run build
- **Code Review**: Danger JS integration for automated PR reviews
- **Font Awesome Pro**: Built-in support for Font Awesome Pro packages

### PHP Support

- **Package Manager**: Composer with dependency caching
- **Code Style**: Laravel Pint for PSR-12 compliant formatting
- **Static Analysis**: PHPStan for type safety and bug detection
- **Code Quality**: Rector for automated code modernization
- **Dependency Analysis**: Composer dependency analyser for unused packages
- **Composer Validation**: Automatic composer.json normalization checks
- **Testing**: Full support for Pest/PHPUnit with database services
- **Coverage**: Support for pcov coverage driver
- **Services**: MySQL 9, PostgreSQL 18, and Redis 8 for testing environments
- **Laravel**: Automatic detection and support for Laravel applications vs packages

### Automation Features

- **Auto-fix**: Automatic code style fixing with commit back to PR
- **PR Labeling**: Automatic labeling based on file changes
- **Concurrency Control**: Smart cancellation of in-progress runs
- **GitHub Annotations**: Inline error reporting in PR reviews

## Optional Secrets

`FONTAWESOME_NPM_AUTH_TOKEN` - Only needed if your project uses FontAwesome Pro packages

---

## Workflows

### Autofix (JS)

Automatically fixes JavaScript code style issues and commits changes back to PR.

**Inputs:**

- `fontawesome-npm-auth-token` (optional): Font Awesome NPM authentication token
- `node-version` (default: '22'): The Node.js version to use
- `package-manager` (default: 'bun'): Package manager to use (`bun` or `pnpm`)
- `working-directory` (default: '.'): Directory to run package manager commands in

**Runs:**

Bun (default) or pnpm install, linting fixes, commit changes

```yaml
jobs:
  autofix:
    uses: hosmelq/.github/.github/workflows/js-autofix.yml@main
    with:
      node-version: '22'
      package-manager: bun
    secrets:
      fontawesome-npm-auth-token: ${{ secrets.FONTAWESOME_NPM_AUTH_TOKEN }}
```

### Autofix (PHP)

Automatically fixes PHP code style with Pint and applies Rector suggestions.

**Inputs:**

- `php-version` (default: '8.5'): The PHP version to use

**Runs:**

Composer install, Pint fixes, Rector fixes, commit changes

```yaml
jobs:
  autofix:
    uses: hosmelq/.github/.github/workflows/php-autofix.yml@main
    with:
      php-version: '8.5'
```

### Autofix (JS + PHP)

Combines JavaScript and PHP autofix capabilities for full-stack projects.

**Inputs:**

- `fontawesome-npm-auth-token` (optional): Font Awesome NPM authentication token
- `node-version` (default: '22'): The Node.js version to use
- `package-manager` (default: 'bun'): Package manager to use (`bun` or `pnpm`)
- `php-version` (default: '8.5'): The PHP version to use
- `working-directory` (default: '.'): Directory to run package manager commands in

**Runs:**

Both JS and PHP autofix processes

```yaml
jobs:
  autofix:
    uses: hosmelq/.github/.github/workflows/js-php-autofix.yml@main
    with:
      node-version: '22'
      package-manager: bun
      php-version: '8.5'
    secrets:
      fontawesome-npm-auth-token: ${{ secrets.FONTAWESOME_NPM_AUTH_TOKEN }}
```

### JavaScript CI

Runs full CI pipeline for JavaScript projects: install, lint, test, build.

**Inputs:**

- `fontawesome-npm-auth-token` (optional): Font Awesome NPM authentication token
- `node-version` (default: '22'): The Node.js version to use
- `package-manager` (default: 'bun'): Package manager to use (`bun` or `pnpm`)
- `working-directory` (default: '.'): Directory to run package manager commands in

**Runs:**

Bun (default) or pnpm install, Oxc linting, frontend build

**Services:**

None

```yaml
jobs:
  ci:
    uses: hosmelq/.github/.github/workflows/js-ci.yml@main
    with:
      node-version: '22'
      package-manager: bun
    secrets:
      fontawesome-npm-auth-token: ${{ secrets.FONTAWESOME_NPM_AUTH_TOKEN }}
```

### PHP CI

Runs full CI pipeline for PHP projects: install, lint, analyze, test.

**Inputs:**

- `mysql-database` (default: 'testing'): The MySQL database name assigned to the test service
- `php-version` (default: '8.5'): The PHP version to use
- `postgres-database` (default: 'testing'): The PostgreSQL database name assigned to the test service

**Runs:**

Composer install, Pint check, PHPStan analysis, Rector check, Pest/PHPUnit tests

**Services:**

MySQL 9, PostgreSQL 18, Redis 8

```yaml
jobs:
  ci:
    uses: hosmelq/.github/.github/workflows/php-ci.yml@main
    with:
      mysql-database: my_custom_testing_db
      php-version: '8.5'
```

### JavaScript + PHP CI

Combines JavaScript and PHP CI pipelines for full-stack projects.

**Inputs:**

- `fontawesome-npm-auth-token` (optional): Font Awesome NPM authentication token
- `mysql-database` (default: 'testing'): The MySQL database name assigned to the test service
- `node-version` (default: '22'): The Node.js version to use
- `package-manager` (default: 'bun'): Package manager to use (`bun` or `pnpm`)
- `php-version` (default: '8.5'): The PHP version to use
- `postgres-database` (default: 'testing'): The PostgreSQL database name assigned to the test service
- `working-directory` (default: '.'): Directory to run package manager commands in

**Runs:**

Both JS and PHP CI processes (JavaScript steps use Bun by default)

**Services:**

MySQL 9, PostgreSQL 18, Redis 8

```yaml
jobs:
  ci:
    uses: hosmelq/.github/.github/workflows/js-php-ci.yml@main
    with:
      mysql-database: my_custom_testing_db
      node-version: '22'
      package-manager: bun
      php-version: '8.5'
    secrets:
      fontawesome-npm-auth-token: ${{ secrets.FONTAWESOME_NPM_AUTH_TOKEN }}
```

### PR Labeler

Automatically adds labels to pull requests based on changed files.

**Inputs:**

- None

**Runs:**

File path analysis, label assignment

```yaml
jobs:
  label:
    uses: hosmelq/.github/.github/workflows/labeler.yml@main
```

---

## Individual Actions

### Core Actions

#### `composer-install@main`

Installs PHP dependencies using Composer with intelligent caching and configurable PHP setup.

**Inputs:**

- `php-coverage` (optional, default: 'none'): Coverage driver to use
- `php-extensions` (optional, default: ''): PHP extensions to install
- `php-tools` (optional, default: 'cs2pr'): PHP tools to install
- `php-version` (default: '8.5'): The PHP version to use

**Caching:**

Composer cache directory keyed by composer.lock hash

**Outputs:**

Composer dependencies installed in vendor/

```yaml
- uses: hosmelq/.github/.github/actions/composer-install@main
  with:
    php-coverage: pcov
    php-extensions: mbstring, xml
    php-tools: composer, cs2pr
    php-version: '8.5'
```

#### `npm-install@main`

Installs JavaScript dependencies using Bun (default) or pnpm with caching and FontAwesome Pro support.

**Inputs:**

- `bun-version` (default: 'latest'): Bun version to install when using Bun
- `cache-dependency-path` (optional, default: 'pnpm-lock.yaml'): Path to `pnpm-lock.yaml` used for cache keying
- `fontawesome-npm-auth-token` (optional): Font Awesome NPM authentication token
- `node-version` (default: '22'): The Node.js version to use
- `package-json-file` (optional, default: 'package.json'): Path to `package.json` when it lives outside the repo root
- `package-manager` (default: 'bun'): Package manager to use (`bun` or `pnpm`)
- `working-directory` (default: '.'): Directory to run package manager commands in

**Caching:**

Bun or pnpm store keyed by pnpm-lock.yaml hash

**Outputs:**

Node modules installed in node_modules/

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

Runs Danger JS to provide automated code review on pull requests.

**Inputs:**

- `danger-args` (optional, default: ''): Additional arguments to pass to Danger JS
- `dangerfile` (optional, default: 'dangerfile.js'): Path to the dangerfile
- `node-version` (default: '22'): The Node.js version to use
- `package-manager` (default: 'bun'): Package manager to use (`bun` or `pnpm`)
- `working-directory` (default: '.'): Directory to run package manager commands in

**Runs:**

Bun (default) or pnpm install, danger ci with GitHub token authentication

**Outputs:**

PR comments with automated code review feedback

#### `js-autofix@main`

Automatically fixes JavaScript code style issues using Oxc and Prettier.

**Inputs:**

- `fontawesome-npm-auth-token` (optional): Font Awesome NPM authentication token
- `node-version` (default: '22'): The Node.js version to use
- `package-manager` (default: 'bun'): Package manager to use (`bun` or `pnpm`)
- `working-directory` (default: '.'): Directory to run package manager commands in

**Runs:**

Bun (default) or pnpm install, Oxc fix, Prettier format, autofix-ci

**Outputs:**

Fixed code files, automatic commit if changes made

#### `js-frontend-build@main`

Builds frontend assets using project's build script.

**Inputs:**

- `fontawesome-npm-auth-token` (optional): Font Awesome NPM authentication token
- `node-version` (default: '22'): The Node.js version to use
- `package-manager` (default: 'bun'): Package manager to use (`bun` or `pnpm`)
- `working-directory` (default: '.'): Directory to run package manager commands in

**Runs:**

Bun (default) or pnpm install, build

**Outputs:**

Built frontend application assets

#### `js-lint-check@main`

Runs Oxc linting on JavaScript/TypeScript files and reports issues.

**Inputs:**

- `fontawesome-npm-auth-token` (optional): Font Awesome NPM authentication token
- `node-version` (default: '22'): The Node.js version to use
- `package-manager` (default: 'bun'): Package manager to use (`bun` or `pnpm`)
- `working-directory` (default: '.'): Directory to run package manager commands in

**Runs:**

Bun (default) or pnpm install, lint (Oxc)

**Outputs:**

Lint report, fails on errors

### PHP Actions

#### `php-autofix@main`

Automatically fixes PHP code style using Laravel Pint.

**Inputs:**

- `php-version` (default: '8.5'): The PHP version to use

**Runs:**

Composer install, composer pint, autofix-ci

**Outputs:**

Fixed PHP code files, automatic commit if changes made

#### `composer-normalize-check@main`

Validates that composer.json is properly normalized and formatted.

**Inputs:**

- `php-version` (default: '8.5'): The PHP version to use

**Runs:**

Composer install, composer normalize --dry-run

**Outputs:**

Validation report, fails if composer.json is not normalized

#### `composer-unused-dependencies-check@main`

Analyzes Composer dependencies and reports any unused packages using composer-dependency-analyser.

**Inputs:**

- `php-version` (default: '8.5'): The PHP version to use

**Runs:**

Composer install, vendor/bin/composer-dependency-analyser

**Outputs:**

Unused dependency report, fails if unused dependencies found

#### `php-pint-check@main`

Runs Laravel Pint to check PHP code style without making changes.

**Inputs:**

- `php-version` (default: '8.5'): The PHP version to use

**Runs:**

Composer install, vendor/bin/pint --test

**Outputs:**

Code style report with GitHub annotations on failure, fails on style violations

#### `php-rector-check@main`

Runs Rector to check for available code modernizations without applying them.

**Inputs:**

- `php-version` (default: '8.5'): The PHP version to use

**Runs:**

Composer install, vendor/bin/rector --ansi --dry-run

**Outputs:**

Code quality improvement suggestions, fails if changes needed

#### `phpstan-check@main`

Runs PHPStan static analysis on PHP code.

**Inputs:**

- `php-version` (default: '8.5'): The PHP version to use

**Runs:**

Composer install, vendor/bin/phpstan analyse with checkstyle format

**Outputs:**

Static analysis report with GitHub annotations, fails on errors
