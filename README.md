# Create Release PR Action

A GitHub Action to create release PRs with automatic version bump, CHANGELOG generation, and version file updates.

## Features

- Automatic version bump based on Conventional Commits
  - `feat!:` or `BREAKING CHANGE:` → major
  - `feat:` → minor
  - `fix:` → patch
- CHANGELOG generation using git-cliff
- Automatic updates to `Cargo.toml` and `package.json`
- Creates release branch (`release/YYYYMMDD-HHMMSS`)
- Creates PR to target branch

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
|------|-------------|----------|-------|
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

## License

MIT
