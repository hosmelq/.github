# Reusable GitHub Actions & Workflows

> Reusable GitHub Actions for **JavaScript** and **PHP/Laravel** projects.

## Prerequisites

**JS projects need:** `package.json`, `pnpm-lock.yaml`

**PHP projects need:** `composer.json`, `pint.json`, `rector.php`, `phpstan.neon`

## Optional Secrets

`FONTAWESOME_NPM_AUTH_TOKEN` - Only needed if your project uses FontAwesome Pro packages

---

## Workflows

### Autofix (JS)

Automatically fixes JavaScript code style issues and commits changes back to PR.

**Inputs:** `node-version` (default: '24'), optional `fontawesome-npm-auth-token`
**Runs:** pnpm install, linting fixes, commit changes

```yaml
jobs:
  autofix:
    uses: hosmelq/.github/.github/workflows/js-autofix.yml@main
    with:
      node-version: '24'
    secrets:
      fontawesome-npm-auth-token: ${{ secrets.FONTAWESOME_NPM_AUTH_TOKEN }}
```

### Autofix (PHP)

Automatically fixes PHP code style with Pint and applies Rector suggestions.

**Inputs:** `php-version` (default: '8.4')
**Runs:** Composer install, Pint fixes, Rector fixes, commit changes

```yaml
jobs:
  autofix:
    uses: hosmelq/.github/.github/workflows/php-autofix.yml@main
    with:
      php-version: '8.4'
```

### Autofix (JS + PHP)

Combines JavaScript and PHP autofix capabilities for full-stack projects.

**Inputs:** `php-version` (default: '8.4'), `node-version` (default: '24'), optional `fontawesome-npm-auth-token`
**Runs:** Both JS and PHP autofix processes

```yaml
jobs:
  autofix:
    uses: hosmelq/.github/.github/workflows/js-php-autofix.yml@main
    with:
      php-version: '8.4'
      node-version: '24'
    secrets:
      fontawesome-npm-auth-token: ${{ secrets.FONTAWESOME_NPM_AUTH_TOKEN }}
```

### JavaScript CI

Runs full CI pipeline for JavaScript projects: install, lint, test, build.

**Inputs:** `node-version` (default: '24'), optional `fontawesome-npm-auth-token`
**Runs:** pnpm install, Oxc linting, frontend build
**Services:** None

```yaml
jobs:
  ci:
    uses: hosmelq/.github/.github/workflows/js-ci.yml@main
    with:
      node-version: '24'
    secrets:
      fontawesome-npm-auth-token: ${{ secrets.FONTAWESOME_NPM_AUTH_TOKEN }}
```

### PHP CI

Runs full CI pipeline for PHP projects: install, lint, analyze, test.

**Inputs:** `php-version` (default: '8.4')
**Runs:** Composer install, Pint check, PHPStan analysis, Rector check, Pest/PHPUnit tests
**Services:** MySQL 9.3, Redis 8.2

```yaml
jobs:
  ci:
    uses: hosmelq/.github/.github/workflows/php-ci.yml@main
    with:
      php-version: '8.4'
```

### JavaScript + PHP CI

Combines JavaScript and PHP CI pipelines for full-stack projects.

**Inputs:** `php-version` (default: '8.4'), `node-version` (default: '24'), optional `fontawesome-npm-auth-token`
**Runs:** Both JS and PHP CI processes
**Services:** MySQL 9.3, Redis 8.2

```yaml
jobs:
  ci:
    uses: hosmelq/.github/.github/workflows/js-php-ci.yml@main
    with:
      php-version: '8.4'
      node-version: '24'
    secrets:
      fontawesome-npm-auth-token: ${{ secrets.FONTAWESOME_NPM_AUTH_TOKEN }}
```

### PR Labeler

Automatically adds labels to pull requests based on changed files.

**Inputs:** None
**Runs:** File path analysis, label assignment

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
- `php-version` (required): The PHP version to use
- `php-coverage` (optional, default: 'none'): Coverage driver to use
- `php-extensions` (optional, default: ''): PHP extensions to install
- `php-tools` (optional, default: 'cs2pr'): PHP tools to install

**Caching:** Composer cache directory keyed by composer.lock hash
**Outputs:** Composer dependencies installed in vendor/

```yaml
- uses: hosmelq/.github/.github/actions/composer-install@main
  with:
    php-version: '8.4'
    php-coverage: pcov
    php-extensions: mbstring, xml
    php-tools: composer, cs2pr
```

#### `pnpm-install@main`

Installs JavaScript dependencies using pnpm with caching and FontAwesome Pro support.

