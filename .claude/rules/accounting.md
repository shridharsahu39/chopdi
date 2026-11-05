---
paths:
  - "server/app/**"
---

# Accounting rules

- **Double entry:** each submitted document posts ledger rows whose debits equal credits in the company currency. Rounding goes to the company's round-off account, as in Books (`reference/books/models/Transactional/LedgerPosting.ts`) and ERPNext (`erpnext/accounts/general_ledger.py`). A posting that doesn't balance fails; it is never saved half-done.
- **Lifecycle:** Draft (editable, posts nothing), then Submitted (posted, locked), then Cancelled (reversing rows). Amend creates a new draft linked to the cancelled document, named with a suffix. Nothing submitted is edited or deleted, and only drafts are deleted.
- **Money** is a fixed-point decimal at the currency's precision (Decimal and decimal strings), never a float. Each row stores the transaction currency, the exchange rate and the company-currency amount.
- **Dates:** the posting date decides the fiscal year and period. Posting into a closed period, or a period locked by period closing or Accounts Settings, is refused with a clear message.
- **Every number in a report comes from the ledger:** opening balance, period movement and closing balance, so the Trial Balance, Profit and Loss and Balance Sheet agree with the General Ledger. A test checks this for each new posting type.
- **Porting a rule:** say in the record which Books or ERPNext file and function it corresponds to, and at which commit (`docs/PARITY.md` header).
