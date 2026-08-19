<!-- markdownlint-disable -->

# Hardening Report: krdlab--setup-haxe/v2.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **krdlab--setup-haxe/v2.0.2** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Both workflow files reference external actions using mutable version tags (@v4) instead of immutable 40-character commit SHAs. A tag can be silently moved to point to a different (potentially malicious) commit, enabling supply-chain attacks.

Failing references in test.yml:
  - uses: actions/checkout@v4

Failing references in codeql-analysis.yml:
  - uses: actions/checkout@v4
  - uses: github/codeql-action/init@v4
  - uses: github/codeql-action/autobuild@v4
  - uses: github/codeql-action/analyze@v4

All should be pinned to their full 40-character SHA digest, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `.github/workflows/test.yml:44`
- `.github/workflows/codeql-analysis.yml:43`
- `.github/workflows/codeql-analysis.yml:47`
- `.github/workflows/codeql-analysis.yml:57`
- `.github/workflows/codeql-analysis.yml:68`

### missing-permissions (severity: medium)

The workflow file test.yml has no top-level `permissions:` key, and its only job (`build`) also has no job-level `permissions:` block. Without explicit permissions, the job inherits the default repository token permissions, which may be overly broad (write access to contents, etc.). A minimal explicit permissions block should be added.

Locations:

- `.github/workflows/test.yml:1`

### script-injection (severity: high)

Rule (a) violation: A `${{ ... }}` expression is interpolated directly inside a `run:` shell command string in test.yml. The expression `${{ matrix.lib_hxml }}` is substituted into the shell command before the shell parses it, allowing an attacker who can influence matrix values (e.g. via a forked PR or workflow_dispatch) to inject arbitrary shell commands.

Offending line (line 64):
  `run: haxelib install test-workflow/${{ matrix.lib_hxml }} --always`

Fix: move the value into an `env:` variable and double-quote it in the shell:
```yaml
env:
  LIB_HXML: ${{ matrix.lib_hxml }}
run: haxelib install "test-workflow/$LIB_HXML" --always
```

Locations:

- `.github/workflows/test.yml:64`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions, script-injection

**Notes:**

Fixed all three findings:
1. **unpinned-uses**: Pinned all 5 action references to full 40-char SHAs — `actions/checkout` → `11d5960a326750d5838078e36cf38b85af677262` (v4), and all three `github/codeql-action/*` steps → `e4fba868fa4b1b91e1fdab776edc8cfbe6e9fb81` (v4). Original tags preserved as inline comments.
2. **missing-permissions**: Added `permissions: {}` at the top level of `test.yml` to explicitly grant no permissions, since the workflow only uses the local action `./` and doesn't require any GitHub token access.
3. **script-injection**: In `test.yml` line 64, moved `${{ matrix.lib_hxml }}` out of the `run:` shell string into an `env:` block as `LIB_HXML`, and updated the shell command to use `"test-workflow/$LIB_HXML"` (double-quoted to prevent word splitting).

