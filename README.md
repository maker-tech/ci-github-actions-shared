# CI Client Workflows (ci-github-actions-shared)

Standardised, reusable GitHub Actions workflows for building and deploying Next.js applications.

## Defaults

- **Package manager**: `pnpm` (overrideable via inputs)
- **Node.js**: `22.12.0` (overrideable via inputs)

> **Note**: For Vercel deployments, use Vercel's native GitHub integration (auto-deploys on push). These workflows focus on CI, Chromatic, and release automation.

## Purpose

This repository provides centralised CI/CD pipelines that:

- Are maintained in one place
- Are versioned and stable
- Run in your repository context
- Use your secrets and permissions

**You are responsible for:**

- Secrets configuration
- Cloud access setup
- Environment configuration

## Available Workflows

| Use Case           | Workflow               | Description                                    |
| ------------------ | ---------------------- | ---------------------------------------------- |
| CI only            | `nextjs-ci.yml`        | Lint, typecheck, and test                      |
| Chromatic          | `deploy-chromatic.yml` | Deploy Chromatic (e.g. on `uat`)               |
| Promote branch     | `promote-branch.yml`   | Merge one branch into another (merge commit)   |
| Release/versioning | `release-version.yml`  | Conventional changelog + GitHub release + sync |
| PR title lint      | `lint-pr-title.yml`    | Enforce Conventional Commits PR titles         |

### Internal Workflows

Workflows prefixed with an underscore (`_`) are **internal to this repository** and should not be referenced by other repositories. These handle CI/CD for this repo itself.

| Workflow       | Description                                       |
| -------------- | ------------------------------------------------- |
| `_release.yml` | Releases this repo and updates major version tags |

## Quick Start

### 1. Choose Your Workflow

Select the workflow that matches your deployment target.

### 2. Create a Workflow File

Create `.github/workflows/ci.yml` in your repository:

**Example: CI Only**

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:

jobs:
  ci:
    uses: maker-tech/ci-github-actions-shared/.github/workflows/nextjs-ci.yml@v1
    with:
      package_manager: pnpm
      pnpm_version: 10.28.2
      node_version: 22.12.0
```

> **Important:** Always reference a version tag (e.g., `@v1`). Never use `@main`.

### 3. Configure Secrets

Add the required secrets to your repository:

**Settings → Secrets and variables → Actions**

| Secret                    | Required For              | Permissions                                                                 |
| ------------------------- | ------------------------- | --------------------------------------------------------------------------- |
| `CHROMATIC_PROJECT_TOKEN` | Chromatic deployments     | Chromatic project token                                                     |
| `CI_GITHUB_TOKEN`         | Branch promotion/releases | Fine-grained PAT with **Contents: Read and write** on the target repository |
| `SLACK_WEBHOOK`           | Slack notifications       | Slack incoming webhook URL                                                  |

## Common Wrapper Examples

Reusable workflows don’t define triggers for your repo. Add a thin wrapper workflow in your repo with the triggers you want.

### Chromatic on `uat`

```yaml
name: Deploy Chromatic

on:
  push:
    branches: [uat]

jobs:
  chromatic:
    uses: maker-tech/ci-github-actions-shared/.github/workflows/deploy-chromatic.yml@v1
    with:
      package_manager: pnpm
      pnpm_version: 10.28.2
      node_version: 22.12.0
      extra_env: |
        NEXT_PUBLIC_CLOUDINARY_CLOUD_NAME=${{ secrets.CLOUDINARY_CLOUD_NAME }}
    secrets:
      CHROMATIC_PROJECT_TOKEN: ${{ secrets.CHROMATIC_PROJECT_TOKEN }}
```

### Promote `dev → uat` (manual)

```yaml
name: Promote dev to uat

on:
  workflow_dispatch: {}

jobs:
  promote:
    uses: maker-tech/ci-github-actions-shared/.github/workflows/promote-branch.yml@v1
    with:
      source_branch: dev
      target_branch: uat
    secrets:
      CI_GITHUB_TOKEN: ${{ secrets.CI_GITHUB_TOKEN }}
      SLACK_WEBHOOK: ${{ secrets.SLACK_APP_WEBHOOK }}
```

### Release on `main` (and sync to `dev`)

```yaml
name: Release Version

on:
  push:
    branches: [main]
    paths-ignore:
      - package.json
      - CHANGELOG.md

