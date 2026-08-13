<!-- markdownlint-disable -->

# Hardening Report: ActionCommons--build-godot/v1.0.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **ActionCommons--build-godot/v1.0.1** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Every `uses:` reference across all workflow files is pinned to a mutable tag or version string rather than a full 40-character commit SHA. This exposes the workflows to supply-chain attacks if any referenced action's tag is moved or compromised. Affected references:

- check-dist.yml: `actions/checkout@v6`, `actions/setup-node@v6`, `actions/upload-artifact@v7`
- ci.yml: `actions/checkout@v6`, `actions/setup-node@v6`, `actions/upload-artifact@v7`
- codeql-analysis.yml: `actions/checkout@v6`, `github/codeql-action/init@v4`, `github/codeql-action/autobuild@v4`, `github/codeql-action/analyze@v4`
- licensed.yml: `actions/checkout@v6`, `actions/setup-node@v6`, `ruby/setup-ruby@v1`, `licensee/setup-licensed@v1.3.2`
- linter.yml: `actions/checkout@v6`, `actions/setup-node@v6`, `super-linter/super-linter/slim@v8`

All should be replaced with their full SHA digest, e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`.

Locations:

- `.github/workflows/check-dist.yml:30`
- `.github/workflows/check-dist.yml:35`
- `.github/workflows/check-dist.yml:68`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:32`
- `.github/workflows/ci.yml:51`
- `.github/workflows/ci.yml:92`
- `.github/workflows/codeql-analysis.yml:34`
- `.github/workflows/codeql-analysis.yml:38`
- `.github/workflows/codeql-analysis.yml:44`
- `.github/workflows/codeql-analysis.yml:48`
- `.github/workflows/licensed.yml:32`
- `.github/workflows/licensed.yml:37`
- `.github/workflows/licensed.yml:44`
- `.github/workflows/licensed.yml:47`
- `.github/workflows/linter.yml:30`
- `.github/workflows/linter.yml:35`
- `.github/workflows/linter.yml:45`

### script-injection (severity: high)

The 'Verify Output' step in ci.yml directly interpolates GitHub Actions expressions into a `run:` shell script (sub-rule a). The expressions `${{ steps.build.outputs.directory }}`, `${{ matrix.config.options }}`, and `${{ steps.build.outputs.header_archive }}` are substituted into the shell command string by the Actions template engine before the shell ever executes it. A malicious value in `matrix.config.options` (which flows from the matrix definition, itself workflow-controllable) or in action outputs could inject arbitrary shell commands. These values must be passed via `env:` variables and then referenced as quoted shell variables (e.g. `"$VAR"`) instead of being interpolated directly with `${{ }}`.

Locations:

- `.github/workflows/ci.yml:108`
- `.github/workflows/ci.yml:109`
- `.github/workflows/ci.yml:110`
- `.github/workflows/ci.yml:111`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

Fixed all 18 unpinned `uses:` references across check-dist.yml, ci.yml, codeql-analysis.yml, licensed.yml, and linter.yml by pinning each to its full 40-character commit SHA (resolved via lookup_action_sha) with the original tag preserved as a comment. Fixed script injection in ci.yml's 'Verify Output' step by moving all three ${{ }} expressions (steps.build.outputs.directory, matrix.config.options, steps.build.outputs.header_archive) into an env: block and referencing them as quoted shell variables ($BUILD_DIRECTORY, $MATRIX_OPTIONS, $HEADER_ARCHIVE) in the run: script.

