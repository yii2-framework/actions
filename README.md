<!-- markdownlint-disable MD041 -->
<p align="center">
    <picture>
        <source media="(prefers-color-scheme: dark)" srcset="https://www.yiiframework.com/image/design/logo/yii3_full_for_dark.svg">
        <source media="(prefers-color-scheme: light)" srcset="https://www.yiiframework.com/image/design/logo/yii3_full_for_light.svg">
        <img src="https://www.yiiframework.com/image/design/logo/yii3_full_for_dark.svg" alt="Yii Framework" width="80%">
    </picture>
    <h1 align="center">Reusable GitHub Actions</h1>
    <br>
</p>
<!-- markdownlint-enable MD041 -->

A comprehensive collection of reusable GitHub Actions and workflows specifically designed for PHP projects. Streamline
your CI/CD pipeline with battle-tested, configurable workflows for testing, static analysis, and code quality checks.

## Features

<picture>
    <source media="(min-width: 768px)" srcset="./docs/svgs/features.svg">
    <img src="./docs/svgs/features-mobile.svg" alt="Feature Overview" style="width: 100%;">
</picture>

## Available Workflows

### Testing Workflows

- [`codeception.yml`](https://github.com/yii2-framework/actions/blob/main/.github/workflows/codeception.yml) - Codeception testing framework.
- [`infection.yml`](https://github.com/yii2-framework/actions/blob/main/.github/workflows/infection.yml) - Mutation testing with Infection.
- [`phpunit-database.yml`](https://github.com/yii2-framework/actions/blob/main/.github/workflows/phpunit-database.yml) - PHPUnit with database services.
- [`phpunit.yml`](https://github.com/yii2-framework/actions/blob/main/.github/workflows/phpunit.yml) - PHPUnit testing with coverage.

### Quality Assurance Workflows

- [`composer-require-checker.yml`](https://github.com/yii2-framework/actions/blob/main/.github/workflows/composer-require-checker.yml) - Dependency validation.
- [`ecs.yml`](https://github.com/yii2-framework/actions/blob/main/.github/workflows/ecs.yml) - Easy Coding Standard.
- [`phpstan.yml`](https://github.com/yii2-framework/actions/blob/main/.github/workflows/phpstan.yml) - Static analysis.
- [`super-linter.yml`](https://github.com/yii2-framework/actions/blob/main/.github/workflows/super-linter.yml) - Super Linter for multi-language linting.

### Utility Actions

- [`php-setup`](https://github.com/yii2-framework/actions/blob/main/actions/php-setup/action.yml) - PHP environment setup.
- [`phpunit-runner`](https://github.com/yii2-framework/actions/blob/main/actions/phpunit/action.yml) - Advanced PHPUnit execution.

## Quick start

### Composer Require Checker

<!-- editorconfig-checker-disable -->
<!-- prettier-ignore-start -->
```yaml
---
on:
  pull_request:
    paths-ignore:
      - ".gitattributes"
      - ".gitignore"
      - "CHANGELOG.md"
      - "docs/**"
      - "README.md"

  push:
    paths-ignore:
      - ".gitattributes"
      - ".gitignore"
      - "CHANGELOG.md"
      - "docs/**"
      - "README.md"

name: composer-require-checker

jobs:
  dependency-check:
    uses: yii2-framework/actions/.github/workflows/composer-require-checker.yml@v1
    with:
      command-options: "--config-file=.composer-require-checker.json"
```
<!-- prettier-ignore-end -->
<!-- editorconfig-checker-enable -->

### Easy Coding Standard

<!-- editorconfig-checker-disable -->
<!-- prettier-ignore-start -->
```yaml
---
on:
  pull_request:
    paths-ignore:
      - ".gitattributes"
      - ".gitignore"
      - "CHANGELOG.md"
      - "docs/**"
      - "README.md"

  push:
    paths-ignore:
      - ".gitattributes"
      - ".gitignore"
      - "CHANGELOG.md"
      - "docs/**"
      - "README.md"

name: easy-coding-standards

jobs:
  coding-standards:
    uses: yii2-framework/actions/.github/workflows/ecs.yml@v1
    with:
      command-options: "check --ansi --no-progress-bar"
      php-version: '["8.4"]'
```
<!-- prettier-ignore-end -->
<!-- editorconfig-checker-enable -->

### Infection Mutation Testing {#infection}

<!-- editorconfig-checker-disable -->
<!-- prettier-ignore-start -->
```yaml
---
on:
  pull_request:
    paths-ignore:
      - ".gitattributes"
      - ".gitignore"
      - "CHANGELOG.md"
      - "docs/**"
      - "README.md"

  push:
    paths-ignore:
      - ".gitattributes"
      - ".gitignore"
      - "CHANGELOG.md"
      - "docs/**"
      - "README.md"

name: mutation-testing

jobs:
  mutation-testing:
    uses: yii2-framework/actions/.github/workflows/reusable-infection.yml@v2
    secrets:
      STRYKER_DASHBOARD_API_KEY: ${{ secrets.STRYKER_DASHBOARD_API_KEY }}
    with:
      # Infection configuration
      command-options: "--threads=4 --min-msi=80"
      command-coverage-options: --with-uncovered
      # PHPStan integration
      phpstan: true
```
<!-- prettier-ignore-end -->
<!-- editorconfig-checker-enable -->

### PHPUnit

<!-- editorconfig-checker-disable -->
<!-- prettier-ignore-start -->
```yaml
---
on:
  pull_request:
    paths-ignore:
      - ".gitattributes"
      - ".gitignore"
      - "CHANGELOG.md"
      - "docs/**"
      - "README.md"

  push:
    paths-ignore:
      - ".gitattributes"
      - ".gitignore"
      - "CHANGELOG.md"
      - "docs/**"
      - "README.md"

name: build

jobs:
  phpunit:
    uses: yii2-framework/actions/.github/workflows/phpunit.yml@v1
    secrets:
      AUTH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      CODECOV_TOKEN: ${{ secrets.CODECOV_TOKEN }}
    with:
      # Composer settings
      composer-command: composer install --prefer-dist --no-progress
      # Coverage settings
      coverage-driver: pcov
      coverage-format: clover
      # PHP configuration
      extensions: mbstring, intl, pdo_sqlite
      ini-values: date.timezone='UTC', memory_limit=-1
      # Operating systems
      os: '["ubuntu-latest", "windows-2022"]'
      # PHP versions to test
      php-version: '["8.1", "8.2", "8.3", "8.4"]'
      # PHPUnit configuration
      phpunit-configuration: phpunit.xml
      phpunit-exclude-group: integration
      phpunit-group: unit
```
<!-- prettier-ignore-end -->
<!-- editorconfig-checker-enable -->

### PHPUnit with Database

<!-- editorconfig-checker-disable -->
<!-- prettier-ignore-start -->
```yaml
---
on:
  pull_request:
    paths-ignore:
      - ".gitattributes"
      - ".gitignore"
      - "CHANGELOG.md"
      - "docs/**"
      - "README.md"

  push:
    paths-ignore:
      - ".gitattributes"
      - ".gitignore"
      - "CHANGELOG.md"
      - "docs/**"
      - "README.md"

name: build-mysql

jobs:
  database-tests:
    uses: yii2-framework/actions/.github/workflows/phpunit-database.yml@v1
    secrets:
      CODECOV_TOKEN: ${{ secrets.CODECOV_TOKEN }}
    with:
      # Database configuration
      database-env: |
        {
          "MYSQL_ROOT_PASSWORD": "root",
          "MYSQL_DATABASE": "test"
        }
        database-health-cmd: "mysqladmin ping"
        database-health-retries: 3
        database-image: mysql
        database-port: 3306
        database-type: mysql
        database-versions: '["8.0", "8.4", "latest"]'
        extensions: pdo, pdo_mysql
        php-version: '["8.4"]'
        phpunit-group: mysql
```
<!-- prettier-ignore-end -->
<!-- editorconfig-checker-enable -->

**Supported Databases:**

| Database   | Docker Image                     | Default Port | Health Check Command     |
| ---------- | -------------------------------- | ------------ | ------------------------ |
| MySQL      | `mysql`                          | 3306         | `mysqladmin ping`        |
| PostgreSQL | `postgres`                       | 5432         | `pg_isready`             |
| SQL Server | `mcr.microsoft.com/mssql/server` | 1433         | `sqlcmd -Q "SELECT 1"`   |
| Oracle     | `gvenzl/oracle-xe`               | 1521         | `sqlplus -S / as sysdba` |

### PHPStan Static Analysis

<!-- editorconfig-checker-disable -->
<!-- prettier-ignore-start -->
```yaml
---
on:
  pull_request:
    paths-ignore:
      - ".gitattributes"
      - ".gitignore"
      - "CHANGELOG.md"
      - "docs/**"
      - "README.md"

  push:
    paths-ignore:
      - ".gitattributes"
      - ".gitignore"
      - "CHANGELOG.md"
      - "docs/**"
      - "README.md"

name: static-analysis

jobs:
  static-analysis:
    uses: yii2-framework/actions/.github/workflows/phpstan.yml@v1
    with:
      # PHPStan configuration
      configuration: phpstan.neon
      command-options: "analyse --error-format=checkstyle | cs2pr"
      # Environment
      php-version: '["8.4"]'
      tools: cs2pr
```
<!-- prettier-ignore-end -->
<!-- editorconfig-checker-enable -->

### Super Linter

<!-- editorconfig-checker-disable -->
<!-- prettier-ignore-start -->
```yaml
---
on:
  - pull_request
  - push

name: linter

jobs:
  linter:
    uses: yii2-framework/actions/.github/workflows/super-linter.yml@main
    permissions:
      checks: write
      contents: read
      statuses: write    
    secrets:
      AUTH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```
<!-- prettier-ignore-end -->
<!-- editorconfig-checker-enable -->

> **Note**: YAML files should use 2-space indentation. This example shows correct YAML syntax - copy it to your `.github/workflows/*.yml` files as-is.

## Package information

[![GitHub Release](https://img.shields.io/github/v/release/yii2-framework/actions?style=for-the-badge&logo=git&logoColor=white&label=Release)](https://github.com/yii2-framework/actions/releases)

## Quality code

[![Super-Linter](https://img.shields.io/github/actions/workflow/status/yii2-framework/actions/linter.yml?style=for-the-badge&label=Super-Linter&logo=github)](https://github.com/yii2-framework/actions/actions/workflows/linter.yml)

## Our social networks

[![Follow on X](https://img.shields.io/badge/-Follow%20on%20X-1DA1F2.svg?style=for-the-badge&logo=x&logoColor=white&labelColor=000000)](https://x.com/Terabytesoftw)

## License

[![License](https://img.shields.io/badge/License-BSD--3--Clause-brightgreen.svg?style=for-the-badge&logo=opensourceinitiative&logoColor=white&labelColor=555555)](LICENSE)
