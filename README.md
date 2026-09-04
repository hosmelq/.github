# Reusable GitHub Actions

Reusable composite actions and workflows for JavaScript and PHP/Laravel projects.

This repository exposes composite actions under [`.github/actions`](.github/actions).

The old reusable CI and autofix workflows were removed. Consumers should compose their own workflows from these actions.

## Usage

Pin every `uses:` reference to the latest commit SHA from this repository instead of using `@main`. This keeps consuming workflows reproducible while still allowing intentional updates when this repo changes.

```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v6

  - name: Check PHP code style
    uses: hosmelq/.github/.github/actions/php-pint-check@<commit-sha>
    with:
      php-version: '8.5'
```

Use the full SHA from the latest `main` commit:

```bash
git ls-remote https://github.com/hosmelq/.github.git refs/heads/main
```

## Contract

Composite actions in this repository do not checkout the consumer repository. Each job must run `actions/checkout` once before calling any action that reads project files, installs dependencies, or runs checks.

Keeping checkout in the consumer workflow avoids repeated clean checkouts when a job composes multiple shared actions.

## Actions

### Core

| Action | Purpose |
| --- | --- |
| [`php-setup`](.github/actions/php-setup/action.yml) | Set up PHP, extensions, tools, and coverage. |
| [`composer-install`](.github/actions/composer-install/action.yml) | Run `php-setup`, restore Composer cache, and install Composer dependencies. |
| [`composer-dependencies`](.github/actions/composer-dependencies/action.yml) | Install Composer dependencies, upload `vendor` as an artifact, or download and extract `vendor` from an artifact. |
| [`npm-install`](.github/actions/npm-install/action.yml) | Set up Nub and run `nub ci`. |

### JavaScript

| Action | Purpose |
| --- | --- |
| [`danger-js`](.github/actions/danger-js/action.yml) | Install JS dependencies and run `nubx danger ci`. |
| [`js-autofix`](.github/actions/js-autofix/action.yml) | Run `nub run fix`, `nub run format`, and `autofix-ci/action`. |
| [`js-frontend-build`](.github/actions/js-frontend-build/action.yml) | Install JS dependencies and run `nub run build`. |
| [`js-lint-check`](.github/actions/js-lint-check/action.yml) | Install JS dependencies and run `nub run lint`. |

### PHP

| Action | Purpose |
| --- | --- |
| [`php-autofix`](.github/actions/php-autofix/action.yml) | Install Composer dependencies, run `composer pint`, and run `autofix-ci/action`. |
| [`composer-normalize-check`](.github/actions/composer-normalize-check/action.yml) | Prepare Composer dependencies and run `composer normalize --dry-run`. |
| [`composer-unused-dependencies-check`](.github/actions/composer-unused-dependencies-check/action.yml) | Prepare Composer dependencies and run `vendor/bin/composer-dependency-analyser`. |
| [`php-pint-check`](.github/actions/php-pint-check/action.yml) | Prepare Composer dependencies, run Pint in check mode, and emit annotations on failure. |
| [`php-rector-check`](.github/actions/php-rector-check/action.yml) | Prepare Composer dependencies and run Rector in dry-run mode. |
| [`phpstan-check`](.github/actions/phpstan-check/action.yml) | Prepare Composer dependencies and run PHPStan with `cs2pr` annotations. |

## Inputs

### PHP Inputs

Used by PHP actions unless noted otherwise:

- `artifact-mode` (optional, default: `none`): `none` installs Composer dependencies, `upload` installs dependencies and uploads `php-vendor.tar`, `download` downloads and extracts `php-vendor.tar`.
- `artifact-name` (optional, default: `php-vendor`): workflow artifact name used when `artifact-mode` is `upload` or `download`.
- `php-coverage` (optional, default: `none`)
- `php-extensions` (optional, default: empty)
- `php-tools` (optional, default: `cs2pr`)
- `php-version` (optional, default: `8.5`)
- `pie-extensions` (optional, default: empty): comma-separated PIE extension packages to install.

### JavaScript Inputs

Used by JavaScript actions unless noted otherwise:

- `node-version` (optional, default: `24`)
- `nub-version` (optional, default: `latest`, only for `npm-install`)
- `working-directory` (optional, default: `.`)

### Danger JS Inputs

- `danger-args` (optional, default: empty)
- `dangerfile` (optional, default: `dangerfile.js`)
- `node-version` (optional, default: `24`)
- `working-directory` (optional, default: `.`)

## Composer Dependencies Example

Use `composer-dependencies` when a job needs `vendor` prepared.

With the default `artifact-mode: none`, it installs Composer dependencies:

```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v6

  - name: Prepare Composer dependencies
    uses: hosmelq/.github/.github/actions/composer-dependencies@<commit-sha>
    with:
      php-version: '8.5'
```

### PIE Extensions

Use `pie-extensions` to install comma-separated PIE packages after PHP setup and before Composer dependencies:

```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v6

  - name: Prepare Composer dependencies
    uses: hosmelq/.github/.github/actions/composer-dependencies@<commit-sha>
    with:
      php-version: '8.5'
      pie-extensions: 'hosmelq/ext-anydoc:^0.2.0'
```

With `artifact-mode: upload`, it installs Composer dependencies, archives `vendor` into `php-vendor.tar`, and uploads that artifact:

```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v6

  - name: Prepare Composer dependencies
    uses: hosmelq/.github/.github/actions/composer-dependencies@<commit-sha>
    with:
      artifact-mode: upload
      artifact-name: php-vendor
      php-version: '8.5'
```

With `artifact-mode: download`, it downloads `artifact-name` and extracts `php-vendor.tar`:

```yaml
steps:
  - name: Checkout
    uses: actions/checkout@v6

  - name: Prepare Composer dependencies
    uses: hosmelq/.github/.github/actions/composer-dependencies@<commit-sha>
    with:
      artifact-mode: download
      artifact-name: php-vendor
      php-version: '8.5'
```

PHP check actions accept the same `artifact-mode` and `artifact-name` inputs and pass them to `composer-dependencies` internally.
