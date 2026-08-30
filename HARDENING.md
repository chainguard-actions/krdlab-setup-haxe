<!-- markdownlint-disable -->

# Hardening Report: krdlab--setup-haxe/v2.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **krdlab--setup-haxe/v2.1.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A GitHub Actions expression is directly interpolated inside a `run:` shell command string. At line 104 of test.yml: `run: haxelib install ${{ env.TEST_LIB_HXML }} --always`. The `env.*` context is workflow-controllable and flows through YAML template substitution before the shell processes it, allowing an attacker to inject arbitrary shell commands. The value should be passed via an `env:` block and referenced as a quoted shell variable (e.g., `"$TEST_LIB_HXML"`) instead.

Locations:

- `.github/workflows/test.yml:104`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in hardened/action/.github/workflows/test.yml at line 104. Moved `${{ env.TEST_LIB_HXML }}` from direct interpolation in the `run:` shell command to an `env:` block as `TEST_LIB_HXML_PATH`, and updated the shell command to reference it as the quoted variable `"$TEST_LIB_HXML_PATH"`. This prevents the workflow-controllable value from being interpreted as shell commands.

