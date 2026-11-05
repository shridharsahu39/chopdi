---
paths:
  - "frontend/**"
---

# Frontend

- React, Vite, Tailwind and React Query, built into `server/app/static/ui`, which git ignores.
- **Books' presentation and components, Chopdi's own theme** (D-10). Before building a screen, open its Books source (`reference/books/src/pages/`, `src/components/`) for:
  - the layout and what the screen shows;
  - the sidebar groups and order (`src/utils/sidebarConfig.ts`);
  - list, form and child-table behaviour;
  - quick edit;
  - keyboard shortcuts;
  - empty states.

  Build it with Chopdi's components and theme tokens. Don't copy Books' code, CSS or icons (D-1), and no Frappe or ERPNext name or logo appears.
- **Generic over bespoke.** Lists, forms, child tables, quick edit, filters and reports render from the document metadata the server gives, as Books renders from its schemas. A new document type should need no new page unless Books has a special screen for it (POS, Chart of Accounts, Import Wizard, Print View, Template Builder, Setup Wizard).
- Colours, fonts and radius come only from theme tokens; never hard-code them. Books has light and dark themes.
- Keep it light: lazy-load pages and keep charts out of the main chunk.
- Mobile works too: lists become cards on phones. Check a changed screen at 390 px.
- `npx tsc --noEmit` passes before handover.
