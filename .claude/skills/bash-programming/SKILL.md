---
name: bash-programming
description: Expert bash scripting guidance with strict mode, error handling, and shellcheck compliance. Use when writing shell scripts, bash functions, or command-line utilities.
---

# Bash Script Expert

## Required preamble for EVERY script

```bash
#!/usr/bin/env bash
set -Eeuo pipefail
trap 'echo >&2 "❌ [${BASH_SOURCE[0]}:$LINENO]: $BASH_COMMAND: exit $?"' ERR
SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
```

## Style Rules

### DO:
- Use inline code instead of functions unless reused
- Show only errors and essential output (silent success)
- Use short error messages: `{ echo >&2 "Swift required"; exit 1; }`
- Use direct values instead of variables if only used once
- Use `|| true` to ignore acceptable failures
- Test with `shellcheck` before considering complete

### DON'T:
- Create print_status(), print_error() wrapper functions
- Announce obvious actions ("Building...", "Installing...")
- Add colors unless specifically requested
- Add script version numbers unless requested
- Wrap single commands in functions

## Good Style Examples

```bash
# Compact prerequisite check
command -v swift &>/dev/null || { echo >&2 "Swift required"; exit 1; }

# Direct conditional action
[[ "$(uname)" == "Darwin" ]] && xattr -d com.apple.quarantine file 2>/dev/null || true
```
