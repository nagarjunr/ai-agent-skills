---
name: python-env-first
description: >-
  Pick the correct Python interpreter and environment before running any Python, pip or script in a
  project. Use at the start of any task that runs Python, installs packages, builds documents with
  Python libraries, or hits "No module named", SSL certificate errors, "bad interpreter" or syntax
  and stdlib errors that suggest an old interpreter. Prevents using an outdated system Python,
  creating a duplicate virtual environment, or installing packages globally.
---

# Python environment first

Most "Python problems" on a developer machine are not code problems. The default `python3` is often an old system build (3.6 or 3.7) with no modern standard library, no current root certificates, and none of the project's packages. Picking the right interpreter first avoids a round of confusing errors.

## Procedure

1. **Look for the project's own environment.**
   ```bash
   ls -d .venv venv env 2>/dev/null; ls pyproject.toml requirements*.txt 2>/dev/null
   ```
   If a virtual environment exists, use its interpreter directly (`.venv/bin/python`), not the shell's `python3`. Do not create a new one.
2. **Confirm what you will run.**
   ```bash
   .venv/bin/python --version
   which -a python3; python3 --version
   ```
   Note the versions. Anything below the project's required version is a stop sign, not something to work around.
3. **If there is no project environment**, prefer the newest interpreter installed (for example a Homebrew `python3.x` under `/opt/homebrew/bin`) over the system one, and ask before creating a new environment. Say which interpreter you chose and why.
4. **Install into the environment, never globally.** `python -m pip install ...` with the chosen interpreter. Ask before adding dependencies the project does not list.
5. **Run scripts safely.** Use `python -I` (isolated mode) when the current directory contains downloaded or untrusted files, so nothing in it is imported by name. Keep your own scripts in a different directory and pass data paths as arguments.

## Typical failures and fixes

| Symptom | Likely cause | Fix |
|---|---|---|
| `AttributeError: module 'math' has no attribute 'dist'` or similar missing stdlib function | Interpreter older than 3.8 | Use a newer interpreter or the project venv |
| `CERTIFICATE_VERIFY_FAILED` | Python without root certificates | Use the venv (with `certifi`) or a Homebrew Python; for python.org builds run its `Install Certificates.command`; do not disable verification |
| `bad interpreter: .../old/path/.venv/bin/python` | Venv or project folder was moved or renamed | Rewrite the old path in `.venv/bin/*` scripts (`pip`, `activate`, `pyvenv.cfg`), or recreate the venv if the user agrees |
| `No module named X` | Wrong interpreter, package not in the venv | Run with the venv interpreter and install there |

## Make it stick

Record the interpreter and venv path in the project's `CLAUDE.md` (or equivalent) so future sessions use it from the start, for example: "Use `.venv/bin/python` (Python 3.13). Never use the system python3."
