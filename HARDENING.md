<!-- markdownlint-disable -->

# Hardening Report: jdx--mise-action/v4.2.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **jdx--mise-action/v4.2.2** was hardened automatically. 1 finding(s) were identified and resolved across 3 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): A `${{ matrix.requirements }}` expression is directly interpolated into a `run:` shell command in the `test` job's "Install requirements" step. The matrix value (`apk add --no-cache curl bash`) is defined in the workflow, but any `${{ ... }}` expression inside a `run:` block is a script-injection risk because YAML template substitution occurs before the shell ever sees the string — an attacker who can influence matrix values (e.g., via a fork PR modifying the workflow) could inject arbitrary shell commands. The value should be moved to an `env:` variable and double-quoted in the shell: `env:\n  REQUIREMENTS: ${{ matrix.requirements }}\nrun: "$REQUIREMENTS"`.

Locations:

- `.github/workflows/test.yml:49`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in .github/workflows/test.yml at the 'Install requirements' step (line 49). Moved `${{ matrix.requirements }}` out of the `run:` shell string into an `env:` block as `REQUIREMENTS: ${{ matrix.requirements }}`, and changed the run command to reference the shell variable `$REQUIREMENTS`. This prevents YAML template substitution from occurring before the shell processes the command, eliminating the injection risk.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed the script-injection finding in .github/workflows/test.yml at line 51. Changed `run: $REQUIREMENTS` to `run: "$REQUIREMENTS"` in the 'Install requirements' step. The env var REQUIREMENTS was set from `${{ matrix.requirements }}` (a matrix context value) and was used unquoted, allowing shell word-splitting and glob expansion. Quoting it prevents metacharacter injection while preserving the intended behavior of running the requirements installation command.

### Iteration 1

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in .github/workflows/test.yml at the 'Install requirements' step. The original code executed `$REQUIREMENTS` (set from `${{ matrix.requirements }}`) directly as a shell command. The fix replaces this with a strict equality check in the `if` condition (`matrix.requirements == 'apk add --no-cache curl bash'`) and a hardcoded `run: apk add --no-cache curl bash` command, eliminating the injection vulnerability while preserving the intended functionality for the alpine matrix entry.

