# Chopdi

A book of accounts for the web. Chopdi brings the functionality of Frappe Books to a browser, rebuilt from scratch in FastAPI and React. It adds the accounting depth of ERPNext's Accounts module, features from Akaunting and Tally, and Frappe-style users, roles, permissions and API keys, so other applications can post their accounting into it.

- Double-entry ledger, with submit, cancel and amend.
- Several companies in one installation.
- Sales, purchases, payments and journal entries.
- Inventory and POS, banking and reconciliation, budgets, multi-currency, and the full report set.
- Runs anywhere cPanel's Python apps run, or on any plain server, with MySQL/MariaDB.

**Status:** planning. Nothing is built yet; the first milestone is the foundation (`docs/SCOPE.md` › Build order, step 1).

| Read this | For |
| --- | --- |
| `docs/SCOPE.md` | Scope, decisions and build order |
| `docs/PARITY.md`, `docs/PARITY_TALLY.md` | Everything to build, item by item |
| `docs/REFERENCE.md` | The projects Chopdi learns from (read, never copied) |
| `docs/API_INTEGRATION.md` | Posting from other applications |
| `AGENTS.md`, `CLAUDE.md` | How to contribute |

Chopdi is an independent project, not affiliated with Frappe Technologies, Akaunting or Tally Solutions. Their names are used only to describe compatibility of features.

## License

MIT. See `LICENSE`.
