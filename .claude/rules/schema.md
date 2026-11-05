---
paths:
  - "server/app/models/**"
  - "server/migrations/**"
  - "server/migrate.py"
---

# Schema changes

- Only through Alembic: edit `server/app/models/`, then `alembic upgrade head`, `alembic revision --autogenerate -m "..."`, read and fix the generated file, `alembic upgrade head && alembic check`. Commit the migration with the models. Never `create_all`, never edit tables by hand.
- Autogenerate can't see renames: it writes a drop and an add, which loses data. Write renames by hand (`op.rename_table`, `batch_op.alter_column(..., new_column_name=...)` with `existing_type` and `existing_nullable`), and drop foreign keys and indexes by their names from the naming convention in `app/models/base.py` before dropping their column.
- Migrations must run on MySQL/MariaDB (InnoDB) as well as SQLite. `env.py` uses batch mode on SQLite only. Preview MySQL SQL with `ALEMBIC_DATABASE_URL="mysql+pymysql://u:p@localhost/x" alembic upgrade head --sql`.
- `tests/test_migrations.py` fails when models and migrations differ. Run the whole suite after a schema change.
- On a server, the same `alembic upgrade head` runs from a terminal, or from cPanel's *Execute python script* through `migrate.py`. Release notes say when a release needs it.
