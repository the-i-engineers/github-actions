# github-actions

Reusable GitHub Actions workflows and composite actions for the [the-i-engineers](https://github.com/the-i-engineers) organisation.

## Reusable Workflows

| Workflow | Description |
|----------|-------------|
| [`lint-pr-title.yml`](.github/workflows/lint-pr-title.yml) | Validate PR titles against [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) |
| [`scheduled-tag-release.yml`](.github/workflows/scheduled-tag-release.yml) | Create semver or weekly tags on a schedule |
| [`terraform-stack.yml`](.github/workflows/terraform-stack.yml) | Plan and apply a single Terraform stack with manual approval |
| [`terraform-required-check.yml`](.github/workflows/terraform-required-check.yml) | Required-check aggregator for Terraform stack workflows |

## Composite Actions

| Action | Description |
|--------|-------------|
| [`lint-pr-title`](.github/actions/lint-pr-title/action.yml) | Composite action version of the PR title linter |
| [`http-smoke-test`](.github/actions/http-smoke-test/action.yml) | HTTP health check with retry and glob status matching |
| [`smoke-test-collect`](.github/actions/smoke-test-collect/action.yml) | Aggregate smoke test outcomes into a markdown table |
| [`smoke-test-summary`](.github/actions/smoke-test-summary/action.yml) | Post smoke test results as a PR comment |

## Usage

Pin callers to a specific immutable tag. [Dependabot](.github/dependabot.yml) opens automatic bump PRs.

```yaml
jobs:
  lint-pr-title:
    uses: the-i-engineers/github-actions/.github/workflows/lint-pr-title.yml@v1.0.0
    permissions:
      pull-requests: write
    secrets:
      token: ${{ secrets.GITHUB_TOKEN }}
```

See each workflow file for its full interface and usage example.

## Versioning

Tags are created automatically by [`tag-release.yml`](.github/workflows/tag-release.yml) on every push to `main` that modifies a reusable workflow or composite action file.

Bump rules follow [Conventional Commits](docs/contributing/conventional-commits.md):

| Commit | Bump |
|--------|------|
| `type!:` or `BREAKING CHANGE:` in footer | **major** |
| `feat:` / `feat(scope):` | **minor** |
| anything else | **patch** |
