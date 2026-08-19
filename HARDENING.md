<!-- markdownlint-disable -->

# Hardening Report: launchdarkly--gha-flags/v1.0.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **launchdarkly--gha-flags/v1.0.3** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference external actions using mutable tags or branch names instead of immutable full 40-character SHA commit hashes. This exposes the workflow to supply-chain attacks if the referenced tag or branch is moved to point to malicious code.

ci.yml:
  - uses: actions/checkout@v3
  - uses: actions/setup-python@v3
  - uses: actions/setup-node@v3
  - uses: pre-commit/action@v3.0.0

lint-pr-title.yml:
  - uses: launchdarkly/gh-actions/.github/workflows/lint-pr-title.yml@main  (mutable branch)

release-please.yml:
  - uses: google-github-actions/release-please-action@v3

Locations:

- `.github/workflows/ci.yml:17`
- `.github/workflows/ci.yml:19`
- `.github/workflows/ci.yml:20`
- `.github/workflows/ci.yml:22`
- `.github/workflows/lint-pr-title.yml:9`
- `.github/workflows/release-please.yml:12`

### missing-permissions (severity: medium)

ci.yml has no top-level `permissions:` key and none of its 7 jobs (ci-build, flag-variations, offline-mode-uses-defaults, can-target-custom-context, can-target-custom-context-with-default-key, can-target-custom-context-with-custom-attribute, can-target-runner, can-target-github-envs) define a job-level `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad.

lint-pr-title.yml uses the elevated `pull_request_target` trigger (which runs with write permissions on the base repository) and has no `permissions:` block, making it particularly risky.

Locations:

- `.github/workflows/ci.yml:1`
- `.github/workflows/lint-pr-title.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all 6 unpinned action references by resolving full 40-character SHA hashes via lookup_action_sha: actions/checkout@v3→f43a0e5ff2bd294095638e18286ca9a3d1956744, actions/setup-python@v3→3542bca2639a428e1796aaa6a2ffef0c0f575566, actions/setup-node@v3→3235b876344d2a9aa001b8d1453c930bba69e610, pre-commit/action@v3.0.0→646c83fcd040023954eafda54b4db0192ce70507, launchdarkly/gh-actions@main→719c66e7dc9a39c5767105d9b32c659239372ad0, google-github-actions/release-please-action@v3→db8f2c60ee802b3748b512940dde88eabd7b7e01. Added top-level 'permissions: {}' to ci.yml and lint-pr-title.yml; added job-level 'permissions: contents: read' to all 8 jobs in ci.yml; added job-level 'permissions: pull-requests: read' to the lint-pr-title job (minimum needed for pull_request_target PR title linting). release-please.yml already had appropriate job-level permissions.

