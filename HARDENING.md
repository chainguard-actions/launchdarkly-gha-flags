<!-- markdownlint-disable -->

# Hardening Report: launchdarkly--gha-flags/v1.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **launchdarkly--gha-flags/v1.0.2** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references in ci.yml use mutable tag refs instead of pinned 40-character commit SHAs, making the workflow vulnerable to supply-chain attacks if those tags are moved or compromised. Failing references: `actions/checkout@v3`, `actions/setup-python@v3`, `actions/setup-node@v3`, `pre-commit/action@v3.0.0`.

Locations:

- `.github/workflows/ci.yml:17`
- `.github/workflows/ci.yml:20`
- `.github/workflows/ci.yml:21`
- `.github/workflows/ci.yml:23`
- `.github/workflows/ci.yml:30`

### unpinned-uses (severity: high)

The `uses:` reference in lint-pr-title.yml uses a mutable branch ref (`@main`) instead of a pinned 40-character commit SHA. Failing reference: `launchdarkly/gh-actions/.github/workflows/lint-pr-title.yml@main`.

Locations:

- `.github/workflows/lint-pr-title.yml:11`

### unpinned-uses (severity: high)

The `uses:` reference in release-please.yml uses a mutable tag ref instead of a pinned 40-character commit SHA. Failing reference: `google-github-actions/release-please-action@v3`.

Locations:

- `.github/workflows/release-please.yml:12`

### missing-permissions (severity: medium)

ci.yml has no top-level `permissions:` key and none of its jobs (ci-build, flag-variations, offline-mode-uses-defaults, can-target-custom-context, can-target-custom-context-with-default-key, can-target-custom-context-with-custom-attribute, can-target-runner, can-target-github-envs) define a `permissions:` block. This means the workflow runs with the default, overly broad GITHUB_TOKEN permissions.

Locations:

- `.github/workflows/ci.yml:1`

### missing-permissions (severity: medium)

lint-pr-title.yml has no top-level `permissions:` key and its only job (`lint-pr-title`) has no `permissions:` block. This workflow is triggered by `pull_request_target` (a privileged trigger), making the lack of explicit permissions especially risky.

Locations:

- `.github/workflows/lint-pr-title.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all findings across three workflow files:

1. ci.yml: Pinned all action references to full SHAs (actions/checkout@v3 → @a37ce9120846195fa4ece8f58b268e6043cb2f26, actions/setup-python@v3 → @3542bca2639a428e1796aaa6a2ffef0c0f575566, actions/setup-node@v3 → @3235b876344d2a9aa001b8d1453c930bba69e610, pre-commit/action@v3.0.0 → @646c83fcd040023954eafda54b4db0192ce70507). Added top-level `permissions: contents: read` block.

2. lint-pr-title.yml: Pinned launchdarkly/gh-actions reusable workflow @main → @54a4d773fe4ff458c0dc93bab1714fefcd77a526. Added top-level `permissions: contents: read` block (critical given the privileged pull_request_target trigger).

3. release-please.yml: Pinned google-github-actions/release-please-action@v3 → @db8f2c60ee802b3748b512940dde88eabd7b7e01. This file already had appropriate job-level permissions (contents: write, pull-requests: write) for release-please to function, so no permissions block was added at the top level.

