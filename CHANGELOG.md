# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## v2.0.0 Under development

- feat(linter)!: replace Super-Linter with dedicated reusable quality jobs.
- feat(security): add reusable security checks for zizmor and the official Gitleaks action.
- refactor(linter)!: remove Super-Linter-specific inputs and token secret.
- refactor(quality): keep secret scanning exclusively in `security.yml`.
- refactor(quality): use the official codespell action for spelling checks.
- fix(security): harden reusable workflows and composite actions for zizmor pedantic audits.
- chore(format): add shared Prettier settings for Markdown examples.

## v1.0.8 June 01, 2026

- feat: add `multi_status` input to super-linter workflow for GitHub Actions status reporting.

## v1.0.7 March 28, 2026

- fix!: remove `ecs.php` and `rector.php` files; update default for `download-config` in `ecs.yml`.

## v1.0.6 March 18, 2026

- ci(actions): enable `opcache.enable_cli=1` when `coverage-driver=none` and migrate PHPUnit test results uploads to `codecov/codecov-action@v5`.

## v1.0.5 February 28, 2026

- fix(codeception): update test output path to match actual output directory.

## v1.0.4 February 2, 2026

- fix(Codecov): correct parameter name from `file` to `files`.

## v1.0.3 January 20, 2026

- fix(ecs): add `PhpdocTypesOrderFixer` configuration and update method order in `ECS` configuration.
- feat(ecs): add `OrderedTypesFixer` configuration to enhance class type ordering in `ECS` configuration.
- feat(ci): add `PHP 8.5` support in GitHub workflows.

## v1.0.2 January 3, 2026

- chore(repository): remove outdated community guidelines and templates.
- feat(ecs): add `ECS` configuration file and update workflow to download it.
- feat(rector): add initial configuration with `PHP 8.1` support.

## v1.0.1 October 8, 2025

- fix(codeception): remove unnecessary action permissions monitoring step in `codeception.yml`.

## v1.0.0 October 6, 2025

- feat: initial release
- feat(super-linter): add biome validation options to `super-linter.yml` workflow.
- fix(workflows): simplify workflow triggers by using an anchor for `paths-ignore` in multiple workflows.
