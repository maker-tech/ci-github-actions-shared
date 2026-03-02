# CI Client Workflows (ci-github-actions-shared)

> [!NOTE] We do not accept PRs from external users.

Standardised, reusable GitHub Actions workflows for building and deploying Next.js applications.

## Contents

- [CI Client Workflows (ci-github-actions-shared)](#ci-client-workflows-ci-github-actions-shared)
  - [Contents](#contents)
  - [Defaults](#defaults)
  - [Purpose](#purpose)
  - [Available Workflows](#available-workflows)
    - [Internal Workflows](#internal-workflows)
  - [Quick Start](#quick-start)
    - [1. Choose Your Workflow](#1-choose-your-workflow)
    - [2. Create a Workflow File](#2-create-a-workflow-file)
    - [3. Configure Secrets](#3-configure-secrets)
  - [Common Wrapper Examples](#common-wrapper-examples)
    - [Chromatic on `uat`](#chromatic-on-uat)
    - [Promote `dev → uat` (manual)](#promote-dev--uat-manual)
    - [Release on `main` (and sync to `dev`)](#release-on-main-and-sync-to-dev)
    - [Release after Vercel auto-deployment](#release-after-vercel-auto-deployment)
    - [Promote, Deploy \& Release (consolidated)](#promote-deploy--release-consolidated)
    - [E2E tests on `dev`](#e2e-tests-on-dev)
    - [Lint PR title](#lint-pr-title)
    - [Sync labels](#sync-labels)
  - [Workflow Reference](#workflow-reference)
    - [nextjs-ci.yml](#nextjs-ciyml)
    - [deploy-chromatic.yml](#deploy-chromaticyml)
    - [e2e-test.yml](#e2e-testyml)
    - [promote-branch.yml](#promote-branchyml)
    - [release-version.yml](#release-versionyml)
    - [lint-pr-title.yml](#lint-pr-titleyml)
    - [repo-sync-labels.yml](#repo-sync-labelsyml)
  - [Composite Actions](#composite-actions)
  - [Environments \& Branching](#environments--branching)
  - [Security Model](#security-model)
  - [Versioning \& Updates](#versioning--updates)
    - [Automated Updates with Renovate](#automated-updates-with-renovate)
  - [What These Workflows Will NOT Do](#what-these-workflows-will-not-do)
  - [Contributing — Workflow Authoring Rules](#contributing--workflow-authoring-rules)
    - [Do not use `secrets.*` in step-level `if` conditions](#do-not-use-secrets-in-step-level-if-conditions)
    - [Pin third-party actions by SHA](#pin-third-party-actions-by-sha)
  - [Getting Help](#getting-help)
  - [FAQ](#faq)
  - [Summary](#summary)

---

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
| E2E tests          | `e2e-test.yml`         | Sharded Playwright E2E tests against a URL     |
| Promote branch     | `promote-branch.yml`   | Merge one branch into another (merge commit)   |
| Release/versioning | `release-version.yml`  | Conventional changelog + GitHub release + sync |
| PR title lint      | `lint-pr-title.yml`    | Enforce Conventional Commits PR titles         |
| Sync labels        | `repo-sync-labels.yml` | Sync GitHub labels from a shared base config   |

### Internal Workflows

Workflows prefixed with an underscore (`_`) are **internal to this repository** and should not be referenced by other repositories. These handle CI/CD for this repo itself.

| Workflow                | Description                                                        |
| ----------------------- | ------------------------------------------------------------------ |
| `_release.yml`          | Releases this repo and updates major version tags                  |
| `_lint-workflows.yml`   | Runs actionlint on PRs that touch workflow files                   |
| `_lint-pr-title.yml`    | Lints PR titles via the reusable `lint-pr-title.yml`               |
| `_repo-sync-labels.yml` | Syncs labels for this repo via the reusable `repo-sync-labels.yml` |

## Quick Start

### 1. Choose Your Workflow

Select the workflow that matches your deployment target.

### 2. Create a Workflow File

Create `.github/workflows/ci.yml` in your repository:

**Example: CI Only** (full example: [`examples/nextjs-ci.yml`](examples/nextjs-ci.yml))

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:

jobs:
  ci:
    name: 'CI'
    uses: maker-tech/ci-github-actions-shared/.github/workflows/nextjs-ci.yml@v1
    with:
      package_manager: pnpm
      ## Defaults (uncomment to override)
      # pnpm_version: '10.28.2'
      # node_version: '22.12.0'
      # working_directory: '.'
      # run_lint: true
      # run_tests: true
      # run_typecheck: true
```

> **Important:** Always reference a version tag (e.g., `@v1`). Never use `@main`.

### 3. Configure Secrets

Add the required secrets to your repository:

**Settings → Secrets and variables → Actions**

| Secret                    | Required For              | Permissions                                                                 |
| ------------------------- | ------------------------- | --------------------------------------------------------------------------- |
| `CHROMATIC_PROJECT_TOKEN` | Chromatic deployments     | Chromatic project token                                                     |
| `CI_GITHUB_TOKEN`         | Branch promotion/releases | Fine-grained PAT with **Contents: Read and write** on the target repository |
| `BASIC_AUTH_USER`         | E2E tests (optional)      | Basic auth username for protected environments                              |
| `BASIC_AUTH_PASSWORD`     | E2E tests (optional)      | Basic auth password for protected environments                              |
| `SLACK_WEBHOOK`           | Slack notifications       | Slack incoming webhook URL                                                  |

## Common Wrapper Examples

Reusable workflows don’t define triggers for your repo. Add a thin wrapper workflow in your repo with the triggers you want.

### Chromatic on `uat`

> Full example: [`examples/deploy-chromatic.yml`](examples/deploy-chromatic.yml)

```yaml
name: Deploy Chromatic

on:
  push:
    branches: [uat]

jobs:
  chromatic:
    name: 'Deploy Chromatic'
    uses: maker-tech/ci-github-actions-shared/.github/workflows/deploy-chromatic.yml@v1
    with:
      package_manager: pnpm
      ## Defaults (uncomment to override)
      # pnpm_version: '10.28.2'
      # node_version: '22.12.0'
      # working_directory: '.'
      # fetch_depth: 0
      # chromatic_command: ''
      # extra_env: ''
    secrets:
      CHROMATIC_PROJECT_TOKEN: ${{ secrets.CHROMATIC_PROJECT_TOKEN }}
```

### Promote `dev → uat` (manual)

> Full example: [`examples/promote-dev-to-uat.yml`](examples/promote-dev-to-uat.yml) | [`examples/promote-uat-to-main.yml`](examples/promote-uat-to-main.yml)

```yaml
name: Promote dev to uat

on:
  workflow_dispatch: {}

permissions:
  contents: write

jobs:
  promote:
    name: 'Promote Dev to UAT'
    uses: maker-tech/ci-github-actions-shared/.github/workflows/promote-branch.yml@v1
    with:
      source_branch: dev
      target_branch: uat
      ## Defaults (uncomment to override)
      # merge_message: ''
    secrets:
      CI_GITHUB_TOKEN: ${{ secrets.CI_GITHUB_TOKEN }}
      SLACK_WEBHOOK: ${{ secrets.SLACK_APP_WEBHOOK }}
```

### Release on `main` (and sync to `dev`)

> Full example: [`examples/release-version.yml`](examples/release-version.yml)
>
> **Tip:** To create releases only after successful deployment, use either:
> - [Release after Vercel auto-deployment](#release-after-vercel-auto-deployment) -- for platforms with native auto-deploy (Vercel, Netlify)
> - [Promote, Deploy & Release](#promote-deploy--release-consolidated) -- for platforms where you deploy via GitHub Actions (CLI, AWS, GCP)

```yaml
name: Release Version

on:
  push:
    branches: [main]
    paths-ignore:
      - package.json
      - CHANGELOG.md

permissions:
  contents: write

jobs:
  release:
    name: 'Create Release'
    uses: maker-tech/ci-github-actions-shared/.github/workflows/release-version.yml@v1
    with:
      release_branch: main
      sync_branch: dev
      ## Defaults (uncomment to override)
      # preset: conventionalcommits #options: conventionalcommits,eslint
      # output_file: CHANGELOG.md
      # skip_on_empty: false
      # release_count: 5            # Releases to keep in changelog (0 = all)
    secrets:
      CI_GITHUB_TOKEN: ${{ secrets.CI_GITHUB_TOKEN }}
      SLACK_WEBHOOK: ${{ secrets.SLACK_APP_WEBHOOK }}
```

### Release after Vercel auto-deployment

> Full example: [`examples/release-on-vercel-auto-deployment.yml`](examples/release-on-vercel-auto-deployment.yml)

Use this pattern when your hosting platform auto-deploys on push and reports deployment status back to GitHub (e.g. Vercel, Netlify). The release is only created after a successful production deployment -- no phantom releases if the deploy fails.

```yaml
name: Release Version

on:
  deployment_status:

permissions:
  contents: write

jobs:
  release:
    name: 'Create Release'
    if: >-
      github.event.deployment_status.state == 'success' &&
      github.event.deployment_status.environment == 'Production'
    uses: maker-tech/ci-github-actions-shared/.github/workflows/release-version.yml@v1
    with:
      release_branch: main
      sync_branch: dev
      skip_automated_commits: true
    secrets:
      CI_GITHUB_TOKEN: ${{ secrets.CI_GITHUB_TOKEN }}
```

> **Important:** The `deployment_status` environment name varies by platform. Vercel uses `Production` (capital P). Check your platform's documentation or inspect a deployment status event in your repo's Actions tab.
>
> When using this pattern, remove any separate `release-version.yml` workflow that triggers on `push` to `main` -- otherwise you will get duplicate releases.

### Promote, Deploy & Release (consolidated)

> Full example: [`examples/promote-deploy-release.yml`](examples/promote-deploy-release.yml)

Use this pattern when you need to verify that production deployment succeeds **before** creating a release. It replaces the separate `promote-uat-to-main.yml` + `release-version.yml` (push-triggered) pair with a single workflow that gates the release on deployment success.

```yaml
name: Promote, Deploy & Release

on:
  workflow_dispatch:

permissions:
  contents: write

jobs:
  promote:
    name: 'Promote UAT to Main'
    uses: maker-tech/ci-github-actions-shared/.github/workflows/promote-branch.yml@v1
    with:
      source_branch: uat
      target_branch: main
    secrets:
      CI_GITHUB_TOKEN: ${{ secrets.CI_GITHUB_TOKEN }}

  deploy:
    name: 'Deploy to Production'
    needs: promote
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v6
        with:
          ref: ${{ needs.promote.outputs.merge_sha }}
      - name: Deploy
        run: echo "Add your deployment steps here"

  release:
    name: 'Create Release'
    needs: deploy
    uses: maker-tech/ci-github-actions-shared/.github/workflows/release-version.yml@v1
    with:
      release_branch: main
      sync_branch: dev
    secrets:
      CI_GITHUB_TOKEN: ${{ secrets.CI_GITHUB_TOKEN }}
```

> **Important:** When using this consolidated pattern, remove any separate `release-version.yml` workflow that triggers on `push` to `main` — otherwise you will get duplicate releases.

### E2E tests on `dev`

> Full example: [`examples/e2e-test.yml`](examples/e2e-test.yml)

```yaml
name: E2E tests DEV

on:
  push:
    branches: [dev]
  workflow_dispatch:

jobs:
  e2e:
    name: 'E2E Tests'
    uses: maker-tech/ci-github-actions-shared/.github/workflows/e2e-test.yml@v1
    with:
      target_url: https://dev.levande.com.au
      ## Defaults (uncomment to override)
      # playwright_version: '1.55.1'
      # axe_core_version: '4.11.0'
      # node_version: '22'
      # test_browser: chromium  #options: chromium,firefox,webkit
      # shard_total: 4
      # artifact_retention_days: 30
      # fail_screenshots_path: 'tests/functional/screenshots/**'
    secrets:
      BASIC_AUTH_USER: ${{ secrets.BASIC_AUTH_USER }}
      BASIC_AUTH_PASSWORD: ${{ secrets.BASIC_AUTH_PASSWORD }}
      SLACK_WEBHOOK: ${{ secrets.SLACK_APP_WEBHOOK }}
```

### Lint PR title

> Full example: [`examples/lint-pr-title.yml`](examples/lint-pr-title.yml)

```yaml
name: Lint PR title

on:
  pull_request:
    types: [opened, edited, synchronize, reopened]

permissions:
  pull-requests: read

jobs:
  lint:
    name: 'Lint PR Title'
    uses: maker-tech/ci-github-actions-shared/.github/workflows/lint-pr-title.yml@v1
    ## Defaults (uncomment to override)
    # with:
    #   preset: conventionalcommits  #options: conventionalcommits,eslint
```

### Sync labels

> Full example: [`examples/repo-sync-labels.yml`](examples/repo-sync-labels.yml)

```yaml
name: Repo - Sync Labels

on:
  workflow_dispatch:

jobs:
  sync-labels:
    uses: maker-tech/ci-github-actions-shared/.github/workflows/repo-sync-labels.yml@v1
    ## Defaults (uncomment to override)
    # with:
    #   extra_labels_file: .github/repo-labels.json
    #   delete_other_labels: false
    permissions:
      contents: read
      issues: write
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

### e2e-test.yml

Run sharded Playwright E2E tests against a target URL. Uses minimal dependencies (Playwright + Axe only, not full project deps) for fast installs.

**Inputs:**

| Name                      | Required | Default                           | Description                                                         |
| ------------------------- | -------- | --------------------------------- | ------------------------------------------------------------------- |
| `playwright_version`      | No       | `1.55.1`                          | Playwright version                                                  |
| `axe_core_version`        | No       | `4.11.0`                          | Axe accessibility testing library version                           |
| `node_version`            | No       | `22`                              | Node.js version                                                     |
| `target_url`              | Yes      | -                                 | Target URL to run tests against                                     |
| `test_browser`            | No       | `chromium`                        | Browser to install and test with (options:chromium,firefox,webkit ) |
| `shard_total`             | No       | `4`                               | Number of parallel shards                                           |
| `artifact_retention_days` | No       | `30`                              | Days to keep test report artifacts                                  |
| `fail_screenshots_path`   | No       | `tests/functional/screenshots/**` | Path to failure screenshots                                         |

**Secrets:**

| Name                  | Required | Notes                                          |
| --------------------- | -------- | ---------------------------------------------- |
| `BASIC_AUTH_USER`     | No       | Basic auth username for protected environments |
| `BASIC_AUTH_PASSWORD` | No       | Basic auth password for protected environments |
| `SLACK_WEBHOOK`       | No       | Slack incoming webhook URL                     |

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

| Name              | Required | Notes                                                                       |
| ----------------- | -------- | --------------------------------------------------------------------------- |
| `CI_GITHUB_TOKEN` | Yes      | Fine-grained PAT with **Contents: Read and write** on the target repository |
| `SLACK_WEBHOOK`   | No       | Slack incoming webhook URL                                                  |

**Outputs:**

| Name        | Description                                      |
| ----------- | ------------------------------------------------ |
| `merge_sha` | The merge commit SHA pushed to the target branch |

---

### release-version.yml

Create a conventional changelog + bump version + create GitHub release, then optionally sync `release_branch → sync_branch`.

**Inputs:**

| Name             | Required | Default               | Description                             |
| ---------------- | -------- | --------------------- | --------------------------------------- |
| `release_branch` | No       | `main`                | Branch where releases are created       |
| `sync_branch`    | No       | `dev`                 | Branch to sync after release            |
| `preset`         | No       | `conventionalcommits` | Changelog preset                        |
| `output_file`    | No       | `CHANGELOG.md`        | Changelog file path                     |
| `skip_on_empty`  | No       | `false`               | Skip release if no changes              |
| `release_count`  | No       | `5`                   | Releases to keep in changelog (0 = all) |
| `skip_automated_commits` | No | `false`            | Skip if last commit is from github-actions[bot] |

**Secrets:**

| Name              | Required | Notes                                                                       |
| ----------------- | -------- | --------------------------------------------------------------------------- |
| `CI_GITHUB_TOKEN` | Yes      | Fine-grained PAT with **Contents: Read and write** on the target repository |
| `SLACK_WEBHOOK`   | No       | Slack incoming webhook URL                                                  |

---

### lint-pr-title.yml

Validate PR titles follow Conventional Commits (or eslint conventions).

**Inputs:**

| Name     | Required | Default               | Description                                                  |
| -------- | -------- | --------------------- | ------------------------------------------------------------ |
| `preset` | No       | `conventionalcommits` | Commit convention preset (`conventionalcommits` or `eslint`) |

---

### repo-sync-labels.yml

Sync GitHub labels from a shared base config. Applies a standard set of labels (dependencies, automated, dev-deps, prod-deps, github-actions, security) from the shared repo. Consumer repos can optionally provide additional labels.

**Inputs:**

| Name                  | Required | Default | Description                                         |
| --------------------- | -------- | ------- | --------------------------------------------------- |
| `extra_labels_file`   | No       | -       | Path to additional labels JSON in the consumer repo |
| `delete_other_labels` | No       | `false` | Whether to delete labels not defined in config      |

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

> **Permissions:** Each shared workflow declares the minimum `GITHUB_TOKEN` permissions it needs. However, due to GitHub's intersection model, the calling workflow must also declare at least the same permissions. If your repository uses restrictive default permissions, add a `permissions` block to your wrapper workflow. See the [Common Wrapper Examples](#common-wrapper-examples) for the correct values.

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
		"github>maker-tech/ci-github-actions-shared:renovate-preset"
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

`next`, `react`, `react-dom`, `next-auth`, `tailwindcss`, `typescript`, `zod`, `@tanstack/react-query`

**Adding project-specific critical packages:**

If your project uses additional packages that should get individual PRs (e.g. `@sentry/*`, `@builder.io/*`), extend the preset in your repo's `renovate.json`:

```json
{
	"$schema": "https://docs.renovatebot.com/renovate-schema.json",
	"extends": [
		"config:recommended",
		"github>maker-tech/ci-github-actions-shared:renovate-preset"
	],
	"timezone": "Pacific/Auckland",
	"schedule": ["before 6am on monday"],
	"packageRules": [
		{
			"description": "Project-specific critical packages",
			"matchPackageNames": ["/^@sentry//", "/^@builder\\.io//"],
			"labels": ["dependencies", "critical"],
			"minimumReleaseAge": "14 days",
			"prPriority": 15
		}
	]
}
```

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

## Contributing — Workflow Authoring Rules

When adding or editing workflows in this repository, keep these rules in mind:

### Do not use `secrets.*` in step-level `if` conditions

The `secrets` context is [not available](https://docs.github.com/en/actions/learn-github-actions/contexts#context-availability) in `jobs.<id>.steps.if`. actionlint (which runs automatically on PRs via `_lint-workflows.yml`) enforces this and will fail the check.

**Instead**, evaluate the secret in a `run` step and expose the result as a step output:

```yaml
# Evaluate secret availability (secrets IS available in `run`)
- name: Check Slack webhook
  id: slack
  run: echo "available=${{ secrets.SLACK_WEBHOOK != '' }}" >> "$GITHUB_OUTPUT"

# Gate on the output (steps.* IS available in `if`)
- name: Send Slack notification
  if: ${{ steps.slack.outputs.available == 'true' }}
  uses: rtCamp/action-slack-notify@...
```

### Pin third-party actions by SHA

Always reference third-party actions by their full commit SHA, not a mutable tag:

```yaml
# Good
uses: rtCamp/action-slack-notify@c58b60ee33df2229ed2d2eed86eeaf7e6c527c5a

# Bad
uses: rtCamp/action-slack-notify@v2
```

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
