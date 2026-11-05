# CLAUDE.md

This file guides Claude Code in this repository. The base branch is `main`. Keep one pull request open at a time: add work to it until it's merged, then branch the next one from `main`.

@AGENTS.md

## How work is done here

### Before the first edit

- Check `git status` and keep anyone else's work. Stage explicit paths only, never broad ones, and commit only when asked.
- Read `docs/SCOPE.md` (decisions and build order) and the parts of `docs/PARITY.md` the change covers.
- For each document type, report or screen you build, read its reference first:
  - `reference/books/`: the schema, the model and the UI;
  - `reference/erpnext/erpnext/accounts/`;
  - `reference/akaunting/`;
  - `reference/frappe/frappe/core/`.

  `docs/REFERENCE.md` says how to set these up. Say in the pull request which reference files, at which commit, the behaviour came from.
- If a git-ignored `CLAUDE.local.md` exists, it has this machine's extra instructions. Follow it too.

### Rules

`.claude/rules/` loads by itself: security every session; schema, accounting and frontend when you work on their files.

### A change is done when

1. **Tests:**
   - cases are written first;
   - pytest tests are added that fail without the change;
   - anything a person or a job does is run through the real UI or API;
   - accounting changes also check that the ledger balances and that cancel and amend work.
2. **Security check:** the diff has been reviewed against `.claude/rules/security.md`, and each finding is fixed or recorded with why not.
3. **Release readiness**, when it ships:
   - `pytest` passes in `server/`, on SQLite and on MariaDB;
   - a fresh-install check passes;
   - the version is bumped;
   - what wasn't verified is stated.
4. **Guidance:** a lasting convention goes in `AGENTS.md`, this file or `.claude/rules/` in the same change, and the `docs/PARITY.md` ticks are updated.

## Commands

None yet. The first change, the foundation (`docs/SCOPE.md` › Build order), creates `server/` and `frontend/`. Add the commands here then.
