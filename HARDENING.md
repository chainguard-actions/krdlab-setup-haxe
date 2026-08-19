<!-- markdownlint-disable -->

# Hardening Report: krdlab--setup-haxe/v1.5.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **krdlab--setup-haxe/v1.5.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references in workflow files use mutable tag-based refs (@v3, @v2) instead of pinned 40-character SHA commit digests. This exposes the workflow to supply-chain attacks if the referenced action tag is moved or compromised.

Failing references:
- `.github/workflows/test.yml`: `actions/checkout@v3`
- `.github/workflows/codeql-analysis.yml`: `actions/checkout@v3`, `github/codeql-action/init@v2`, `github/codeql-action/autobuild@v2`, `github/codeql-action/analyze@v2`

Locations:

- `.github/workflows/test.yml:37`
- `.github/workflows/codeql-analysis.yml:30`
- `.github/workflows/codeql-analysis.yml:34`
- `.github/workflows/codeql-analysis.yml:41`
- `.github/workflows/codeql-analysis.yml:56`

### missing-permissions (severity: medium)

Neither `.github/workflows/test.yml` nor `.github/workflows/codeql-analysis.yml` declares a top-level `permissions:` block, and no job within either file declares job-level permissions. Without explicit permissions, workflows run with the default (potentially broad) token permissions, violating the principle of least privilege.

Locations:

- `.github/workflows/test.yml:1`
- `.github/workflows/codeql-analysis.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed both workflow files:

1. `.github/workflows/test.yml`:
   - Added top-level `permissions: {}` block
   - Pinned `actions/checkout@v3` → `@a37ce9120846195fa4ece8f58b268e6043cb2f26 # v3`

2. `.github/workflows/codeql-analysis.yml`:
   - Added top-level `permissions: {}` block
   - Added job-level permissions (`actions: read`, `contents: read`, `security-events: write`) required for CodeQL scanning
   - Pinned `actions/checkout@v3` → `@a37ce9120846195fa4ece8f58b268e6043cb2f26 # v3`
   - Pinned `github/codeql-action/init@v2` → `@b8d3b6e8af63cde30bdc382c0bc28114f4346c88 # v2`
   - Pinned `github/codeql-action/autobuild@v2` → `@b8d3b6e8af63cde30bdc382c0bc28114f4346c88 # v2`
   - Pinned `github/codeql-action/analyze@v2` → `@b8d3b6e8af63cde30bdc382c0bc28114f4346c88 # v2`

