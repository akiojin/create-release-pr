# Create Release PR Action

A GitHub Action to create release PRs with automatic version bump, CHANGELOG generation, and version file updates.

## Features

- Automatic version bump based on Conventional Commits
  - `feat!:` or `BREAKING CHANGE:` → major
  - `feat:` → minor
  - `fix:` → patch
- CHANGELOG generation using git-cliff
- Automatic version updates for multiple package managers
- Creates release branch (`release/YYYYMMDD-HHMMSS`)
- Creates PR to target branch

## Supported Version Files

This action automatically detects and updates version files for the following ecosystems:

| Ecosystem | File(s) |
|-----------|---------|
| **Rust** | `Cargo.toml` |
| **Node.js/npm** | `package.json` |
| **Python** | `pyproject.toml`, `setup.py` |
| **PHP** | `composer.json` |
| **Dart/Flutter** | `pubspec.yaml` |
| **Ruby** | `version.rb`, `*.gemspec` |
| **Java (Maven)** | `pom.xml` |
| **Java/Kotlin (Gradle)** | `build.gradle`, `build.gradle.kts` |
| **Swift** | `Package.swift` |
| **.NET/C#** | `Directory.Build.props`, `*.csproj` |
| **Helm** | `Chart.yaml` |

Files are only updated if they exist in the repository.

## Usage

```yaml
name: Prepare Release

on:
  workflow_dispatch:

permissions:
  contents: write
  pull-requests: write

jobs:
  create-release-pr:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          ref: develop
          fetch-depth: 0
          token: ${{ secrets.GITHUB_TOKEN }}

      - uses: akiojin/create-release-pr@v1
        with:
          source-branch: develop
          target-branch: main
          github-token: ${{ secrets.GITHUB_TOKEN }}
```

## Inputs

| Name | Description | Required | Default |
|------|-------------|----------|---------|
| `source-branch` | Source branch to create release from | No | `develop` |
| `target-branch` | Target branch for the release PR | No | `main` |
| `github-token` | GitHub token for creating PR | Yes | - |

## Outputs

| Name | Description |
|------|-------------|
| `version` | New version number |
| `pr-number` | Created PR number |
| `pr-url` | Created PR URL |

## git-cliff Configuration

For customized CHANGELOG generation, create a `cliff.toml` in your repository root. See [git-cliff documentation](https://git-cliff.org/docs/configuration) for details.

If no `cliff.toml` is found, a simple fallback changelog format is used.

## Release Flow

```
workflow_dispatch trigger
        ↓
Checkout source branch (e.g., develop)
        ↓
Analyze Conventional Commits → Determine version bump
        ↓
Create release/YYYYMMDD-HHMMSS branch
        ↓
Update version files (Cargo.toml, package.json, etc.)
Update CHANGELOG.md (git-cliff)
        ↓
Commit & Push to release branch
        ↓
Create PR to target branch (e.g., main)
        ↓
PR merge triggers your release workflow
```

## License

MIT
