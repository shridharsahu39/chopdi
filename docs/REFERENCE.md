# Reference sources

`reference/` holds read-only, shallow checkouts of the projects Chopdi rebuilds. Git ignores the folder; it is never committed or packaged.

| Folder | Project | Licence | What is checked out |
| --- | --- | --- | --- |
| `reference/books` | github.com/frappe/books | AGPL-3.0 | All of it |
| `reference/erpnext` | github.com/frappe/erpnext | GPL-3.0 | Sparse: `erpnext/accounts/`, `erpnext/setup/`, `erpnext/regional/india/`, `license.txt` |
| `reference/india-compliance` | github.com/resilient-tech/india-compliance | GPL-3.0 | All of it (GST: `india_compliance/gst_india/`; out of scope for now, D-5) |
| `reference/akaunting` | github.com/akaunting/akaunting | Business Source License 1.1 (not open source; see D-11) | All of it (Laravel: `app/Models/`, `app/Reports/`, `app/Widgets/`, `routes/`) |
| `reference/frappe` | github.com/frappe/frappe | MIT | Sparse: `frappe/core/`, `frappe/api/`, `frappe/auth.py`, `frappe/permissions.py`, the OAuth doctypes, `LICENSE` |

The commits checked out are at the top of `docs/PARITY.md`.

- **Read, don't copy** (D-1): behaviour is learned from these files and written fresh; the UI takes Books' presentation and components, not its code or theme (D-10). When a change relies on one, its record names the file and commit.
- **Set up on a new machine:**

```bash
mkdir -p reference && cd reference
git clone --depth 1 https://github.com/frappe/books.git books
git clone --depth 1 --filter=blob:none --sparse https://github.com/frappe/erpnext.git erpnext
(cd erpnext && git sparse-checkout set --no-cone '/erpnext/accounts/' '/erpnext/setup/' '/erpnext/regional/india/' '/license.txt')
git clone --depth 1 https://github.com/resilient-tech/india-compliance.git india-compliance
git clone --depth 1 https://github.com/akaunting/akaunting.git akaunting
git clone --depth 1 --filter=blob:none --sparse https://github.com/frappe/frappe.git frappe
(cd frappe && git sparse-checkout set --no-cone '/frappe/core/' '/frappe/permissions.py' '/frappe/auth.py' '/frappe/api/' \
  '/frappe/integrations/doctype/oauth_client/' '/frappe/integrations/doctype/oauth_bearer_token/' '/LICENSE')
```

- **Refresh:** run `git -C reference/<name> pull --depth 1`, then `python3 docs/tools/parity_lists.py` (it keeps the ticks), and review what the diff of `docs/PARITY.md` added.
- **Where things are in Books:**
  - schemas: `schemas/app/**/*.json` (fields, child tables, flags such as `isSubmittable`);
  - document logic: `models/baseModels/<Name>/<Name>.ts`;
  - ledger posting: `models/Transactional/LedgerPosting.ts`;
  - reports: `reports/`;
  - screens: `src/pages/`, `src/components/`;
  - sidebar: `src/utils/sidebarConfig.ts`;
  - default charts of accounts: `fixtures/verified/`;
  - print templates: `templates/`.
