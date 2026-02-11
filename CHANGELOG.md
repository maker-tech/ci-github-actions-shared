## 0.1.0 (2026-02-11)


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

