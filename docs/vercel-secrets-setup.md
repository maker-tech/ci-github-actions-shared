# Vercel Secrets Setup

How to obtain the three secrets required for deploying to Vercel via the CLI in GitHub Actions.

## Required Secrets

| Secret             | Description                                  |
| ------------------ | -------------------------------------------- |
| `VERCEL_TOKEN`     | Personal access token or team-scoped token   |
| `VERCEL_ORG_ID`    | Internal team/org ID (format: `team_xxx`)    |
| `VERCEL_PROJECT_ID`| Internal project ID (format: `prj_xxx`)      |

---

## Step 1 — Get the Org ID and Project ID

The fastest way is to link the project locally. Run this in the project root:

```bash
npx vercel link
```

It will prompt you to select your team and project, then write a `.vercel/project.json` file:

```json
{
  "orgId": "team_xxxxxxxxxxxxxxxxxxxx",
  "projectId": "prj_xxxxxxxxxxxxxxxxxxxx"
}
```

Map these values to GitHub secrets:

- `orgId` → `VERCEL_ORG_ID`
- `projectId` → `VERCEL_PROJECT_ID`

> **Note:** `.vercel/project.json` is gitignored by default — do not commit it.

### Alternative: Vercel Dashboard

If you prefer to get the values manually:

- **Org/Team ID** — Vercel Dashboard → **Team Settings** → **General** → Team ID
- **Project ID** — Vercel Dashboard → select project → **Settings** → **General** → Project ID

---

## Step 2 — Create a Vercel Token

1. Go to **Vercel Dashboard** → **Account Settings** → **Tokens**
2. Click **Create Token**
3. Set a descriptive name (e.g. `github-actions-<project-name>`)
4. **Important:** Select the correct **Scope** — choose the team that owns the project, not your personal account
5. Copy the token immediately — it will not be shown again

Use this value → `VERCEL_TOKEN`

---

## Step 3 — Add Secrets to GitHub

Go to your GitHub repo → **Settings** → **Secrets and variables** → **Actions** → **New repository secret**

Add all three:

- `VERCEL_TOKEN`
- `VERCEL_ORG_ID`
- `VERCEL_PROJECT_ID`

---

## Verify

Confirm the token is valid and can see the project:

```bash
# Check who the token belongs to
npx vercel whoami --token=<your-token>

# List projects under the team
npx vercel ls --scope=<team-slug> --token=<your-token>
```

`whoami` should return your team slug. `ls` should list your project.

---

## Troubleshooting

If you get **"Project not found"** during deployment, check the following:

| Cause | Fix |
| --- | --- |
| Token scoped to personal account, but project is under a team | Recreate the token with the correct **team scope** |
| `VERCEL_ORG_ID` is your personal ID, but project is under a team (or vice versa) | Re-run `vercel link` and copy the correct `orgId` |
| Trailing whitespace or newline in a GitHub secret | Delete and re-add the secret, pasting cleanly |
| Token has access to multiple teams | Add `--scope` to the Vercel CLI command in your workflow: `npx vercel --prod --scope=${{ secrets.VERCEL_ORG_ID }} --token=${{ secrets.VERCEL_TOKEN }}` |
