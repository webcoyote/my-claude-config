---
name: uv-python-scripts
description: Use uv (not pip, poetry, or plain python) for any new Python script or project. Single-file scripts get a uv shebang with inline PEP 723 metadata; multi-file projects use a pyproject.toml managed by uv. Triggers when creating a .py file, starting a Python project, adding a dependency, or the user says "write a python script".
---

# uv for Python

Use `uv` for everything Python — running scripts, managing deps, virtualenvs, and projects. Don't use `pip`, `pip-tools`, `poetry`, `pipenv`, `venv`, or `python -m pip` directly.

## Decision: single script vs. project

- **One file, no shared state with other code → single script** with PEP 723 inline metadata + uv shebang. Fully self-contained, no `pyproject.toml`.
- **Anything bigger** (multiple modules, importable package, tests, CLI entry point) → **uv project** with `pyproject.toml`.

## Single-file script template

```python
#!/usr/bin/env -S uv run --script
# /// script
# requires-python = ">=3.12"
# dependencies = [
#   "httpx",
#   "rich",
# ]
# ///
"""Short description of what this script does."""

import httpx
from rich import print

def main() -> None:
    r = httpx.get("https://example.com")
    print(r.status_code)

if __name__ == "__main__":
    main()
```

After writing, `chmod +x script.py` so it runs directly. The shebang `#!/usr/bin/env -S uv run --script` works on macOS and Linux; `-S` is required so `env` accepts the multi-word argument. uv resolves and caches deps on first run.

To add a dep to an existing script: `uv add --script script.py <pkg>`.

## Project template (multi-file)

Initialize:

```bash
uv init <name>          # creates pyproject.toml, .python-version, src layout, README
cd <name>
uv add <runtime-deps>
uv add --dev pytest ruff
```

Run things via uv (it manages the venv automatically):

```bash
uv run python -m mypkg
uv run pytest
uv run ruff check
```

Commit `pyproject.toml` and `uv.lock`. Don't commit `.venv/`.

## Command translation

| Instead of                  | Use                          |
|-----------------------------|------------------------------|
| `python script.py`          | `uv run script.py` (or run via shebang) |
| `python -m venv .venv`      | (skip — `uv run` handles it) |
| `pip install <pkg>`         | `uv add <pkg>` (project) or `uv add --script file.py <pkg>` |
| `pip install -r reqs.txt`   | `uv pip install -r reqs.txt` (compat shim) or migrate to pyproject |
| `pipx install <tool>`       | `uv tool install <tool>`     |
| `pipx run <tool>`           | `uvx <tool>`                 |
| `poetry add <pkg>`          | `uv add <pkg>`               |
| `poetry install`            | `uv sync`                    |

## Don't

- Don't write `requirements.txt` for new work — use `pyproject.toml` or PEP 723 inline metadata.
- Don't create or activate a venv manually; `uv run` does it.
- Don't use a plain `#!/usr/bin/env python3` shebang for scripts that have dependencies — those will break on machines without those deps installed globally. Use the uv shebang.
- Don't pin Python via `python_requires` in setup.py; use `requires-python` in pyproject or the script header.

## If uv isn't installed

`brew install uv` (macOS) or `curl -LsSf https://astral.sh/uv/install.sh | sh`. Don't fall back to pip.
