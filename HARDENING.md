<!-- markdownlint-disable -->

# Hardening Report: krdlab--setup-haxe/v2.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **krdlab--setup-haxe/v2.0.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Multiple `uses:` references in workflow files use mutable tag refs (@v4) instead of full 40-character SHA commit pins, making the action vulnerable to supply-chain attacks if the tag is moved.

In .github/workflows/test.yml:
- `actions/checkout@v4`

In .github/workflows/codeql-analysis.yml:
- `actions/checkout@v4`
- `github/codeql-action/init@v4`
- `github/codeql-action/autobuild@v4`
- `github/codeql-action/analyze@v4`

Locations:

- `.github/workflows/test.yml:40`
- `.github/workflows/codeql-analysis.yml:30`
- `.github/workflows/codeql-analysis.yml:34`
- `.github/workflows/codeql-analysis.yml:40`
- `.github/workflows/codeql-analysis.yml:56`

### permissions (severity: medium)

missing-permissions: .github/workflows/test.yml has no top-level `permissions:` key and its only job (`build`) also has no `permissions:` key. Without explicit permissions, the workflow inherits the default repository permissions, which may be overly broad (e.g., write access to contents).

Locations:

- `.github/workflows/test.yml:1`

### script-injection (severity: high)

Sub-rule (a): A `${{ matrix.lib_hxml }}` expression is interpolated directly inside a `run:` shell command. Per the check rules, `matrix.*` is an untrusted-input expression and any `${{ ... }}` directly inside a `run:` block is a script-injection risk — the value is substituted into the shell command string before the shell parses it, allowing metacharacter injection if the matrix value were attacker-influenced.

Offending line: `run: haxelib install test-workflow/${{ matrix.lib_hxml }} --always`

Fix: move the value into an `env:` variable and double-quote the shell expansion, e.g.:
```yaml
env:
  LIB_HXML: ${{ matrix.lib_hxml }}
run: haxelib install "test-workflow/$LIB_HXML" --always
```

Locations:

- `.github/workflows/test.yml:56`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, permissions, script-injection

**Notes:**

Fixed all three findings:
1. **unpinned-uses**: Pinned all action references to full SHA commits in both workflow files:
   - `actions/checkout@v4` → `@11d5960a326750d5838078e36cf38b85af677262 # v4` (in both test.yml and codeql-analysis.yml)
   - `github/codeql-action/init@v4` → `@e4fba868fa4b1b91e1fdab776edc8cfbe6e9fb81 # v4`
   - `github/codeql-action/autobuild@v4` → `@e4fba868fa4b1b91e1fdab776edc8cfbe6e9fb81 # v4`
   - `github/codeql-action/analyze@v4` → `@e4fba868fa4b1b91e1fdab776edc8cfbe6e9fb81 # v4`
2. **permissions**: Added `permissions: contents: read` top-level block to `.github/workflows/test.yml`.
3. **script-injection**: Moved `${{ matrix.lib_hxml }}` out of the `run:` shell command into an `env:` block as `LIB_HXML`, and referenced it as `"$LIB_HXML"` (double-quoted) in the shell command to prevent shell metacharacter injection.

