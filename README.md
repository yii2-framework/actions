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

- [`assets.yml`](https://github.com/yii2-framework/actions/blob/main/.github/workflows/assets.yml) - Front-end asset commands with generated file drift detection.
- [`codeception.yml`](https://github.com/yii2-framework/actions/blob/main/.github/workflows/codeception.yml) - Codeception testing framework.
- [`infection.yml`](https://github.com/yii2-framework/actions/blob/main/.github/workflows/infection.yml) - Mutation testing with Infection.
- [`phpunit-database.yml`](https://github.com/yii2-framework/actions/blob/main/.github/workflows/phpunit-database.yml) - PHPUnit with database services.
- [`phpunit.yml`](https://github.com/yii2-framework/actions/blob/main/.github/workflows/phpunit.yml) - PHPUnit testing with coverage.
- [`playwright.yml`](https://github.com/yii2-framework/actions/blob/main/.github/workflows/playwright.yml) - Playwright browser testing.
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
- [`node-setup`](https://github.com/yii2-framework/actions/blob/main/actions/node-setup/action.yml) - Node.js environment setup.
- [`php-setup`](https://github.com/yii2-framework/actions/blob/main/actions/php-setup/action.yml) - PHP environment setup.
- [`phpunit-runner`](https://github.com/yii2-framework/actions/blob/main/actions/phpunit/action.yml) - Advanced PHPUnit execution.
- [`playwright`](https://github.com/yii2-framework/actions/blob/main/actions/playwright/action.yml) - Playwright browser installation.
- [`prettier`](https://github.com/yii2-framework/actions/blob/main/actions/prettier/action.yml) - Markdown and YAML formatting validation.
- [`testo-runner`](https://github.com/yii2-framework/actions/blob/main/actions/testo/action.yml) - Advanced Testo execution.
- [`yamllint`](https://github.com/yii2-framework/actions/blob/main/actions/yamllint/action.yml) - YAML validation.

## Quick start

### Assets

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

name: assets

permissions:
  contents: read

jobs:
  assets:
    uses: yii2-framework/actions/.github/workflows/assets.yml@v2
    with:
      # Node.js settings
      node-version-file: .nvmrc
      install-command: npm ci
      # Commands, run in order
      commands: |
        npm run test:js
        npm run test:mutation
        npm run build
      # Committed generated files that the build must reproduce
      drift-paths: |
        src/asset/**/*.min.css
        src/asset/**/*.min.js
      # Optional PHP and Composer dependencies, for JavaScript tests that read files from vendor/.
      # php-version: "8.3"
```

- `commands` runs each non-empty line in its own `bash -euo pipefail` shell and log group, in order. The job stops at
  the first line that fails, and the remaining lines do not run.
- `drift-paths` lists Git pathspecs, passed verbatim to `git status --porcelain --untracked-files=all` after the
  commands. The job fails and lists the files when a tracked file under those paths was modified or deleted, or an
  untracked file appeared, which means that a fresh build does not reproduce the committed generated files.
  `drift-include-ignored: true` adds `--ignored=matching`, so ignored files under those paths count as drift too.
- `node-version` selects the Node.js version directly and takes precedence. `node-version-file` (default `.nvmrc`) is
  read only when `node-version` is empty, and the job fails when that file does not exist. With both empty, the Node.js
  version preinstalled on the runner is used.
- `cache` (default `npm`) restores the package manager cache keyed on the lock file, so it requires a committed lock
  file. `npm` is the only supported value, because setup-node asks the package manager itself for its cache directory
  and these workflows install nothing before it. Set it to an empty string to disable the cache; with `cache: ""`
  setup-node runs no package manager, so a project that uses another package manager can pass its own
  `install-command`, provided that the command makes that package manager available first.
- PHP is set up only when `php-version` is given: the job then installs that single PHP version without a coverage
  driver and runs `composer <composer-command> <composer-flags>` before Node.js. With the default empty value, PHP and
  Composer are skipped entirely.

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

### Playwright

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

name: browser

permissions:
  contents: read

jobs:
  playwright:
    uses: yii2-framework/actions/.github/workflows/playwright.yml@v2
    with:
      # PHP serves the browser fixtures (the Playwright configuration starts `php -S`).
      php-version: "8.3"
      # Node.js settings
      node-version-file: .nvmrc
      install-command: npm ci --ignore-scripts
      # Playwright browsers
      browsers: chromium
      # Commands, run in order
      commands: |
        npm run build
        npm run test:browser
      # Reports and traces, uploaded only when the job fails
      artifact-name: playwright-report
      artifact-path: |
        runtime/playwright/
```

- Browsers are installed by running `node_modules/.bin/playwright install` from the working directory, the bin that
  `install-command` linked for the project dependency `playwright` or `@playwright/test`. The job fails when that file
  does not exist; it never falls back to npx, the npm cache or a `playwright` executable on `PATH`. `only-shell: true`
  adds `--only-shell` and `with-deps: false` drops `--with-deps`.
- `commands` (default `npx playwright test`), `node-version`, `node-version-file`, `cache` and the PHP inputs behave as
  in [Assets](#assets): the lines run in order and the job stops at the first one that fails, and PHP is set up only
  when `php-version` is given. A line can set environment variables inline, for example
  `TIME_PICKER_BROWSERS="chromium firefox webkit" npm test`.
- `artifact-path` is uploaded only when the job fails, as `<artifact-name>-<os>`. Paths that do not exist produce a
  warning, not a failure.
- A caller may put its own `strategy.matrix` on the job that calls `assets.yml` or `playwright.yml`. It must then pass
  a `concurrency-group` and, for `playwright.yml`, an `artifact-name` that include the matrix values; otherwise all
  cells share one concurrency group and cancel each other, and their artifacts collide.

```yaml
jobs:
  consumer:
    strategy:
      fail-fast: false
      matrix:
        dependencies: [highest, lowest]
    uses: yii2-framework/actions/.github/workflows/playwright.yml@v2
    with:
      artifact-name: consumer-${{ matrix.dependencies }}
      concurrency-group: consumer-${{ matrix.dependencies }}-${{ github.workflow }}-${{ github.ref }}
      commands: |
        TIME_PICKER_DEPENDENCIES=${{ matrix.dependencies }} bash scripts/verify-consumer.sh "2.0.55"
      php-version: "8.3"
```

- Jobs that need a different shape can use the two composite actions directly. `node-setup` does not check out the
  repository, and `playwright` expects the dependencies to be installed.

```yaml
steps:
  - name: Checkout repository.
    uses: actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7.0.1
    with:
      persist-credentials: false

  - name: Setup Node.js Environment.
    uses: yii2-framework/actions/actions/node-setup@v2
    with:
      install-command: npm ci --ignore-scripts
      node-version: "26"

  - name: Install Playwright browsers.
    uses: yii2-framework/actions/actions/playwright@v2
    with:
      browsers: chromium firefox webkit
      only-shell: true
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

### Platform requirements per PHP version

The `ignore-platform-reqs-php-version` input adds `--ignore-platform-reqs` to the Composer command only for the listed
PHP versions, while `ignore-platform-reqs` keeps applying it to every version. Listed versions that are not used by the
workflow have no effect. In Assets and Playwright, both inputs apply only when `php-version` enables PHP setup.

Both inputs are available in the `assets.yml`, `codeception.yml`, `composer-require-checker.yml`, `ecs.yml`,
`infection.yml`, `phpstan.yml`, `phpunit.yml`, `phpunit-database.yml`, `playwright.yml` and `testo.yml` workflows.

```yaml
jobs:
  phpunit:
    uses: yii2-framework/actions/.github/workflows/phpunit.yml@v2
    with:
      ignore-platform-reqs-php-version: "[8.6]"
```

### Linter

```yaml
---
on:
  - pull_request
  - push

name: quality

permissions:
  contents: read

jobs:
  quality:
    uses: yii2-framework/actions/.github/workflows/quality.yml@main
    # Optional additions on top of the shared configuration.
    # with:
    #   codespell-ignore-words-list: foo,bar
    #   codespell-skip: ./resource/*
    #   editorconfig-checker-exclude: ^tests/runtime/
    # Optional configuration paths; a path replaces the shared configuration of that tool.
    #   actionlint-config: .github/linters/actionlint.yml
    #   codespell-config: .github/linters/.codespellrc
    #   editorconfig-checker-config: .github/linters/.editorconfig-checker.json
    #   markdownlint-config: .github/linters/.markdown-lint.yml
    #   prettier-config: .prettierrc.json
    #   yamllint-config: .github/linters/.yamllint.yml
    # Optional ignore path; it replaces the shared Prettier exclusions, not the formatting rules.
    #   prettier-ignore-path: .prettierignore
```

codespell, EditorConfig Checker, markdownlint-cli2 and Prettier use the configuration bundled with the referenced
workflow commit, so the calling repository needs no `.github/linters` files, `.prettierrc.json` or `.prettierignore`.
Its `.editorconfig` still defines the EditorConfig rules. actionlint and yamllint apply built-in defaults.

The shared configuration is on by default and replaces the configuration files that Prettier and EditorConfig Checker
would otherwise discover in the calling repository. Pass `prettier-config` or `editorconfig-checker-config` to keep
using them, or set `use-shared-config: false` to run the tools without the shared configuration.

The shared codespell rules skip non-English Yii translation catalogs, generated mutation reports and JavaScript/CSS
source maps, and specific generated RPC or upstream frontend files. English catalogs, tests, documentation, and
license files remain in scope. Use `codespell-skip` for additional repository-specific paths.

The shared Prettier exclusions cover the default Markdown and YAML scan; `prettier-ignore-path` or `prettier-patterns`
replace the exclusions only, not the formatting rules.

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
