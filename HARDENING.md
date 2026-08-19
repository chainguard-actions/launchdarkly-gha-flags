<!-- markdownlint-disable -->

# Hardening Report: launchdarkly--gha-flags/v1.0.4

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **launchdarkly--gha-flags/v1.0.4** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple workflow files reference external actions using mutable tags or branch names instead of pinned 40-character commit SHAs, making the workflow vulnerable to supply-chain attacks if those tags are moved.

In .github/workflows/ci.yml:
- `uses: actions/checkout@v3` (appears multiple times)
- `uses: actions/setup-python@v3`
- `uses: actions/setup-node@v3`
- `uses: pre-commit/action@v3.0.0`

In .github/workflows/lint-pr-title.yml:
- `uses: launchdarkly/gh-actions/.github/workflows/lint-pr-title.yml@main`

In .github/workflows/release-please.yml:
- `uses: google-github-actions/release-please-action@v3`

All of these should be pinned to full 40-character commit SHAs (e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v3`).

Locations:

- `.github/workflows/ci.yml:18`
- `.github/workflows/ci.yml:20`
- `.github/workflows/ci.yml:21`
- `.github/workflows/ci.yml:23`
- `.github/workflows/ci.yml:31`
- `.github/workflows/lint-pr-title.yml:11`
- `.github/workflows/release-please.yml:11`

### missing-permissions (severity: medium)

ci.yml has no top-level `permissions:` key and none of its jobs define job-level `permissions:` blocks. This means the workflow runs with the default (broad) token permissions. Given that it is triggered on `pull_request` events, the GITHUB_TOKEN should be explicitly scoped to the minimum required permissions (e.g. `contents: read`).

Locations:

- `.github/workflows/ci.yml:1`

### missing-permissions (severity: medium)

lint-pr-title.yml has no top-level `permissions:` key and no job-level `permissions:` block. This workflow is triggered by `pull_request_target`, which runs with write access to the base repository and access to secrets. Without explicit minimal permissions, the delegated workflow (`launchdarkly/gh-actions/.github/workflows/lint-pr-title.yml@main`) inherits overly broad token permissions. A `permissions: {}` or minimal scoped block should be added.

Locations:

- `.github/workflows/lint-pr-title.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings across three workflow files:

1. ci.yml: Pinned all 8 `actions/checkout@v3` references to SHA a37ce9120846195fa4ece8f58b268e6043cb2f26, `actions/setup-python@v3` to SHA 3542bca2639a428e1796aaa6a2ffef0c0f575566, `actions/setup-node@v3` to SHA 3235b876344d2a9aa001b8d1453c930bba69e610, and `pre-commit/action@v3.0.0` to SHA 646c83fcd040023954eafda54b4db0192ce70507. Added top-level `permissions: contents: read`.

2. lint-pr-title.yml: Pinned `launchdarkly/gh-actions@main` to SHA 719c66e7dc9a39c5767105d9b32c659239372ad0. Added `permissions: {}` to restrict the broad token permissions on this pull_request_target-triggered workflow.

3. release-please.yml: Pinned `google-github-actions/release-please-action@v3` to SHA db8f2c60ee802b3748b512940dde88eabd7b7e01. This file already had appropriate job-level permissions (contents: write, pull-requests: write) so no permissions change was needed.

