### Related Issue/Request:

<!--- Link to GitHub issue, Jira ticket, or Slack discussion that prompted this change --->

### This PR...

<!--- Short description of what this change accomplishes --->

### Type of Change:

<!--- Check all that apply --->

- [ ] 🐛 Bug fix (non-breaking change that fixes an issue)
- [ ] ✨ New feature (non-breaking change that adds functionality)
- [ ] 💥 Breaking change (fix or feature that would cause existing workflows to fail)
- [ ] 📝 Documentation update
- [ ] 🔧 Maintenance (refactoring, dependencies, CI config)

### Changes:

<!--- Bullet points of changes made --->

### Breaking Changes:

<!--- If this is a breaking change, describe:
1. What will break
2. Which client repos are affected
3. Migration steps for consumers
--->

N/A

### Notes:

<!--- Any extra context: why this approach was chosen, alternatives considered, follow-up work needed --->

---

## Testing

### How was this tested?

<!--- Describe how you verified the changes work --->

- [ ] Tested locally with [act](https://github.com/nektos/act) or similar
- [ ] Tested in a feature branch of a consuming repository
- [ ] Created a draft PR in a client repo to verify workflow execution
- [ ] Dry-run only (documentation/comment changes)

### Test repository (if applicable):

<!--- Link to test PR in a consuming repo, or note if N/A --->

---

## Security Checklist:

<!--- IMPORTANT: This is a PUBLIC repository --->

- [ ] No secrets, tokens, or credentials are hardcoded
- [ ] No internal URLs, IPs, or infrastructure details exposed
- [ ] No client-specific identifiers or project names
- [ ] All sensitive values use `secrets:` or `inputs:` parameters
- [ ] Git history reviewed for accidental secret commits

---

## Documentation:

- [ ] Input parameters are documented with descriptions
- [ ] ONBOARDING.md updated (if adding new workflows)
- [ ] Inline comments explain non-obvious logic
- [ ] CHANGELOG.md updated with user-facing changes

---

## Review Checklist:

- [ ] Workflow syntax is valid (`actionlint` passes or manually verified)
- [ ] Inputs have sensible defaults where appropriate
- [ ] Error handling covers common failure scenarios
- [ ] Slack/notification steps use `if: failure()` correctly
- [ ] Version pinning used for third-party actions (SHA or tag, not `@main`)
- [ ] Backward compatible with existing consumers (or breaking change documented)
- [ ] Tested with at least one consuming repository

---

## Versioning:

<!--- After merge, what version bump is needed? --->

- [ ] **Patch** (v1.0.x) - Bug fixes, minor improvements
- [ ] **Minor** (v1.x.0) - New features, backward compatible
- [ ] **Major** (vX.0.0) - Breaking changes

<!--- Reminder: After merge, create a release tag following semver --->
