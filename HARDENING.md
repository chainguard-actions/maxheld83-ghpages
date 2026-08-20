<!-- markdownlint-disable -->

# Hardening Report: maxheld83--ghpages/v0.3.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **maxheld83--ghpages/v0.3.0** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow uses 'actions/checkout@v1' which is pinned to a mutable tag (@v1) rather than an immutable 40-character commit SHA. A tag can be moved to point to a different (potentially malicious) commit, enabling supply-chain attacks.

Locations:

- `.github/workflows/main.yml:11`

### missing-permissions (severity: medium)

The workflow file '.github/workflows/main.yml' has no top-level 'permissions:' key, and the only job ('deploy_ghpages') also has no job-level 'permissions:' key. Without explicit permissions, the GITHUB_TOKEN is granted its default (potentially broad) permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/main.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed two findings in .github/workflows/main.yml: (1) Pinned 'actions/checkout@v1' to its immutable commit SHA '50fbc622fc4ef5163becd7fab6573eac35f8462e' with '# v1' comment for readability. (2) Added 'permissions: {}' at the top level to enforce least-privilege — the workflow uses a GH_PAT secret for deployment rather than the GITHUB_TOKEN, so no GITHUB_TOKEN permissions are needed.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed all unquoted shell variable expansions in entrypoint.sh and main.yml:
- entrypoint.sh line 6: `cd $BUILD_DIR` → `cd "$BUILD_DIR"`
- entrypoint.sh line 11: `echo $GITHUB_REPOSITORY` → `echo "$GITHUB_REPOSITORY"` (in REPONAME assignment)
- entrypoint.sh line 12: `echo $GITHUB_REPOSITORY` → `echo "$GITHUB_REPOSITORY"` (in OWNER assignment)
- entrypoint.sh line 23: `git push --force $REMOTE_REPO master:$REMOTE_BRANCH` → `git push --force "$REMOTE_REPO" "master:$REMOTE_BRANCH"`
- entrypoint.sh line 24: `cd $GITHUB_WORKSPACE` → `cd "$GITHUB_WORKSPACE"`
- .github/workflows/main.yml line 14: `echo $GITHUB_SHA` → `echo "$GITHUB_SHA"`

