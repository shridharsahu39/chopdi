# Chopdi: scope, decisions and build order

**As of:** 05/11/2026  
**Goal:** Frappe Books' functionality as a web app, with nothing missed. It adds ERPNext's Accounts module, Akaunting's and Tally's accounting features, and Frappe-style users and API access, built on FastAPI and React.

## What Chopdi is

The accounting books for a business, as a web app, in five parts, plus an API for other applications (`docs/API_INTEGRATION.md`):

1. **Frappe Books:**
   - the documents: quotes, sales and purchase invoices, payments, journal entries;
   - the masters: parties, items, price lists, tax templates, the chart of accounts;
   - inventory: stock movements, shipments, purchase receipts, batches, serial numbers, locations;
   - sales tools: POS, loyalty programmes, pricing rules, coupon codes, leads;
   - reports: General Ledger, Profit and Loss, Balance Sheet, Trial Balance, GSTR-1, GSTR-2, Stock Ledger, Stock Balance;
   - print templates, import wizard, customise form, setup wizard, dashboard and Get Started.
2. **ERPNext Accounts on top of that:**
   - companies, fiscal years, cost centres and accounting dimensions;
   - payment entries with reconciliation, payment terms and schedules;
   - bank accounts, bank transactions and bank reconciliation;
   - budgets, period closing, deferred revenue and expense;
   - exchange-rate revaluation, tax rules, tax withholding (TDS);
   - dunning, subscriptions, invoice discounting, share management;
   - ERPNext's full report set: receivables and payables ageing, cash flow, gross profit, registers and so on.
3. **Frappe's users and permissions:**
   - users, roles and role profiles;
   - document and field-level permissions, user permissions;
   - API keys and secrets, OAuth clients;
   - sign-in security: two-factor, lockout, sessions;
   - activity, access and API logs; version history; comments; sharing.
4. **Akaunting's** client portal, recurring documents, categories and dashboards (D-11).
5. **Tally's accounting features** beyond these (D-5): `docs/PARITY_TALLY.md`.

GST and payroll are out for now (D-5, D-9).

The exact list, item by item, is `docs/PARITY.md` (generated from the reference sources) plus `docs/PARITY_TALLY.md` (by hand).

## Decisions

Numbered and dated. **Open** ones must be settled before the work they block.

- **D-1 Licences: rebuild the functionality, not the code or the UI.** 05/11/2026.
  - Licences: Frappe Books is AGPL-3.0, ERPNext GPL-3.0, India Compliance GPL-3.0, Frappe MIT.
  - Chopdi does everything those apps do, and its code, templates, CSS and icons are its own. The reference is read for behaviour and never copied. Re-typing a function line by line counts as copying.
  - The UI follows Books' presentation and components, without its exact theme (D-10).
  - The names "Frappe" and "ERPNext" and their logos don't appear in the product.
- **D-2 Hosting: anything that runs on cPanel.** 05/11/2026. The target is cPanel's Python app (Passenger) with MySQL/MariaDB, no Docker, Python 3.8-compatible and pure-Python dependencies. That also runs on any plain server.
- **D-3 Money.** Fixed-point decimals at each currency's precision, for multi-currency. Exchange rates keep 9 decimal places. 05/11/2026.
- **D-4 Companies: several companies in one installation**, scoped by user permissions, as in ERPNext. 05/11/2026. A group company that consolidates them is in `docs/PARITY_TALLY.md`.
- **D-5 Scope: a book of accounts. GST is out for now.** 05/11/2026.
  - Scope is Books, plus ERPNext Accounts, plus Akaunting's functionality (D-11), plus Tally's accounting features (`docs/PARITY_TALLY.md`).
  - India Compliance stays in `reference/`, and its items are listed as `[-]` in `docs/PARITY.md`, so nothing is missed when GST comes back. The same goes for Books' GSTR reports and India-only fields.
  - **Everything except GST stays in:** inventory and POS, sales tools (leads, loyalty, pricing rules, coupons), TDS/TCS, and Tally's order processing and manufacturing.
