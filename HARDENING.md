<!-- markdownlint-disable -->

# Hardening Report: ActionCommons--build-godot/v1.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **ActionCommons--build-godot/v1.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The 'Verify Output' run: block in ci.yml directly interpolates GitHub Actions expressions inside shell command strings. The expressions ${{ steps.build.outputs.directory }}, ${{ matrix.config.options }}, and ${{ steps.build.outputs.header_archive }} are substituted by the Actions template engine before the shell ever sees the string, allowing any newlines or shell metacharacters in those values to be interpreted by the shell. These should be passed via env: variables and referenced as quoted shell variables (e.g., "$VAR") instead.

Locations:

- `.github/workflows/ci.yml:109`
- `.github/workflows/ci.yml:110`
- `.github/workflows/ci.yml:111`

### unpinned-uses (severity: high)

All uses: references across all workflow files use mutable version tags instead of immutable 40-character SHA commit hashes. This exposes the workflows to supply-chain attacks if any referenced action's tag is moved to point to malicious code. Failing references include: actions/checkout@v6, actions/setup-node@v6, actions/upload-artifact@v7, github/codeql-action/init@v4, github/codeql-action/autobuild@v4, github/codeql-action/analyze@v4, ruby/setup-ruby@v1, licensee/setup-licensed@v1.3.2, super-linter/super-linter/slim@v8.

Locations:

- `.github/workflows/check-dist.yml:29`
- `.github/workflows/check-dist.yml:34`
- `.github/workflows/check-dist.yml:68`
- `.github/workflows/ci.yml:27`
- `.github/workflows/ci.yml:32`
- `.github/workflows/ci.yml:50`
- `.github/workflows/ci.yml:93`
- `.github/workflows/codeql-analysis.yml:33`
- `.github/workflows/codeql-analysis.yml:38`
- `.github/workflows/codeql-analysis.yml:44`
- `.github/workflows/codeql-analysis.yml:49`
- `.github/workflows/licensed.yml:33`
- `.github/workflows/licensed.yml:38`
- `.github/workflows/licensed.yml:47`
- `.github/workflows/licensed.yml:50`
- `.github/workflows/linter.yml:29`
- `.github/workflows/linter.yml:34`
- `.github/workflows/linter.yml:44`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses

**Notes:**

Fixed script injection in ci.yml 'Verify Output' step by moving ${{ steps.build.outputs.directory }}, ${{ matrix.config.options }}, and ${{ steps.build.outputs.header_archive }} expressions into an env: block and referencing them as $BUILD_DIRECTORY, $MATRIX_OPTIONS, and $BUILD_HEADER_ARCHIVE in the shell script. Pinned all unpinned uses: references across check-dist.yml, ci.yml, codeql-analysis.yml, licensed.yml, and linter.yml to their full 40-character commit SHAs (actions/checkout, actions/setup-node, actions/upload-artifact, github/codeql-action/{init,autobuild,analyze}, ruby/setup-ruby, licensee/setup-licensed, super-linter/super-linter/slim), preserving the original tag in a trailing comment for readability.

