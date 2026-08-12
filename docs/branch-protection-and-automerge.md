# Branch Protection, Rulesets, and Renovate Automerge

This guide is for the **consuming repository** (examples use `dev`). It covers GitHub Rulesets plus the extra Renovate config you must add if you want patch/minor updates to automerge.

The shareable preset does **not** enable automerge. Extending it only opens PRs. This repo's own `.github/renovate.json` is also not a template for application repos.

## Two Renovate configs (do not mix them up)

| Config                 | File                                                | Audience                        | Automerge                                                                                                                                                                                                                                     |
| ---------------------- | --------------------------------------------------- | ------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Shareable preset       | [`renovate-preset.json`](../renovate-preset.json)   | Consuming repos via `extends`   | **No.** Opens PRs; humans merge. `automergeSchedule` is only a time window.                                                                                                                                                                   |
| This repo's own config | [`.github/renovate.json`](../.github/renovate.json) | This shared-workflows repo only | **Yes, but only** for trusted GitHub Actions (`actions/checkout`, `actions/setup-node`, `actions/cache`, `actions/upload-artifact`, `pnpm/action-setup`) on minor, patch, and digest updates. Majors and third-party actions never automerge. |

See the [README Renovate section](../README.md#automated-updates-with-renovate) for the full breakdown of each file.

## What this achieves

After you complete **both** the rulesets below **and** the consuming-repo `automerge: true` package rules:

- Human PRs in the consuming repository require **2 approving reviews** and **passing CI checks** before merging to `dev`.
- Renovate patch and minor PRs (except packages you exclude) **automerge without human review** once CI checks pass.
- Shared workflow updates (`maker-tech/ci-github-actions-shared`) stay on manual review unless you explicitly automerge them.
- Sensitive packages you list (for example Next.js, React, next-auth, Builder.io) stay on manual review, even for patches.
- Major version updates always require human review (the preset already sets `automerge: false` for majors).
- Org admins can force-merge when a check is stuck or known-failing.

If you only extend the preset and skip the automerge package rules, Renovate still opens PRs, but nothing merges automatically.

## Why two rulesets?

GitHub Rulesets enforce rules on a branch. Bypass actors skip all rules **within a specific ruleset only**. A single ruleset with both reviews and status checks would let bypass actors skip everything, including CI. Two rulesets let us separate concerns:

| Ruleset                       | Rules                      | Bypass actors           | Effect                                                                       |
| ----------------------------- | -------------------------- | ----------------------- | ---------------------------------------------------------------------------- |
| **A — Require user reviews**  | 2 PR approvals             | Renovate app, Org admin | Renovate and admins skip reviews; everyone else needs 2 approvals            |
| **B — Require status checks** | `CI Gate`, `Lint PR Title` | Org admin               | Everyone (including Renovate) must pass CI; admins can force-merge if needed |

### Why not `platformAutomerge: true`?

GitHub's native auto-merge (`platformAutomerge: true`) respects **all** ruleset requirements including reviews. Renovate PRs would sit waiting for 2 humans to approve indefinitely. With `platformAutomerge: false`, Renovate merges via its own API token, which triggers its bypass actor status on Ruleset A.

---

## Prerequisites

- Admin access to the **consuming repository** on GitHub.
- The Renovate GitHub App installed on the consuming repo/org.
- A consuming-repo `.github/renovate.json` that extends the shareable preset **and** sets `automerge: true` for the updates you want (see [example config](#example-consuming-repo-config)). The preset alone does not automerge.
- The `quality-checks.yml` and `lint-pr-title.yml` workflows committed in the consuming repository and run at least once there (so check names appear in GitHub's dropdown).

---

## Step 1 — Remove classic branch protection on `dev` in the consuming repository

Classic branch protection and Rulesets can coexist, but they stack — the most restrictive wins. To avoid conflicts, use Rulesets exclusively.

1. Go to **Settings > Branches**.
2. If a branch protection rule exists for `dev`, note down its current settings (required checks, required reviewers, dismiss stale reviews, code owners, etc.).
3. Click **Edit** on the `dev` rule, then **Delete this rule**.
4. If no rule exists for `dev`, skip this step.

---

## Step 2 — Create Ruleset A: "dev - Require user reviews"

> Settings > Rules > Rulesets > New ruleset > New branch ruleset

1. Click **New ruleset** > **New branch ruleset**.
2. Set the following:
   - **Ruleset name:** `dev - Require user reviews`
   - **Enforcement status:** Active
3. Under **Target branches:**
   - Click **Add target** > **Add default branch** (if `dev` is the default), or select **Include by pattern** and enter `dev`.
4. Under **Bypass list:**
   - Click **Add bypass** and add both of the following:
     - **Renovate** (the GitHub App) — set to **Always**
     - **Org admin** (the admin role or specific admin user) — set to **Always**
5. Under **Branch rules**, enable:
   - **Require a pull request before merging**
     - Required approvals: **2**
     - Optionally enable: Dismiss stale reviews on new pushes, Require review from code owners (match your previous settings)
6. Do **NOT** enable any status check rules in this ruleset.
7. Click **Create**.

---

## Step 3 — Create Ruleset B: "dev - Require status checks"

> Settings > Rules > Rulesets > New ruleset > New branch ruleset

1. Click **New ruleset** > **New branch ruleset**.
2. Set the following:
   - **Ruleset name:** `dev - Require status checks`
   - **Enforcement status:** Active
3. Under **Target branches:**
   - Same as Ruleset A — target the `dev` branch.
4. Under **Bypass list:**
   - Click **Add bypass** and add:
     - **Org admin** (the admin role or specific admin user) — set to **Always**
   - Do **NOT** add Renovate here. Renovate must pass these checks like everyone else.
5. Under **Branch rules**, enable:
   - **Require status checks to pass**
     - Click **Add checks** and add each of these:
       - `CI Gate`
       - `Lint PR Title`
     - Leave **Require branches to be up to date before merging** disabled (causes unnecessary rebases).
6. Do **NOT** enable any pull request review rules in this ruleset.
7. Click **Create**.

### Important notes on adding checks

- Check names like `CI Gate` and `Lint PR Title` only appear in the "Add checks" dropdown **after the workflow has run at least once** on the repo. If they don't appear, type the exact name manually into the search box.
- Only add checks that run on **every** PR. Do not add FOSSA, `renovate/stability-days`, or Vercel — these only run on specific PRs and would deadlock all other PRs.

---

## Step 4 — Verify the rulesets

1. Go to **Settings > Rules > Rulesets** and confirm two active rulesets exist:
   - `dev - Require user reviews` — Renovate + Org admin in bypass list, no status checks
   - `dev - Require status checks` — Org admin in bypass list, `CI Gate` + `Lint PR Title` required
2. Open an existing PR targeting `dev` (or create a throwaway one).
3. Scroll to the merge box and confirm both requirements are visible:
   - "At least 2 approving reviews are required"
   - `CI Gate` — Required
   - `Lint PR Title` — Required

---

## How CI Gate works

The `CI Gate` job in the consuming repository's `.github/workflows/quality-checks.yml` solves a specific problem with required status checks and conditional CI.

### The problem

We only want to run linting and type-checking when source files change — not for docs-only or config-only PRs. The naive approach is using `paths-ignore` on the workflow trigger:

```yaml
# DON'T DO THIS with required checks
on:
  pull_request:
    paths-ignore:
      - 'docs/**'
      - '**/*.md'
```

This causes a **deadlock**: when all changed files match `paths-ignore`, the workflow never fires, the required check never appears, and the PR is blocked forever.

### The solution

The workflow always fires on every PR (no `paths-ignore` on the trigger). Inside the workflow, three jobs work together:

1. **`changes`** — Uses `dorny/paths-filter` to detect whether source files changed.
2. **`checks`** — Only runs lint and typecheck if source files changed. Calls the shared CI reusable workflow.
3. **`ci-gate`** — Always runs. Reports a consistent check name (`CI Gate`) that the ruleset can require. Passes when checks succeed or are skipped; fails when checks fail.

The gate is needed because when a reusable workflow job is skipped, GitHub may not report the inner check name (`CI / Lint & Test`), which would cause the same deadlock. The gate always runs and always reports a status.

### Working example

This is the full `quality-checks.yml` for reference in the consuming repository:

```yaml
name: Quality Checks

on:
  workflow_dispatch:
  pull_request:

jobs:
  changes:
    name: 'Detect changes'
    runs-on: ubuntu-latest
    permissions:
      pull-requests: read
    outputs:
      should_run: ${{ steps.filter.outputs.src }}
    steps:
      - uses: dorny/paths-filter@fbd0ab8f3e69293af611ebaee6363fc25e6d187d # v4.0.1
        id: filter
        with:
          predicate-quantifier: 'every'
          filters: |
            src:
              - '**'
              - '!.github/**'
              - '!.husky/**'
              - '!.cursor/**'
              - '!docs/**'
              - '!**/*.md'

  checks:
    name: 'CI'
    needs: changes
    if: needs.changes.outputs.should_run == 'true'
    uses: maker-tech/ci-github-actions-shared/.github/workflows/nextjs-ci.yml@v2
    with:
      package_manager: pnpm
      pnpm_version: '10.28.2'
      node_version: '24.19.0'
      run_lint: true
      run_typecheck: true
      run_tests: false

  ci-gate:
    name: 'CI Gate'
    if: ${{ !cancelled() }}
    needs: [changes, checks]
    runs-on: ubuntu-latest
    steps:
      - run: |
          if [[ "${{ needs.checks.result }}" == "failure" || "${{ needs.checks.result }}" == "cancelled" ]]; then
            exit 1
          fi
```

### How it behaves

| PR changes                           | `changes`                | `checks`              | `ci-gate`              | Result               |
| ------------------------------------ | ------------------------ | --------------------- | ---------------------- | -------------------- |
| Source files (`.ts`, `.tsx`, etc.)   | Runs, `should_run=true`  | Runs lint + typecheck | Passes if checks pass  | CI validates code    |
| Docs/config only (`.md`, `.github/`) | Runs, `should_run=false` | Skipped               | Passes (skipped is OK) | No wasted CI minutes |
| Mix of source + docs                 | Runs, `should_run=true`  | Runs lint + typecheck | Passes if checks pass  | CI validates code    |

### Why `!cancelled()` instead of `always()`?

The gate uses `if: ${{ !cancelled() }}` rather than `if: always()`. Both handle the skipped case correctly, but `always()` has a known issue where it can get stuck waiting when a `needs` dependency calls a reusable workflow. `!cancelled()` resolves reliably in all cases.

---

## Renovate automerge configuration

The shareable preset never sets `automerge: true`. Automerge only happens if the **consuming repository** adds it in `.github/renovate.json`.

This shared-workflows repo's [`.github/renovate.json`](../.github/renovate.json) is unrelated: it automerges a small allowlist of GitHub Actions used here, and should not be copied into an application repo.

### Example consuming-repo config

```json
{
	"$schema": "https://docs.renovatebot.com/renovate-schema.json",
	"extends": [
		"config:recommended",
		"github>maker-tech/ci-github-actions-shared:renovate-preset"
	],
	"platformAutomerge": false,
	"packageRules": [
		{
			"description": "Automerge patch and minor updates",
			"matchUpdateTypes": ["minor", "patch"],
			"automerge": true,
			"automergeType": "pr"
		},
		{
			"description": "Shared CI workflows stay on manual review",
			"matchDepNames": ["maker-tech/ci-github-actions-shared"],
			"automerge": false
		},
		{
			"description": "Sensitive packages stay on manual review",
			"matchPackageNames": [
				"next",
				"react",
				"react-dom",
				"next-auth",
				"/^@builder\\.io//"
			],
			"automerge": false
		}
	]
}
```

Later `packageRules` override earlier ones, so the shared-workflow and sensitive-package rules must come **after** the blanket minor/patch automerge rule. Without those exclusions, enabling automerge for all minor/patch updates would also merge shared workflow PRs (the preset only adds a review checklist; it does not disable automerge).

### What automerges (with the example above)

- **Patch** and **minor** updates for most dependencies.
- Renovate opens a PR, CI runs, and once checks pass Renovate merges it automatically — no human approval needed.
- Updates must be at least **14 days old** (`minimumReleaseAge` from the preset) before Renovate will create a PR.
- Both PR creation (`schedule`) and automerging (`automergeSchedule`) run **before 8am every weekday** (NZT). The preset already sets these windows. `automergeSchedule` does nothing until `automerge: true` is set.

### What still requires manual review

- **Major** version updates (the preset sets `automerge: false`).
- **Shared CI workflows** (`maker-tech/ci-github-actions-shared`) if you keep the exclusion above.
- **Sensitive packages** you list — even patch/minor updates. The example uses:
  - `next` (framework — known for breaking changes in patches)
  - `react` / `react-dom` (core runtime)
  - `next-auth` (authentication — security-critical)
  - `@builder.io/*` (CMS integration)
- **Vulnerability alerts** still follow the automerge rules but skip the 14-day waiting period.

### Key setting: `platformAutomerge: false`

This is critical for the bypass to work in the consuming repository. It tells Renovate to merge PRs using its own API token rather than GitHub's native auto-merge. Since Renovate is a bypass actor on Ruleset A, its merge call bypasses the review requirement. If this were `true`, GitHub's auto-merge would wait for reviews and the PR would be stuck.

---

## Validation

After completing the setup in the consuming repository, verify each scenario works correctly.

### Test 1 — Renovate patch/minor PR automerges

This test only applies after the consuming repo has `automerge: true` for patch/minor (see the example config above). Extending the preset alone is not enough.

1. Wait for the next Renovate schedule (before 8am on any weekday, NZT) or trigger a manual run from the Dependency Dashboard issue.
2. A patch or minor PR (not a shared-workflow or sensitive-package PR) should:
   - Show `CI Gate` and `Lint PR Title` checks running.
   - Display "2 approvals required" in the merge box (this is normal — the UI always shows it).
   - Merge automatically once checks pass, **without any human approval**.

### Test 2 — Sensitive package and shared-workflow PRs require review

1. If a PR appears for `next`, `react`, `next-auth`, `@builder.io/*`, or `maker-tech/ci-github-actions-shared`:
   - It should NOT automerge.
   - It should require 2 human approvals before merging.

### Test 3 — Human PR requires reviews + checks

1. Open a PR with source file changes targeting `dev`.
2. Confirm it requires 2 approvals AND passing `CI Gate` + `Lint PR Title`.

### Test 4 — Docs-only PR passes without CI running

1. Open a PR that only changes `.md` files or files inside `.github/`.
2. `CI Gate` should appear and pass (the `checks` job is skipped, gate passes).
3. PR still requires 2 human approvals (Ruleset A).

### Test 5 — Admin force-merge

1. As an Org admin, open a PR where a check is failing or stuck.
2. The admin should be able to merge despite failing checks (Ruleset B bypass).
3. The admin should be able to merge without reviews (Ruleset A bypass).

---

## Troubleshooting

| Symptom                                         | Likely cause                                                                                            | Fix                                                             |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------- |
| Renovate PR stuck waiting for reviews           | Renovate not in Ruleset A bypass list, or classic branch protection still exists                        | Check Ruleset A bypass list; delete classic branch protection   |
| `CI Gate` shows "Expected — Waiting" forever    | CI Gate check name doesn't match what's in Ruleset B                                                    | Verify the exact check name in the workflow matches the ruleset |
| Human PR blocked by a check that never runs     | A Renovate-only check (FOSSA, stability-days) was added to Ruleset B                                    | Remove it — only add checks that run on all PRs                 |
| Renovate PR doesn't automerge after checks pass | No `automerge: true` rule in the consuming repo (the shareable preset does not enable it)               | Add the package rules from the example config above             |
| Renovate PR doesn't automerge after checks pass | `platformAutomerge` is set to `true` instead of `false` in `renovate.json`                              | Change to `false` so Renovate merges via its own token          |
| Shared workflow PR automerged unexpectedly      | Blanket minor/patch `automerge: true` with no later exclusion for `maker-tech/ci-github-actions-shared` | Add `automerge: false` for that dep after the blanket rule      |
| Admin can't force-merge                         | Admin not in bypass list for both rulesets                                                              | Add admin to bypass list on both Ruleset A and B                |