**Inputs:** `node-version` (required), `fontawesome-npm-auth-token` (optional)
**Caching:** pnpm store keyed by pnpm-lock.yaml hash
**Outputs:** Node modules installed in node_modules/

```yaml
- uses: hosmelq/.github/.github/actions/pnpm-install@main
  with:
    node-version: '24'
    fontawesome-npm-auth-token: ${{ secrets.FONTAWESOME_NPM_AUTH_TOKEN }}
```

### JavaScript Actions

#### `danger-js@main`

Runs Danger JS to provide automated code review on pull requests.

**Inputs:** 
- `node-version` (required): The Node.js version to use
- `dangerfile` (optional, default: 'dangerfile.js'): Path to the dangerfile
- `danger-args` (optional, default: ''): Additional arguments to pass to Danger JS

**Runs:** pnpm install, danger ci with GitHub token authentication
**Outputs:** PR comments with automated code review feedback

#### `js-autofix@main`

Automatically fixes JavaScript code style issues using Oxc and Prettier.

**Inputs:** 
- `node-version` (required): The Node.js version to use
- `fontawesome-npm-auth-token` (optional): Font Awesome NPM authentication token

**Runs:** pnpm install, pnpm fix (Oxc), pnpm format (Prettier), autofix-ci
**Outputs:** Fixed code files, automatic commit if changes made

#### `js-frontend-build@main`

Builds frontend assets using project's build script.

**Inputs:** 
- `node-version` (required): The Node.js version to use
- `fontawesome-npm-auth-token` (optional): Font Awesome NPM authentication token

**Runs:** pnpm install, pnpm build
**Outputs:** Built frontend application assets

#### `js-lint-check@main`

Runs Oxc linting on JavaScript/TypeScript files and reports issues.

**Inputs:** 
- `node-version` (required): The Node.js version to use
- `fontawesome-npm-auth-token` (optional): Font Awesome NPM authentication token

**Runs:** pnpm install, pnpm lint (Oxc)
**Outputs:** Lint report, fails on errors

### PHP Actions

#### `php-autofix@main`

Automatically fixes PHP code style using Laravel Pint.

**Inputs:** 
- `php-version` (required): The PHP version to use

**Runs:** Composer install, composer pint, autofix-ci
**Outputs:** Fixed PHP code files, automatic commit if changes made

#### `composer-normalize-check@main`

Validates that composer.json is properly normalized and formatted.

**Inputs:** 
- `php-version` (required): The PHP version to use

**Runs:** Composer install, composer normalize --dry-run
**Outputs:** Validation report, fails if composer.json is not normalized

#### `composer-unused-dependencies-check@main`

Analyzes Composer dependencies and reports any unused packages using composer-dependency-analyser.

**Inputs:** 
- `php-version` (required): The PHP version to use

**Runs:** Composer install, vendor/bin/composer-dependency-analyser
**Outputs:** Unused dependency report, fails if unused dependencies found

#### `php-pint-check@main`

Runs Laravel Pint to check PHP code style without making changes.

**Inputs:** 
- `php-version` (required): The PHP version to use

**Runs:** Composer install, ./vendor/bin/pint --test
**Outputs:** Code style report with GitHub annotations on failure, fails on style violations

#### `php-rector-check@main`

Runs Rector to check for available code modernizations without applying them.

**Inputs:** 
- `php-version` (required): The PHP version to use

**Runs:** Composer install, vendor/bin/rector --ansi --dry-run
**Outputs:** Code quality improvement suggestions, fails if changes needed

#### `phpstan-check@main`

Runs PHPStan static analysis on PHP code.

**Inputs:** 
- `php-version` (required): The PHP version to use

**Runs:** Composer install, vendor/bin/phpstan analyse with checkstyle format
**Outputs:** Static analysis report with GitHub annotations, fails on errors

## What's Included

### JavaScript Support
- **Package Manager**: pnpm with intelligent caching
- **Linting**: Oxc for fast, modern JavaScript/TypeScript linting
- **Formatting**: Prettier for consistent code formatting
- **Build System**: Frontend asset building with pnpm build
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
- **Services**: MySQL 9.3 and Redis 8.2 for testing environments
- **Laravel**: Automatic detection and support for Laravel applications vs packages

### Automation Features
- **Auto-fix**: Automatic code style fixing with commit back to PR
- **PR Labeling**: Automatic labeling based on file changes
- **Concurrency Control**: Smart cancellation of in-progress runs
- **GitHub Annotations**: Inline error reporting in PR reviews
