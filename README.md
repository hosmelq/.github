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
Installs PHP dependencies using Composer with intelligent caching.

**Inputs:** `php-version` (required)
**Caching:** Vendor directory keyed by composer.lock hash
**Outputs:** Composer dependencies installed in vendor/

```yaml
- uses: hosmelq/.github/.github/actions/composer-install@main
  with:
    php-version: '8.4'
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

#### `js-autofix@main`
Automatically fixes JavaScript code style issues using configured linter.

**Inputs:** `node-version` (required), `fontawesome-npm-auth-token` (optional)
**Runs:** pnpm install, linting fixes
**Outputs:** Fixed code files, commit if changes made

#### `js-frontend-build@main`
Builds frontend assets using project's build script.

**Inputs:** `node-version` (required), `fontawesome-npm-auth-token` (optional)
**Runs:** pnpm install, pnpm run build
**Outputs:** Built assets in public/build or dist/

#### `js-lint-check@main`
Runs Oxc linting on JavaScript/TypeScript files and reports issues.

**Inputs:** `node-version` (required), `fontawesome-npm-auth-token` (optional)
**Runs:** pnpm install, Oxc linting
**Outputs:** Lint report, fails on errors

### PHP Actions

#### `php-autofix@main`
Automatically fixes PHP code style with Pint and applies Rector suggestions.

**Inputs:** `php-version` (required)
**Runs:** Composer install, Pint fixes, Rector fixes
**Outputs:** Fixed code files, commit if changes made

#### `composer-normalize-check@main`
Validates that composer.json is properly normalized and formatted.

**Inputs:** `php-version` (required)
**Runs:** Composer install, composer normalize --dry-run
**Outputs:** Validation report, fails if not normalized

#### `composer-unused-dependencies-check@main`
Analyzes Composer dependencies and reports any unused packages.

**Inputs:** `php-version` (required)
**Runs:** Composer install, dependency analysis
**Outputs:** Unused dependency report, fails if unused deps found

#### `php-pint-check@main`
Runs Laravel Pint to check PHP code style without making changes.

**Inputs:** `php-version` (required)
**Runs:** Composer install, Pint dry-run
**Outputs:** Code style report, fails on style violations

#### `php-rector-check@main`
Runs Rector to check for available code modernizations without applying them.

**Inputs:** `php-version` (required)
**Runs:** Composer install, Rector dry-run
**Outputs:** Modernization suggestions, fails if changes needed

#### `phpstan-check@main`
Runs PHPStan static analysis on PHP code.

**Inputs:** `php-version` (required)
**Runs:** Composer install, PHPStan analysis
**Outputs:** Static analysis report, fails on errors

## What's Included

- **JS**: Oxc linting, pnpm with caching
- **PHP**: Pint, PHPStan, Rector, Pest/PHPUnit with MySQL 9.3/Redis 8.2, Laravel support
