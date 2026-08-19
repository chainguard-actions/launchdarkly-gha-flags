<!-- markdownlint-disable -->

# Hardening Report: launchdarkly--gha-flags/v1.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **launchdarkly--gha-flags/v1.0.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference actions using mutable tags or branch names instead of pinned 40-character SHA commits, making them vulnerable to supply-chain attacks.

.github/workflows/ci.yml:
  - uses: actions/checkout@v3
  - uses: actions/setup-python@v3
  - uses: actions/setup-node@v3
  - uses: pre-commit/action@v3.0.0

.github/workflows/lint-pr-title.yml:
  - uses: launchdarkly/gh-actions/.github/workflows/lint-pr-title.yml@main

.github/workflows/release-please.yml:
  - uses: google-github-actions/release-please-action@v3

Locations:

- `.github/workflows/ci.yml:19`
- `.github/workflows/ci.yml:23`
- `.github/workflows/ci.yml:24`
- `.github/workflows/ci.yml:26`
- `.github/workflows/lint-pr-title.yml:10`
- `.github/workflows/release-please.yml:10`

### missing-permissions (severity: medium)

ci.yml has no top-level 'permissions:' key and none of its jobs (ci-build, flag-variations, offline-mode-uses-defaults, can-target-custom-context, can-target-custom-context-with-default-key, can-target-custom-context-with-custom-attribute, can-target-runner, can-target-github-envs) define a 'permissions:' block. This means the workflow runs with the default broad token permissions. Similarly, lint-pr-title.yml has no top-level or job-level 'permissions:' key despite being triggered by the sensitive 'pull_request_target' event, which runs with write access to the base repository.

Locations:

- `.github/workflows/ci.yml:1`
- `.github/workflows/lint-pr-title.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references by resolving them to full 40-character SHA commits:
- ci.yml: Pinned actions/checkout@v3 → @a37ce9120846195fa4ece8f58b268e6043cb2f26, actions/setup-python@v3 → @3542bca2639a428e1796aaa6a2ffef0c0f575566, actions/setup-node@v3 → @3235b876344d2a9aa001b8d1453c930bba69e610, pre-commit/action@v3.0.0 → @646c83fcd040023954eafda54b4db0192ce70507. Added top-level `permissions: contents: read` block.
- lint-pr-title.yml: Pinned launchdarkly/gh-actions@main → @54a4d773fe4ff458c0dc93bab1714fefcd77a526. Added top-level `permissions: contents: read / pull-requests: read` (minimal permissions for a PR title linter using the sensitive pull_request_target event).
- release-please.yml: Pinned google-github-actions/release-please-action@v3 → @db8f2c60ee802b3748b512940dde88eabd7b7e01. This file already had appropriate job-level permissions (contents: write, pull-requests: write) for release-please.