jobs:
  release:
    uses: maker-tech/ci-github-actions-shared/.github/workflows/release-version.yml@v1
    with:
      release_branch: main
      sync_branch: dev
    secrets:
      CI_GITHUB_TOKEN: ${{ secrets.CI_GITHUB_TOKEN }}
      SLACK_WEBHOOK: ${{ secrets.SLACK_APP_WEBHOOK }}
```

### Lint PR title

```yaml
name: Lint PR title

on:
  pull_request:
    types: [opened, edited, synchronize, reopened]

jobs:
  lint:
    uses: maker-tech/ci-github-actions-shared/.github/workflows/lint-pr-title.yml@v1
```

## Workflow Reference

### nextjs-ci.yml

Runs linting, type checking, and tests. No deployment.

**Inputs:**

| Name                | Required | Default   | Description                       |
| ------------------- | -------- | --------- | --------------------------------- |
| `package_manager`   | No       | `pnpm`    | Package manager (`pnpm` or `npm`) |
| `pnpm_version`      | No       | `10.28.2` | pnpm version (when using pnpm)    |
| `node_version`      | No       | `22.12.0` | Node.js version                   |
| `working_directory` | No       | `.`       | Directory containing the app      |
| `run_lint`          | No       | `true`    | Run linting                       |
| `run_tests`         | No       | `true`    | Run tests                         |
| `run_typecheck`     | No       | `true`    | Run TypeScript check              |

---

### deploy-chromatic.yml

Deploy Chromatic (typically used on `uat` or similar).

**Inputs:**

| Name                | Required | Default   | Description                                   |
| ------------------- | -------- | --------- | --------------------------------------------- |
| `package_manager`   | No       | `pnpm`    | Package manager (`pnpm` or `npm`)             |
| `pnpm_version`      | No       | `10.28.2` | pnpm version (when using pnpm)                |
| `node_version`      | No       | `22.12.0` | Node.js version                               |
| `working_directory` | No       | `.`       | Directory containing the app                  |
| `fetch_depth`       | No       | `0`       | Git fetch depth (0 for full history)          |
| `chromatic_command` | No       | -         | Command to run (blank uses defaults)          |
| `extra_env`         | No       | -         | Extra env vars as newline-separated KEY=VALUE |

**Secrets:**

| Name                      | Required |
| ------------------------- | -------- |
| `CHROMATIC_PROJECT_TOKEN` | Yes      |

---

### promote-branch.yml

Merge one branch into another (always creates a merge commit). Common use: `dev → uat`, `uat → main`.

**Inputs:**

| Name            | Required | Default | Description          |
| --------------- | -------- | ------- | -------------------- |
| `source_branch` | Yes      | -       | Branch to merge from |
| `target_branch` | Yes      | -       | Branch to merge into |
| `merge_message` | No       | -       | Merge commit message |

**Secrets:**

| Name               | Required | Notes                                                                       |
| ------------------ | -------- | --------------------------------------------------------------------------- |
| `CI_GITHUB_TOKEN`  | Yes      | Fine-grained PAT with **Contents: Read and write** on the target repository |
| `SLACK_WEBHOOK`    | No       | Slack incoming webhook URL                                                  |

---

### release-version.yml

Create a conventional changelog + bump version + create GitHub release, then optionally sync `release_branch → sync_branch`.

**Inputs:**

| Name             | Required | Default               | Description                       |
| ---------------- | -------- | --------------------- | --------------------------------- |
| `release_branch` | No       | `main`                | Branch where releases are created |
| `sync_branch`    | No       | `dev`                 | Branch to sync after release      |
| `preset`         | No       | `conventionalcommits` | Changelog preset                  |
| `output_file`    | No       | `CHANGELOG.md`        | Changelog file path               |
| `skip_on_empty`  | No       | `false`               | Skip release if no changes        |

**Secrets:**

| Name               | Required | Notes                                                                       |
| ------------------ | -------- | --------------------------------------------------------------------------- |
| `CI_GITHUB_TOKEN`  | Yes      | Fine-grained PAT with **Contents: Read and write** on the target repository |
| `SLACK_WEBHOOK`    | No       | Slack incoming webhook URL                                                  |

---

### lint-pr-title.yml

Validate PR titles follow Conventional Commits.

**Inputs:** None

## Composite Actions

These actions can be used directly in your workflows for more granular control:

| Action                 | Description                            |
| ---------------------- | -------------------------------------- |
| `actions/setup-node`   | Setup Node.js with pnpm/npm caching    |
| `actions/nextjs-build` | Install dependencies and build Next.js |

**Example:**

```yaml
steps:
  - uses: actions/checkout@v4

  - uses: maker-tech/ci-github-actions-shared/actions/setup-node@v1
    with:
      package_manager: pnpm
      pnpm_version: 10.28.2
      node_version: 22.12.0

  - uses: maker-tech/ci-github-actions-shared/actions/nextjs-build@v1
    with:
      package_manager: pnpm
      skip_install: 'true'
