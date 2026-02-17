## [1.3.1](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.3.0...v1.3.1) (2026-02-16)

### Bug Fixes

- **workflows:** pin consumer-repo checkout to github.sha for non-main default branches ([#22](https://github.com/maker-tech/ci-github-actions-shared/issues/22)) ([355d66c](https://github.com/maker-tech/ci-github-actions-shared/commit/355d66c1e04d25d93917cdd0818429080777700f))

## [1.3.0](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.2.2...v1.3.0) (2026-02-16)

### Features

- **workflows:** add repo-sync-labels workflow and example workflows ([#20](https://github.com/maker-tech/ci-github-actions-shared/issues/20)) ([34c38b9](https://github.com/maker-tech/ci-github-actions-shared/commit/34c38b983b55da26b97b2405aff1ca23f0711d5c))

## [1.2.2](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.2.1...v1.2.2) (2026-02-13)

### Bug Fixes

- **workflows:** expose tag output and update README examples ([#14](https://github.com/maker-tech/ci-github-actions-shared/issues/14)) ([4c20202](https://github.com/maker-tech/ci-github-actions-shared/commit/4c202028f5e3620d59d6096cdf7f9464e83165e6))

## [1.2.1](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.2.0...v1.2.1) (2026-02-13)

### Bug Fixes

- **workflows:** include repo name in Slack notification titles ([#13](https://github.com/maker-tech/ci-github-actions-shared/issues/13)) ([c58137d](https://github.com/maker-tech/ci-github-actions-shared/commit/c58137d92c49bdeddec42c03cef84c2d30419888))

## [1.2.0](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.1.1...v1.2.0) (2026-02-13)

### Features

- **workflows:** add reusable e2e-test workflow ([#11](https://github.com/maker-tech/ci-github-actions-shared/issues/11)) ([d0826ec](https://github.com/maker-tech/ci-github-actions-shared/commit/d0826ec942329712fa6578c33162375f467c29cf))

## [1.1.1](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.1.0...v1.1.1) (2026-02-12)

### Bug Fixes

- **ci:** update Slack webhook secret name in \_release.yml ([#10](https://github.com/maker-tech/ci-github-actions-shared/issues/10)) ([c277c29](https://github.com/maker-tech/ci-github-actions-shared/commit/c277c292cf622aea5a1a05f41bf796fc7a6523e2))

## [1.1.0](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.0.0...v1.1.0) (2026-02-12)

### Features

- **ci:** add automated actionlint and document workflow authoring rules ([#8](https://github.com/maker-tech/ci-github-actions-shared/issues/8)) ([eea427d](https://github.com/maker-tech/ci-github-actions-shared/commit/eea427d4ea935f5fd44151a60fc63df0b799b633))

### Bug Fixes

- **ci:** dereference major version tag to commit and document convention ([#7](https://github.com/maker-tech/ci-github-actions-shared/issues/7)) ([7b50870](https://github.com/maker-tech/ci-github-actions-shared/commit/7b5087044088661831e757beab800844d3639fa4))

## [1.0.0](https://github.com/maker-tech/ci-github-actions-shared/compare/2886e7c6ab118a3f7089db20ba575d9ecbd28766...v1.0.0) (2026-02-11)

### ⚠ BREAKING CHANGES

- consumers must update their secret references from
  ADMIN_GITHUB_TOKEN to CI_GITHUB_TOKEN.

- docs: update README with CI_GITHUB_TOKEN name and permissions

* Rename all ADMIN_GITHUB_TOKEN references to CI_GITHUB_TOKEN
* Add Permissions column to the secrets quick-start table
* Add Notes column to promote-branch and release-version secrets
  tables documenting the required fine-grained PAT permissions
  (Contents: Read and write)

### Features

- add common actions ([2886e7c](https://github.com/maker-tech/ci-github-actions-shared/commit/2886e7c6ab118a3f7089db20ba575d9ecbd28766))

### Bug Fixes

- avoid referencing secrets in step-level if conditions ([05de5d6](https://github.com/maker-tech/ci-github-actions-shared/commit/05de5d62bb73f09c8860f29649494258c1dae863))
- rename ADMIN_GITHUB_TOKEN to CI_GITHUB_TOKEN ([#5](https://github.com/maker-tech/ci-github-actions-shared/issues/5)) ([b42ff6f](https://github.com/maker-tech/ci-github-actions-shared/commit/b42ff6f4627c5bb863dd4a9e6b8e7eae99639970))

### Reverts

- Revert "chore(release): v0.1.0 [skip ci]" ([c1577fc](https://github.com/maker-tech/ci-github-actions-shared/commit/c1577fc257e4e045b20c15aa33af157837f95e0f))
