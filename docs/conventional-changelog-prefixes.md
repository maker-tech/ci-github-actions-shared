# Conventional changelog commit prefixes

This repo’s [`release-version.yml`](../.github/workflows/release-version.yml) uses [TriPSs/conventional-changelog-action](https://github.com/TriPSs/conventional-changelog-action) with the **`conventionalcommits`** preset (override via the workflow `preset` input).

Commits must follow [Conventional Commits](https://www.conventionalcommits.org/): **`type(scope)!: summary`** (scope and breaking `!` are optional).

## Types that affect versioning

With the default conventionalcommits tooling, these **types drive semver bumps**:

| Prefix | Typical bump | Notes |
|--------|--------------|--------|
| `feat:` | **minor** | New backward-compatible behavior |
| `fix:` | **patch** | Bug fixes |
| `perf:` | **patch** | Performance improvements |

A **major** bump usually requires a breaking change: `type!:` (e.g. `feat!: remove old API`) or a footer such as `BREAKING CHANGE:` in the commit body.

## Types that appear in the changelog but do not bump (by default)

Examples: `chore:`, `ci:`, `docs:`, `style:`, `refactor:`, `test:`, `build:`.

They may still be listed in generated notes, but **they do not trigger a new version** on their own. For CI/workflow changes that *should* ship as a patch release, use something like **`fix(ci): …`** so consumers get a tag to pin—not `chore(ci):`.

## Scopes

Scopes are labels in parentheses and do not replace the type: **`fix(ci): pin setup-node`** is a `fix` with scope `ci`. Only the **type** (`feat` / `fix` / `perf`, plus breaking markers) controls the bump behavior above.

## Changing behavior

Different **`preset`** values (or custom conventional-changelog config in consuming repos) can map types differently. Treat this table as accurate for **`conventionalcommits`** as used here; consult that preset’s docs if you switch presets.
