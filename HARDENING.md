<!-- markdownlint-disable -->

# Hardening Report: launchdarkly--gha-flags/v1.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **launchdarkly--gha-flags/v1.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference external actions using mutable tags or branch names instead of full 40-character SHA digests, making them vulnerable to supply-chain attacks if the referenced tag or branch is moved.

.github/workflows/ci.yml:
  - uses: actions/checkout@v3 (lines 20, 38, 68, 101, 119, 137, 157, 176)
  - uses: actions/setup-python@v3 (line 24)
  - uses: actions/setup-node@v3 (line 25)
  - uses: pre-commit/action@v3.0.0 (line 27)

.github/workflows/lint-pr-title.yml:
  - uses: launchdarkly/gh-actions/.github/workflows/lint-pr-title.yml@main (line 12)

.github/workflows/release-please.yml:
  - uses: google-github-actions/release-please-action@v3 (line 10)

All of these should be pinned to a full commit SHA (e.g. actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683).

Locations:

- `.github/workflows/ci.yml:20`
- `.github/workflows/ci.yml:24`
- `.github/workflows/ci.yml:25`
- `.github/workflows/ci.yml:27`
- `.github/workflows/lint-pr-title.yml:12`
- `.github/workflows/release-please.yml:10`

### missing-permissions (severity: medium)

Two workflow files have no top-level `permissions:` key and no job-level `permissions:` blocks on any of their jobs. Without explicit permissions, GitHub Actions defaults to the repository's default token permissions (which may be read-write), granting more access than necessary.

- .github/workflows/ci.yml: 8 jobs (ci-build, flag-variations, offline-mode-uses-defaults, can-target-custom-context, can-target-custom-context-with-default-key, can-target-custom-context-with-custom-attribute, can-target-runner, can-target-github-envs) — none have permissions: blocks.
- .github/workflows/lint-pr-title.yml: 1 job (lint-pr-title) — no permissions: block. This is especially concerning because the workflow is triggered by pull_request_target, which runs with write access to the base repository.

Add a top-level `permissions: {}` (or minimal specific scopes) to each file.

Locations:

- `.github/workflows/ci.yml:1`
- `.github/workflows/lint-pr-title.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all unpinned action references by pinning to full 40-character SHA digests (with original tag names preserved as comments): actions/checkout@v3→f43a0e5ff2bd294095638e18286ca9a3d1956744, actions/setup-python@v3→3542bca2639a428e1796aaa6a2ffef0c0f575566, actions/setup-node@v3→3235b876344d2a9aa001b8d1453c930bba69e610, pre-commit/action@v3.0.0→646c83fcd040023954eafda54b4db0192ce70507, launchdarkly/gh-actions@main→719c66e7dc9a39c5767105d9b32c659239372ad0, google-github-actions/release-please-action@v3→db8f2c60ee802b3748b512940dde88eabd7b7e01. Added top-level `permissions: {}` to ci.yml and lint-pr-title.yml. The release-please.yml already had appropriate job-level permissions (contents: write, pull-requests: write) for release-please to function.

