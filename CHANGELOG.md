## [2.2.1](https://github.com/maker-tech/ci-github-actions-shared/compare/v2.2.0...v2.2.1) (2026-08-18)


### Bug Fixes

* **ci:** bump release actions to Node 24 and align docs to v2 ([#60](https://github.com/maker-tech/ci-github-actions-shared/issues/60)) ([ffb1335](https://github.com/maker-tech/ci-github-actions-shared/commit/ffb1335c17bd0e16a806c467c63d337471eeb3de))

## [2.2.0](https://github.com/maker-tech/ci-github-actions-shared/compare/v2.1.2...v2.2.0) (2026-08-17)


### Features

* set fail-fast default value to false ([#59](https://github.com/maker-tech/ci-github-actions-shared/issues/59)) ([f082826](https://github.com/maker-tech/ci-github-actions-shared/commit/f0828264d9266f80ef262c8c578ab81452269b32))

## [2.1.2](https://github.com/maker-tech/ci-github-actions-shared/compare/v2.1.1...v2.1.2) (2026-08-12)


### Bug Fixes

* **ci:** bump default Node.js to 24 and run pnpm/action-setup on Node 24 ([#58](https://github.com/maker-tech/ci-github-actions-shared/issues/58)) ([534dd2f](https://github.com/maker-tech/ci-github-actions-shared/commit/534dd2f9b4a2c8bfb35a4e93b86137c6403c5421))

## [2.1.1](https://github.com/maker-tech/ci-github-actions-shared/compare/v2.1.0...v2.1.1) (2026-05-10)


### Bug Fixes

* use cron job times for scheduling, set ignored authors ([#56](https://github.com/maker-tech/ci-github-actions-shared/issues/56)) ([c56e2bd](https://github.com/maker-tech/ci-github-actions-shared/commit/c56e2bd95ffd7d5b5b01b7eb5180165206983c22))

## [2.1.0](https://github.com/maker-tech/ci-github-actions-shared/compare/v2.0.4...v2.1.0) (2026-05-08)


### Features

* update renovate ([#54](https://github.com/maker-tech/ci-github-actions-shared/issues/54)) ([40b6fb4](https://github.com/maker-tech/ci-github-actions-shared/commit/40b6fb4b8a9d4a4fde7a46b37c08f7ec83c443d1))

## [2.0.4](https://github.com/maker-tech/ci-github-actions-shared/compare/v2.0.3...v2.0.4) (2026-03-18)


### Bug Fixes

* **ci:** update stale checkout reference in README ([#48](https://github.com/maker-tech/ci-github-actions-shared/issues/48)) ([4e695e4](https://github.com/maker-tech/ci-github-actions-shared/commit/4e695e4b8b3ddc851d871237fcecee797aa0f882))

## [2.0.3](https://github.com/maker-tech/ci-github-actions-shared/compare/v2.0.2...v2.0.3) (2026-03-18)


### Bug Fixes

* **ci:** update actions/checkout to v6.0.2 ([#47](https://github.com/maker-tech/ci-github-actions-shared/issues/47)) ([b316ef9](https://github.com/maker-tech/ci-github-actions-shared/commit/b316ef9e874bb8c3a7ee3a79c7ef5d1e9c6ba5c0))

## [2.0.2](https://github.com/maker-tech/ci-github-actions-shared/compare/v2.0.1...v2.0.2) (2026-03-18)


### Reverts

* **ci:** downgrade actions/checkout from v4.3.0 to v4.2.2 ([#45](https://github.com/maker-tech/ci-github-actions-shared/issues/45)) ([379845c](https://github.com/maker-tech/ci-github-actions-shared/commit/379845c6a8910616f8c29d6d933c5e696320e189))

## [2.0.1](https://github.com/maker-tech/ci-github-actions-shared/compare/v2.0.0...v2.0.1) (2026-03-03)


### Bug Fixes

* **ci:** pin conventional-changelog-action to release_branch ([#42](https://github.com/maker-tech/ci-github-actions-shared/issues/42)) ([794a482](https://github.com/maker-tech/ci-github-actions-shared/commit/794a48286c1fed84c0b043859aed156b1c47d123))

## [2.0.0](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.4.9...v2.0.0) (2026-03-02)


### ⚠ BREAKING CHANGES

* removes skip_automated_commits input from
release-version.yml. Consumers passing skip_automated_commits: true
must switch to the new deploy-vercel-cli-release.yml pattern.

- Delete examples/release-on-vercel-auto-deployment.yml
- Remove skip_automated_commits input and guard step from release-version.yml
- Add examples/deploy-vercel-cli-release.yml with vercel.json ignoreCommand
  setup docs for skipping production auto-deploys
- Update README with new Deploy via Vercel CLI + Release section and setup
  checklist including vercel.json snippet
- Update cross-references in promote-deploy-release.yml,
  promote-uat-to-main.yml, and release-version.yml examples

* feat!(ci): replace deployment_status pattern with Vercel CLI deploy + release (#40) ([8690cbf](https://github.com/maker-tech/ci-github-actions-shared/commit/8690cbff3fa8c6888825c6004938330a6c77e070)), closes [#40](https://github.com/maker-tech/ci-github-actions-shared/issues/40)

## [1.4.9](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.4.8...v1.4.9) (2026-03-02)


### Bug Fixes

* **ci:** use deployed commit SHA for skip_automated_commits guard ([#39](https://github.com/maker-tech/ci-github-actions-shared/issues/39)) ([dd350b1](https://github.com/maker-tech/ci-github-actions-shared/commit/dd350b1e6ccc8b2eee91792d9a3f01a045dd79ed))

## [1.4.8](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.4.7...v1.4.8) (2026-03-02)


### Bug Fixes

* **ci:** resolve working_directory before use to prevent invalid cache path ([#38](https://github.com/maker-tech/ci-github-actions-shared/issues/38)) ([8c2ba3f](https://github.com/maker-tech/ci-github-actions-shared/commit/8c2ba3f675f1dbd71975ea506a5acc96eadee4d3))

## [1.4.7](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.4.6...v1.4.7) (2026-03-02)


### Bug Fixes

* **ci:** warn against secrets in extra_env; reorganize README ([#37](https://github.com/maker-tech/ci-github-actions-shared/issues/37)) ([18dd1cd](https://github.com/maker-tech/ci-github-actions-shared/commit/18dd1cd55516ea37cfe6f988bc6d4fa40e0c1cd5))

## [1.4.6](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.4.5...v1.4.6) (2026-03-02)


### Bug Fixes

* **ci:** scope release branch guard to push events only ([#36](https://github.com/maker-tech/ci-github-actions-shared/issues/36)) ([851654f](https://github.com/maker-tech/ci-github-actions-shared/commit/851654f67730a48958a4cb331b59f9fbad117976))

## [1.4.5](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.4.4...v1.4.5) (2026-03-02)


### Bug Fixes

* **ci:** add skip_automated_commits input for deployment_status release pattern ([#35](https://github.com/maker-tech/ci-github-actions-shared/issues/35)) ([ed8ab15](https://github.com/maker-tech/ci-github-actions-shared/commit/ed8ab158dbed01fed8bbb64cdba4bd1d6b0bdd22))

## [1.4.4](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.4.3...v1.4.4) (2026-03-02)


### Bug Fixes

* **ci:** support consolidated promote-deploy-release orchestration ([#34](https://github.com/maker-tech/ci-github-actions-shared/issues/34)) ([272b807](https://github.com/maker-tech/ci-github-actions-shared/commit/272b8074e84c2fead331bf98188c9eefb07a8a41))

## [1.4.3](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.4.2...v1.4.3) (2026-02-20)


### Bug Fixes

* **ci:** add preset input to lint-pr-title workflow ([#33](https://github.com/maker-tech/ci-github-actions-shared/issues/33)) ([969b6c5](https://github.com/maker-tech/ci-github-actions-shared/commit/969b6c5e5e74ace1cdc5835a13ebc4350c099c4c))

## [1.4.2](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.4.1...v1.4.2) (2026-02-18)


### Bug Fixes

* **ci:** add cursor rule to enforce fix(ci) prefix for CI changes ([#32](https://github.com/maker-tech/ci-github-actions-shared/issues/32)) ([b2fcea5](https://github.com/maker-tech/ci-github-actions-shared/commit/b2fcea58bb30cc4bc5d0d54d528ea2cbf3d9d468))

## [1.4.1](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.4.0...v1.4.1) (2026-02-17)


### Bug Fixes

* **workflows:** replace github.workflow_ref with ci_ref input for shared repo checkout ([#25](https://github.com/maker-tech/ci-github-actions-shared/issues/25)) ([9036b84](https://github.com/maker-tech/ci-github-actions-shared/commit/9036b8434507535180cf29aa9dc08720f0f44044))

## [1.4.0](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.3.1...v1.4.0) (2026-02-17)


### Features

* **workflows:** add release_count input to release-version workflow ([#23](https://github.com/maker-tech/ci-github-actions-shared/issues/23)) ([448477a](https://github.com/maker-tech/ci-github-actions-shared/commit/448477a9d551e0c3256e6758b49dbe51cb3d997f))

## [1.3.1](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.3.0...v1.3.1) (2026-02-16)


### Bug Fixes

* **workflows:** pin consumer-repo checkout to github.sha for non-main default branches ([#22](https://github.com/maker-tech/ci-github-actions-shared/issues/22)) ([355d66c](https://github.com/maker-tech/ci-github-actions-shared/commit/355d66c1e04d25d93917cdd0818429080777700f))

## [1.3.0](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.2.2...v1.3.0) (2026-02-16)


### Features

* **workflows:** add repo-sync-labels workflow and example workflows ([#20](https://github.com/maker-tech/ci-github-actions-shared/issues/20)) ([34c38b9](https://github.com/maker-tech/ci-github-actions-shared/commit/34c38b983b55da26b97b2405aff1ca23f0711d5c))

## [1.2.2](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.2.1...v1.2.2) (2026-02-13)


### Bug Fixes

* **workflows:** expose tag output and update README examples ([#14](https://github.com/maker-tech/ci-github-actions-shared/issues/14)) ([4c20202](https://github.com/maker-tech/ci-github-actions-shared/commit/4c202028f5e3620d59d6096cdf7f9464e83165e6))

## [1.2.1](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.2.0...v1.2.1) (2026-02-13)


### Bug Fixes

* **workflows:** include repo name in Slack notification titles ([#13](https://github.com/maker-tech/ci-github-actions-shared/issues/13)) ([c58137d](https://github.com/maker-tech/ci-github-actions-shared/commit/c58137d92c49bdeddec42c03cef84c2d30419888))

## [1.2.0](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.1.1...v1.2.0) (2026-02-13)


### Features

* **workflows:** add reusable e2e-test workflow ([#11](https://github.com/maker-tech/ci-github-actions-shared/issues/11)) ([d0826ec](https://github.com/maker-tech/ci-github-actions-shared/commit/d0826ec942329712fa6578c33162375f467c29cf))

## [1.1.1](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.1.0...v1.1.1) (2026-02-12)


### Bug Fixes

* **ci:** update Slack webhook secret name in _release.yml ([#10](https://github.com/maker-tech/ci-github-actions-shared/issues/10)) ([c277c29](https://github.com/maker-tech/ci-github-actions-shared/commit/c277c292cf622aea5a1a05f41bf796fc7a6523e2))

## [1.1.0](https://github.com/maker-tech/ci-github-actions-shared/compare/v1.0.0...v1.1.0) (2026-02-12)


### Features

* **ci:** add automated actionlint and document workflow authoring rules ([#8](https://github.com/maker-tech/ci-github-actions-shared/issues/8)) ([eea427d](https://github.com/maker-tech/ci-github-actions-shared/commit/eea427d4ea935f5fd44151a60fc63df0b799b633))


### Bug Fixes

* **ci:** dereference major version tag to commit and document convention ([#7](https://github.com/maker-tech/ci-github-actions-shared/issues/7)) ([7b50870](https://github.com/maker-tech/ci-github-actions-shared/commit/7b5087044088661831e757beab800844d3639fa4))

## [1.0.0](https://github.com/maker-tech/ci-github-actions-shared/compare/2886e7c6ab118a3f7089db20ba575d9ecbd28766...v1.0.0) (2026-02-11)


### ⚠ BREAKING CHANGES

* consumers must update their secret references from
ADMIN_GITHUB_TOKEN to CI_GITHUB_TOKEN.

Co-authored-by: Cursor <cursoragent@cursor.com>

* docs: update README with CI_GITHUB_TOKEN name and permissions

- Rename all ADMIN_GITHUB_TOKEN references to CI_GITHUB_TOKEN
- Add Permissions column to the secrets quick-start table
- Add Notes column to promote-branch and release-version secrets
  tables documenting the required fine-grained PAT permissions
  (Contents: Read and write)

Co-authored-by: Cursor <cursoragent@cursor.com>

### Features

* add common actions ([2886e7c](https://github.com/maker-tech/ci-github-actions-shared/commit/2886e7c6ab118a3f7089db20ba575d9ecbd28766))


### Bug Fixes

* avoid referencing secrets in step-level if conditions ([05de5d6](https://github.com/maker-tech/ci-github-actions-shared/commit/05de5d62bb73f09c8860f29649494258c1dae863))
* rename ADMIN_GITHUB_TOKEN to CI_GITHUB_TOKEN ([#5](https://github.com/maker-tech/ci-github-actions-shared/issues/5)) ([b42ff6f](https://github.com/maker-tech/ci-github-actions-shared/commit/b42ff6f4627c5bb863dd4a9e6b8e7eae99639970))


### Reverts

* Revert "chore(release): v0.1.0 [skip ci]" ([c1577fc](https://github.com/maker-tech/ci-github-actions-shared/commit/c1577fc257e4e045b20c15aa33af157837f95e0f))