- **D-6 Desktop-only features.** Books runs on Electron with one local database file. The database selector, file backups and auto-update become, for the web: company selection, server-side backups with a downloadable export, and server releases. 05/11/2026.
- **D-7 No ERPNext sync.** 05/11/2026. Books' `ERPNextSyncSettings`, `ERPNextSyncQueue` and `FetchFromERPNextQueue` are `[-]` in `docs/PARITY.md`.
- **D-9 No payroll for now.** 05/11/2026. Tally's payroll items are `[-]` in `docs/PARITY_TALLY.md`.
- **D-10 The look: Books' presentation and components, Chopdi's own theme.** 05/11/2026.
  - **Follow Books:** the layout and how things are presented, and the same kinds of components behaving the same way:
    - the sidebar groups;
    - list views with filters;
    - forms with child tables;
    - the quick-edit side panel;
    - the command palette and keyboard shortcuts;
    - reports with filters, totals and export;
    - print view and template builder;
    - the setup wizard and Get Started.
  - **Chopdi's own:** colours, fonts and visual style, through theme tokens.

- **D-11 Akaunting: functionality, read only.** 05/11/2026.
  - Its licence is the **Business Source License 1.1** (`reference/akaunting/LICENSE.txt`), which isn't an open-source licence.
  - My reading: it limits production use and rebranding of Akaunting itself, and each version turns into GPLv3 four years after it's published.
  - So, as with D-1, Akaunting is read for functionality, and nothing is copied. Its paid app store is out.
  - What it adds over Books and ERPNext, listed in `docs/PARITY.md`:
    - a client portal where customers see and pay invoices;
    - recurring documents;
    - income and expense categories;
    - email templates per event;
    - widget dashboards.

- **D-12 Licence: MIT.** 05/11/2026. This is possible because nothing is copied from the AGPL, GPL or BSL projects (D-1, D-11). Keep it that way: a contribution that ports their code would conflict with MIT.

## Build order

Each step is one pull request, merged before the next one starts.

1. **Foundation.**
   - `server/` (FastAPI, SQLAlchemy, Alembic, the settings registry, audit log, errors) and `frontend/` (React, Vite, Tailwind, React Query).
   - Users, roles, sessions with CSRF, PBKDF2 passwords, lockout, API key and secret.
   - A metadata-driven document engine, the equivalent of Frappe's DocType and Books' schemas: fields, child tables, naming series, links, submit, cancel and amend, permissions, version history.
   - The Books shell: sidebar, list view, form view, quick edit, command palette, theme.
   - Release ZIP; a local test environment with demo data.
2. **Chart of accounts and the ledger.** Company, Fiscal Year, Account tree with the standard charts, Cost Centre, Currency, General Ledger entries and the GL report, Journal Entry, Accounting Settings.
3. **Parties and items.** Party, Address, Item, Item Group, UOM, Price List, Tax templates, Number Series.
4. **Sales and purchases.** Quote, Sales Invoice, Purchase Invoice, Payment, taxes, print templates, and Profit and Loss, Balance Sheet and Trial Balance.
5. **Setup wizard, Get Started, dashboard, import wizard, customise form.**
6. **The integration API** (`docs/API_INTEGRATION.md`): API users posting invoices, payments, refunds and reversals idempotently.
7. **Inventory and POS.**
8. **Sales tools:** loyalty, pricing rules, coupons, leads.
9. **ERPNext Accounts in groups:**
   - payment entries and reconciliation;
   - banking;
   - receivables and payables reports;
   - budgets and period closing;
   - tax withholding;
   - multi-currency revaluation;
   - deferred accounting;
   - subscriptions, dunning and the rest, in priority order.
10. **Akaunting's extras:** client portal, recurring documents, categories, email templates, dashboards.
11. **Tally's extras** (`docs/PARITY_TALLY.md`), in priority order: order processing, banking and cheques, inventory depth, TDS/TCS, the edit log (early, if the rule applies), interest, budgets and scenarios, group consolidation, migration from Tally. Payroll (D-9) and GST (D-5) are out for now.

Steps 1 to 6 give a usable book of accounts on the web that other applications can post into. Everything else adds to it.
