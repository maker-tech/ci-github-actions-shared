# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- Initial repository structure
- Reusable workflows:
  - `nextjs-ci.yml` - Lint, typecheck, and test (pnpm default, Node 22)
  - `deploy-chromatic.yml` - Deploy to Chromatic
  - `promote-branch.yml` - Merge one branch into another
  - `release-version.yml` - Conventional changelog + GitHub release
  - `lint-pr-title.yml` - Validate PR titles follow Conventional Commits
- Composite actions:
  - `setup-node` - Standardised Node.js setup with pnpm/npm caching
  - `nextjs-build` - Build Next.js applications
- Documentation:
  - README with client onboarding guide
  - PR template for workflow changes
  - CODEOWNERS for review requirements

### Notes

- Vercel deployment workflows not included - use Vercel's native GitHub integration instead
- Docker/GCP workflows can be added when needed

---

## Versioning Guide

When releasing:

- **Patch** (v1.0.x): Bug fixes, minor improvements, documentation updates
- **Minor** (v1.x.0): New features, new workflows, backward-compatible changes
- **Major** (vX.0.0): Breaking changes that require client updates

### Creating a Release

```bash
# Tag the release
git tag v1.0.0
git push origin v1.0.0

# For major versions, also update the floating tag
git tag -f v1
git push -f origin v1
```

### Client Upgrade Path

When making breaking changes (major version):

1. Document what will break in this changelog
2. List affected workflows
3. Provide migration steps
4. Announce via appropriate channels before merging
