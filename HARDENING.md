<!-- markdownlint-disable -->

# Hardening Report: krdlab--setup-haxe/v2.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **krdlab--setup-haxe/v2.1.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A ${{ env.* }} expression is directly interpolated inside a run: shell command. The env variable TEST_LIB_HXML is set to `test-workflow/lib${{ matrix.hxml_suffix }}.hxml` (itself containing a matrix context value) and then interpolated directly into the shell command `haxelib install ${{ env.TEST_LIB_HXML }} --always`. Any ${{ ... }} expression inside a run: block is a script-injection risk because YAML template substitution occurs before the shell ever sees the value, allowing an attacker-controlled matrix or env value to inject shell metacharacters. The fix is to reference the env var as a shell variable instead: `haxelib install "$TEST_LIB_HXML" --always`.

Locations:

- `.github/workflows/test.yml:79`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in hardened/action/.github/workflows/test.yml line 79: replaced `haxelib install ${{ env.TEST_LIB_HXML }} --always` with `haxelib install "$TEST_LIB_HXML" --always`. The TEST_LIB_HXML env variable is already defined at the job level and is available as a shell environment variable, so referencing it as $TEST_LIB_HXML (double-quoted) is safe and avoids YAML template substitution before the shell sees the value.