```

## Environments & Branching

Environments (dev, staging, prod) are **owned by your repository**.

Typical patterns:

- `main` → production
- `uat` → staging/UAT
- `dev` → development

The shared workflows do not enforce environment rules. For Vercel, use their native GitHub integration for automatic preview/production deployments.

## Security Model

- Workflows run **in your repository context**
- Secrets are **never shared** between repositories
- No access to other client repositories
- No access to this organisation's secrets

## Versioning & Updates

This repository follows semantic versioning:

- `v1` → backward-compatible updates (floating tag, always points to latest 1.x.x)
- `v2` → breaking changes
- `v1.2.3` → exact version (immutable)

This repo uses its own `release-version.yml` workflow (via `_release.yml`) to create releases. When a new version is released, the major version tag (e.g., `v1`) is automatically updated to point to it.

> **Maintainer note — major version tags:**
> The `_release.yml` workflow automatically force-updates the major version tag (e.g., `v1`) whenever a new `v1.x.x` release is created. This allows consumers to reference `@v1` and always get the latest compatible version.
>
> If you ever need to do this manually:
>
> ```bash
> git tag -fa v1 v1.x.x^{} -m "Update v1 to v1.x.x"
> git push origin v1 --force
> ```
>
> Replace `v1.x.x` with the actual tag (e.g., `v1.2.3`). The `^{}` dereferences the annotated tag to the underlying commit.

**You control when to upgrade** by changing the version tag in your workflow files.

```yaml
# Pin to major version (recommended)
uses: maker-tech/ci-github-actions-shared/.github/workflows/nextjs-ci.yml@v1

# Pin to specific version
uses: maker-tech/ci-github-actions-shared/.github/workflows/nextjs-ci.yml@v1.2.3
```

### Automated Updates with Renovate

This repo provides a **shareable Renovate preset** for consumer repositories. Use it to automatically track shared workflow updates and manage npm dependencies with sensible defaults.

**Minimal consumer `renovate.json`:**

```json
{
	"$schema": "https://docs.renovatebot.com/renovate-schema.json",
	"extends": [
		"config:recommended",
		"github>maker-tech/ci-github-actions-shared"
	],
	"timezone": "Pacific/Auckland",
	"schedule": ["before 6am on monday"]
}
```

That's it. The preset handles everything else.

**What the preset includes:**

| Category                | Behaviour                                                    |
| ----------------------- | ------------------------------------------------------------ |
| Shared workflow refs    | Auto-detect `ci-github-actions-shared/...@vX` and create PRs |
| Major version updates   | Flagged as breaking with review checklist                    |
| Dev dependencies        | Grouped, 14-day stability period                             |
| Production dependencies | Grouped (non-critical), 14-day stability                     |
| Critical packages       | Individual PRs (next, react, typescript, etc.)               |
| Node.js version         | Notes to align with shared workflow defaults                 |

**Critical packages** (get individual PRs):

`next`, `react`, `react-dom`, `next-auth`, `tailwindcss`, `typescript`, `zod`, `@tanstack/react-query`, `@sentry/*`, `@builder.io/*`

**Why inheritance over scaffolding?**

| Scaffolding (copy-paste)      | Inheritance (this approach)         |
| ----------------------------- | ----------------------------------- |
| Workflows copied to each repo | Thin wrappers call shared workflows |
| Updates require manual sync   | Updates via Renovate PRs            |
| Drift between repositories    | Consistent behaviour everywhere     |
| Template bloat                | Single source of truth              |

## What These Workflows Will NOT Do

The shared workflows will not:

- Contain client-specific logic
- Manage your secrets
- Encode your environment rules
- Support custom hacks via flags

**If your use case doesn't fit:** Request a new workflow, not a workaround.

## Getting Help

If you need:

- A new deployment target
- A missing feature
- A bug fix

Please open an issue with:

1. Repository name
2. Desired workflow
3. Expected outcome

## FAQ

**Can we customise the steps?**

No. Custom logic belongs in your repository, not shared workflows.

**Can we pin to a specific version?**

Yes, and you should: `@v1.2.3`

**What happens if a workflow changes?**

Nothing, unless you upgrade. Your pinned version remains stable.

## Summary

- Centralised CI logic
- Client-owned configuration
- Versioned and predictable
- No copy-paste pipelines
