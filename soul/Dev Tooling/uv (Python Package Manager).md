---
tags: [python, tooling, cli, package-management]
source: [oh-my-zsh uv plugin docs]
---
# uv (Python Package Manager)

**uv** is a fast Python package and project manager (from Astral) that replaces the pip/venv/poetry combo with a single tool for dependency resolution, virtual environments, locking, and Python version management.

## Core commands
| Command | Purpose |
|---|---|
| `uv add` | Add a package to the project |
| `uv remove` | Remove a package from the project |
| `uv init` | Initialize a new project |
| `uv lock` | Lock dependencies (`--refresh` rebuild without upgrading, `--upgrade` upgrade to newest compatible) |
| `uv sync` | Sync the environment to the lock file (`--refresh` ignore cache, `--upgrade` allow upgrades ignoring lock) |
| `uv run` | Run a command inside the project's environment |
| `uv python install / list / pin / uninstall` | Manage installed Python versions and pin a project to one |
| `uv venv` | Manage virtual environments directly |
| `uv export --format requirements-txt` | Export the lock file to a `requirements.txt` for interop with pip-only tooling |
| `uv self update` | Update uv itself |
| `uv tree` | Show the full dependency tree |

The oh-my-zsh `uv` plugin wraps these in short aliases (e.g. `uvs` = `uv sync`, `uvl` = `uv lock`, `uva` = `uv add`) and adds shell completions.

## When to use / gotchas
- `uv lock` vs `uv sync`: locking updates the lock file to reflect `pyproject.toml`; syncing makes the actual environment match the lock file. Running code with stale deps usually means "sync," not "lock."
- `uv sync --upgrade` ignores the existing lock file's pinned versions — use deliberately, not as a routine sync.
- `uv python pin` scopes a Python version to the project (writes `.python-version`), which matters when multiple projects need different interpreter versions on one machine.

## Related
[[Docker CLI (oh-my-zsh plugin)]] · [[Paired Config Flags That Silently Fall Back]]
