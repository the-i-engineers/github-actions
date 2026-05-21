# AGENTS.md — github-actions

This repository provides shared reusable GitHub Actions workflows and composite actions for the [the-i-engineers](https://github.com/the-i-engineers) organisation. Read `README.md` first for the full catalogue.

## Scope

This repo covers **infrastructure and DevOps CI** only:

- Terraform plan/apply automation (`terraform-stack`, `terraform-required-check`)
- PR title linting against Conventional Commits (`lint-pr-title`)
- Scheduled semantic or weekly tag/release automation (`scheduled-tag-release`)
- HTTP smoke tests and result aggregation (`http-smoke-test`, `smoke-test-collect`, `smoke-test-summary`)

**Out of scope here:** Java/Gradle application CI (build, test, publish, container image). That belongs in [`the-i-engineers/reusable-workflows`](https://github.com/the-i-engineers/reusable-workflows).

## Default branch

`main` — all changes land via pull request. Never push directly to `main`.

## Development workflow

Always work in a `git worktree` — never on the main checkout:

```bash
cd github-actions/
git fetch origin main && git pull --ff-only origin main
git worktree add ../../worktrees/github-actions-<branch-slug> -b <branch> origin/main
cd ../../worktrees/github-actions-<branch-slug>
# work here
```

Place worktrees in a `worktrees/` directory **beside** the `github-actions/` checkout (i.e. a sibling, not inside it).

## Testing changes

Before opening a PR, verify your changes locally:

- **Workflow/action linting:** `actionlint` runs automatically on PRs via `lint-workflows.yml`
- **Integration test:** `test.yml` exercises all composite actions and the reusable `scheduled-tag-release` and `terraform-required-check` workflows. Trigger it manually on your branch via `gh workflow run test.yml --ref <branch>` or push a PR.
- **Lint PR title action:** `caller-lint-pr-title.yml` tests the composite action end-to-end on every PR.

Do not ship a new workflow or action without a corresponding test case in `test.yml`.

## Versioning

Tags are created **automatically** by `tag-release.yml` on every push to `main` that modifies a reusable workflow or composite action file.

Bump rules follow [Conventional Commits](docs/contributing/conventional-commits.md):

| Commit type | Version bump |
|-------------|-------------|
| `type!:` or `BREAKING CHANGE:` in footer | **major** |
| `feat:` | **minor** |
| anything else | **patch** |

Tags are immutable (`vX.Y.Z`). No mutable `v1` alias is maintained. Callers must pin to a specific tag and rely on Dependabot to bump.

## Pull requests

- **Title:** follow [Conventional Commits](docs/contributing/conventional-commits.md) — `type(scope): description`
- **Body:** describe what changed and why; include a test evidence link (workflow run URL)
- **One concern per PR** — do not bundle multiple workflow/action changes
- **Check for an open PR** before pushing: `gh pr list --head <branch>`

## Conventional Commits types used in this repo

| Type | Use for |
|------|---------|
| `feat` | new workflow, new action, new input |
| `fix` | bug in existing workflow/action |
| `refactor` | restructure without behaviour change |
| `docs` | README, AGENTS.md, contributing docs |
| `chore` | dependency bumps, CI for this repo itself |
| `perf` | runtime optimisation |

Breaking changes: append `!` to the type or add `BREAKING CHANGE:` in the commit footer.

## Adding a new reusable workflow

1. Create `.github/workflows/<name>.yml` with `on: workflow_call`
2. Add a full input/secret table in the workflow file header comment
3. Add a test job in `.github/workflows/test.yml`
4. Add a row to the Reusable Workflows table in `README.md`
5. Update the path trigger list in `tag-release.yml` if needed

## Adding a new composite action

1. Create `.github/actions/<name>/action.yml`
2. Add a test job in `.github/workflows/test.yml`
3. Add a row to the Composite Actions table in `README.md`
4. The `tag-release.yml` path trigger already watches `.github/actions/**` — no change needed

## Relationship to other shared-action repos

| Repo | Scope |
|------|-------|
| `github-actions` (this repo) | Infra/DevOps CI: Terraform, PR linting, smoke tests, release |
| [`reusable-workflows`](https://github.com/the-i-engineers/reusable-workflows) | Application CI: Java/Gradle build, test, publish, container image |
| [`template-gradle`](https://github.com/the-i-engineers/template-gradle) | Gradle toolchain distributor; ships workflow templates that call `reusable-workflows` |

If you are unsure which repo a new workflow belongs in, ask: is it consumed by application teams building Java services, or by platform engineers managing infrastructure? Application → `reusable-workflows`. Infrastructure → this repo.
