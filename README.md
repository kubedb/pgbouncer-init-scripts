# git-sync-pgbouncer.sh — verification

This script reads the Postgres password from
`/var/run/pgbouncer/secret/password`, connects via the local UNIX socket (no
host/port), and creates `TABLE_NAME` (default `my_table`).

Quick verification steps:

1. Inspect password file
   - cat /var/run/pgbouncer/secret/password

2. Run the script (from the script directory)
   - chmod +x git-sync-pgbouncer.sh
   - ./git-sync-pgbouncer.sh
   - The script logs success/failure messages.

3. Verify table exists (use the same password)
   - export PGPASSWORD="$(cat /var/run/pgbouncer/secret/password)"
   - psql -U postgres -d postgres -c "\dt"
   - OR check specific table:
     psql -U postgres -d postgres -c "SELECT to_regclass('public.my_table');"

Expected output examples:
- "\dt" should list `public.my_table` if created.
- "SELECT to_regclass(...)" should return the table name if present, otherwise NULL.

Troubleshooting:
- Ensure the `psql` client is installed and available in PATH before running the script.
- Ensure the password file exists and contains the correct password.
- If using a different DB name or table name, set `PG_DB` and `TABLE_NAME` env vars before running:
  - PG_DB=mydb TABLE_NAME=events ./git-sync-pgbouncer.sh
