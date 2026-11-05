# Security check for every change

Every change gets a security check before handover, alongside its QA. It covers code, migrations, configuration, the release ZIP and the frontend. A documentation-only change is checked only for secrets and personal data in its text.

## While building

1. **Every API route checks who is calling:** a session user or an API user (`token key:secret`), then a document permission for the action (read, write, create, submit, cancel, amend, delete, report, export, import, share, print, email), then user permissions on the linked records it touches (Company, for example). A route open without a caller (sign-in, password reset, health check) is open on purpose, with the reason in a comment.
2. **Identity and scope come from the session or the API key, never from the body.** The company, owner or party a request acts for is checked against the caller's user permissions.
3. **Keep the pipeline:**
   - server-side sessions with hashed cookie tokens;
   - a CSRF token on every state-changing browser request (API-key requests carry no cookie and skip it);
   - PBKDF2 passwords, lockout and optional two-factor;
   - API secrets stored only as hashes.
4. **Validate input at the route or service:** types, lengths, choices, dates and decimal precision. Query through SQLAlchemy with bound values. Never put request fields into SQL text, file paths, shell commands or template code. Print templates render with autoescape and no access to the server.
5. **Financial documents:**
   - submitted documents are never edited or deleted;
   - cancel writes reversing ledger rows;
   - amend creates a new linked document;
   - ledger rows balance;
   - every write keeps the actor, reason where one applies, request key, row locks (`with_for_update`) and audit history.
6. **Files and imports:**
   - uploads are sniffed by content and stored outside the web root;
   - they are served only through permission-checked routes;
   - the import wizard checks each row with the same validation and permissions as the form.
7. **Secrets:** only the database login and `SECRET_KEY` (plus `ENVIRONMENT`, `UPLOAD_DIR`) come from the environment or `config.env`. Every other secret is a sealed setting, never returned to the browser or written to the audit log. Never commit `config.env`, a database, an upload or a key, and never print a secret.
8. **Logs** (activity, access, API request, audit) never carry passwords, API secrets, tokens or sealed settings.
9. **Errors** return a clear message without stack traces or internals.
10. **Dependencies** are pure Python only (so it runs on shared cPanel hosting), pinned, and 3.8-compatible. Frontend packages are pinned too.

## Before handover

1. Run the `security-reviewer` agent on the change's diff.
2. Fix each finding, or record why not. A high finding blocks handover until it's fixed, or the maintainer accepts it in the pull request. High means any of these:
   - an authorisation bypass;
   - one company's or user's data reaching another;
   - a posted document changed;
   - injection;
   - a leaked secret.
3. Add a security case to the QA record for each new route or rule, for example: a missing permission is refused, another company's record is refused, an API key without the role is refused, bad input is refused.
4. Record the check in the change's `qa.md`, under "Security check".
5. Before any commit, read the staged diff for secrets and personal data.
