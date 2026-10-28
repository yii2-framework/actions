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
- [`testo.yml`](https://github.com/yii2-framework/actions/blob/main/.github/workflows/testo.yml) - Testo testing with coverage.

### Quality Assurance Workflows

- [`composer-require-checker.yml`](https://github.com/yii2-framework/actions/blob/main/.github/workflows/composer-require-checker.yml) - Dependency validation.
- [`ecs.yml`](https://github.com/yii2-framework/actions/blob/main/.github/workflows/ecs.yml) - Easy Coding Standard.
- [`phpstan.yml`](https://github.com/yii2-framework/actions/blob/main/.github/workflows/phpstan.yml) - Static analysis.
- [`quality.yml`](https://github.com/yii2-framework/actions/blob/main/.github/workflows/quality.yml) - Reusable quality checks.
- [`scorecard.yml`](https://github.com/yii2-framework/actions/blob/main/.github/workflows/scorecard.yml) - OpenSSF Scorecard supply-chain security analysis.
- [`security.yml`](https://github.com/yii2-framework/actions/blob/main/.github/workflows/security.yml) - Security checks for GitHub Actions and secrets.

### Utility Actions

- [`actionlint`](https://github.com/yii2-framework/actions/blob/main/actions/actionlint/action.yml) - GitHub Actions workflow linting.
- [`codespell`](https://github.com/yii2-framework/actions/blob/main/actions/codespell/action.yml) - Spelling validation.
- [`editorconfig-checker`](https://github.com/yii2-framework/actions/blob/main/actions/editorconfig-checker/action.yml) - EditorConfig validation.
- [`markdownlint`](https://github.com/yii2-framework/actions/blob/main/actions/markdownlint/action.yml) - Markdown validation.
- [`php-setup`](https://github.com/yii2-framework/actions/blob/main/actions/php-setup/action.yml) - PHP environment setup.
- [`phpunit-runner`](https://github.com/yii2-framework/actions/blob/main/actions/phpunit/action.yml) - Advanced PHPUnit execution.
- [`prettier`](https://github.com/yii2-framework/actions/blob/main/actions/prettier/action.yml) - Markdown and YAML formatting validation.
- [`testo-runner`](https://github.com/yii2-framework/actions/blob/main/actions/testo/action.yml) - Advanced Testo execution.
- [`yamllint`](https://github.com/yii2-framework/actions/blob/main/actions/yamllint/action.yml) - YAML validation.

## Quick start

### Composer Require Checker

```yaml
---
on:
  pull_request: &ignore-paths
    paths-ignore:
      - ".gitattributes"
      - ".gitignore"
      - "CHANGELOG.md"
      - "docs/**"
      - "README.md"

  push: *ignore-paths

name: composer-require-checker

jobs:
  dependency-check:
    uses: yii2-framework/actions/.github/workflows/composer-require-checker.yml@v1
    with:
      command-options: "--config-file=.composer-require-checker.json"
```

### Easy Coding Standard

```yaml
---
on:
  pull_request: &ignore-paths
    paths-ignore:
      - ".gitattributes"
      - ".gitignore"
      - "CHANGELOG.md"
      - "docs/**"
      - "README.md"

  push: *ignore-paths

name: easy-coding-standards

jobs:
  coding-standards:
    uses: yii2-framework/actions/.github/workflows/ecs.yml@v1
    with:
      command-options: "check --ansi --no-progress-bar"
      php-version: '["8.4"]'
```

### Infection Mutation Testing {#infection}

```yaml
---
on:
  pull_request: &ignore-paths
    paths-ignore:
      - ".gitattributes"
      - ".gitignore"
      - "CHANGELOG.md"
      - "docs/**"
      - "README.md"

  push: *ignore-paths

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

### PHPUnit

```yaml
---
on:
  pull_request: &ignore-paths
    paths-ignore:
      - ".gitattributes"
      - ".gitignore"
      - "CHANGELOG.md"
      - "docs/**"
      - "README.md"

  push: *ignore-paths

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

### PHPUnit with Database

```yaml
---
on:
  pull_request: &ignore-paths
    paths-ignore:
      - ".gitattributes"
      - ".gitignore"
      - "CHANGELOG.md"
      - "docs/**"
      - "README.md"

  push: *ignore-paths

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

**Supported Databases:**

| Database   | Docker Image                     | Default Port | Health Check Command     |
| ---------- | -------------------------------- | ------------ | ------------------------ |
| MySQL      | `mysql`                          | 3306         | `mysqladmin ping`        |
| PostgreSQL | `postgres`                       | 5432         | `pg_isready`             |
| SQL Server | `mcr.microsoft.com/mssql/server` | 1433         | `sqlcmd -Q "SELECT 1"`   |
| Oracle     | `gvenzl/oracle-xe`               | 1521         | `sqlplus -S / as sysdba` |

### PHPStan Static Analysis

```yaml
---
on:
  pull_request: &ignore-paths
    paths-ignore:
      - ".gitattributes"
      - ".gitignore"
      - "CHANGELOG.md"
      - "docs/**"
      - "README.md"

  push: *ignore-paths

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

### Testo

```yaml
---
on:
  pull_request: &ignore-paths
    paths-ignore:
      - ".gitattributes"
      - ".gitignore"
      - "CHANGELOG.md"
      - "docs/**"
      - "README.md"

  push: *ignore-paths

name: build

jobs:
  testo:
    uses: yii2-framework/actions/.github/workflows/testo.yml@v2
    secrets:
      AUTH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
      CODECOV_TOKEN: ${{ secrets.CODECOV_TOKEN }}
    with:
      # Composer settings
      composer-command: install
      # Coverage settings
      coverage-driver: pcov
      coverage-format: clover
      # PHP configuration
      extensions: mbstring, intl, pdo_sqlite
      php-ini-values: date.timezone='UTC', memory_limit=-1
      # Operating systems
      os: '["ubuntu-latest", "windows-2022"]'
      # PHP versions to test
      php-version: '["8.2", "8.3", "8.4", "8.5"]'
      # Testo configuration
      testo-configuration: testo.php
      testo-suite: Unit
      testo-type: "!bench"
```

### Linter

```yaml
---
on:
  - pull_request
  - push

name: linter

jobs:
  linter:
    uses: yii2-framework/actions/.github/workflows/quality.yml@main
    permissions:
      contents: read
    # Optional configuration and ignore paths, including former .github/linters files.
    # with:
    #   actionlint-config: .github/linters/actionlint.yml
    #   codespell-config: .github/linters/.codespellrc
    #   codespell-ignore-words-file: .github/linters/codespell-ignored-words.txt
    #   editorconfig-checker-config: .github/linters/.editorconfig-checker.json
    #   markdownlint-config: .github/linters/.markdown-lint.yml
    #   prettier-config: .github/linters/.prettierrc
    #   prettier-ignore-path: .github/linters/.prettierignore
    #   yamllint-config: .github/linters/.yaml-lint.yml
```

### Security

```yaml
---
on:
  - pull_request
  - push

name: security

permissions:
  contents: read

jobs:
  security:
    uses: yii2-framework/actions/.github/workflows/security.yml@main
    permissions:
      contents: read
    # Optional configuration and ignore paths, including former .github/linters files.
    # with:
    #   gitleaks-config: .github/linters/.gitleaks.toml
    #   zizmor-config: .github/linters/zizmor.yml
```

### OpenSSF Scorecard

```yaml
---
on:
  branch_protection_rule:
  push:
    branches:
      - main
  schedule:
    - cron: "30 1 * * 6"

name: scorecard

permissions:
  contents: read

jobs:
  scorecard:
    uses: yii2-framework/actions/.github/workflows/scorecard.yml@main
    permissions:
      contents: read # Required to check out the repository.
      id-token: write # Required to sign published results with the GitHub OIDC token.
      security-events: write # Required to upload SARIF results to code scanning.
    # Optional token with read access, required by the Branch-Protection check for classic branch protection.
    secrets:
      SCORECARD_TOKEN: ${{ secrets.SCORECARD_TOKEN }}
    # Optional configuration.
    # with:
    #   file-mode: git
    #   publish-results: true
    #   upload-sarif: false # Private repositories without GitHub Code Security.
```

- Scorecard analyzes the default branch only, so trigger the workflow with `push` on that branch and `schedule`.
- `file-mode` defaults to `git`, so Scorecard also analyzes paths such as `.github` that `.gitattributes` marks with
  `export-ignore`. The default `archive` mode of the action reads the GitHub tarball and skips those paths, which
  hides workflows, the security policy and token permissions from the checks.
- `publish-results` sends signed results to the OpenSSF Scorecard API, which replaces the weekly public scan and
  enables the badge and the viewer for public repositories. The API verifies the called workflow, so the job keeps a
  literal `ubuntu-latest` runner and only allowlisted steps.
- `upload-sarif` sends the SARIF results to GitHub code scanning, which private repositories only provide with GitHub
  Code Security. Set it to `false` there, otherwise the upload fails with HTTP 403 after the Scorecard run and the
  artifact upload complete.
- `SCORECARD_TOKEN` is optional. An unset secret expands to an empty string and the workflow falls back to
  `github.token`, which reads repository rulesets but not classic branch protection.

```markdown
[![OpenSSF Scorecard](https://api.scorecard.dev/projects/github.com/{owner}/{repo}/badge)](https://scorecard.dev/viewer/?uri=github.com/{owner}/{repo})
```

> **Note**: YAML files should use 2-space indentation. This example shows correct YAML syntax - copy it to your `.github/workflows/*.yml` files as-is.

## Package information

[![GitHub Release](https://img.shields.io/github/v/release/yii2-framework/actions?style=for-the-badge&logo=git&logoColor=white&label=Release)](https://github.com/yii2-framework/actions/releases)

## Quality code

[![Quality](https://img.shields.io/github/actions/workflow/status/yii2-framework/actions/linter.yml?style=for-the-badge&label=Quality&logo=github)](https://github.com/yii2-framework/actions/actions/workflows/linter.yml)
[![OpenSSF Scorecard](https://api.scorecard.dev/projects/github.com/yii2-framework/actions/badge?style=for-the-badge)](https://scorecard.dev/viewer/?uri=github.com/yii2-framework/actions)

## Our social networks

[![Follow on X](https://img.shields.io/badge/-Follow%20on%20X-1DA1F2.svg?style=for-the-badge&logo=x&logoColor=white&labelColor=000000)](https://x.com/Terabytesoftw)

## License

[![License](https://img.shields.io/badge/License-BSD--3--Clause-brightgreen.svg?style=for-the-badge&logo=opensourceinitiative&logoColor=white&labelColor=555555)](LICENSE)
