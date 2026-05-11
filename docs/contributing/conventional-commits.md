# Conventional Commits

All repositories use the **Conventional Commits** specification for commit messages and PR titles.

Full spec: <https://www.conventionalcommits.org/en/v1.0.0/>

---

## Summary

A commit message (and PR title) must be structured as:

```
<type>(<scope>): <short description>

[optional body]

[optional footer(s)]
```

- **`type`** and **`scope`** are lowercase.
- **`short description`** is imperative mood, lowercase, no trailing period, ≤ 72 characters total including the type and scope prefix.
- **`body`** and **`footer(s)`** are optional. Use them for additional context, issue references (`Closes #42`), or breaking-change declarations.

---

## Types

| Type | Use for |
|------|---------|
| `feat` | A new feature or capability visible to users or operators |
| `fix` | A bug fix |
| `style` | Formatting, whitespace, or cosmetic changes — no logic change |
| `docs` | Documentation only — no code or config change |
| `chore` | Maintenance: dependency bumps, lock file updates, config that does not affect production behaviour |
| `ci` | CI/CD workflow or pipeline changes (new workflows, logic changes to existing ones) |
| `refactor` | Code restructuring that neither fixes a bug nor adds a feature |
| `test` | Adding or correcting tests |
| `perf` | Performance improvement |

---

## Scopes (optional but encouraged)

Use the stack, component, or area name. Examples: `landingzone`, `hub`, `platform_dev`, `iam`, `k8s`, `argocd`, `terraform`, `actions`.

---

## Breaking Changes

Append `!` after the type/scope **or** add a `BREAKING CHANGE:` footer (or both):

```
feat(terraform-stack)!: rename IENGINEER_API_TOKEN to GH_TOKEN_PRIVATE_MODULES

BREAKING CHANGE: all callers must rename their secret mapping.
```

Either form triggers a **major version bump** in the semver auto-tagging.

---

## PR Titles = Squash Commit Messages

All repositories merge via **squash merge**. The PR title becomes the single commit on `main`. It must therefore follow the Conventional Commits format.

### Examples

| PR title | Type | Effect |
|----------|------|--------|
| `feat(terraform-stack): add private_modules_org input` | `feat` | minor bump |
| `fix(lint-pr-title): correct PR comment anchor link` | `fix` | patch bump |
| `docs: add AGENTS.md` | `docs` | patch bump |
| `chore(ci): bump actions/checkout from v4 to v6` | `chore(ci)` | patch bump |
| `feat(terraform-stack)!: rename IENGINEER_API_TOKEN` | `feat!` | **major bump** |

> **Convention:** version bumps to CI workflow files (Dependabot or manual) use **`chore(ci)`**, not `ci`. The `ci` type is reserved for logic or structural changes to a workflow.

---

## Semver Auto-Tagging

`tag-release.yml` reads commit messages since the last tag and determines the next semver version automatically:

| Highest commit type since last tag | Bump |
|-------------------------------------|------|
| `type!:` or `BREAKING CHANGE:` footer | **major** — `v1.x.y → v2.0.0` |
| `feat:` / `feat(scope):` | **minor** — `v1.2.x → v1.3.0` |
| anything else | **patch** — `v1.2.3 → v1.2.4` |
