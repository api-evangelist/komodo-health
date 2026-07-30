---
name: komodo-health-query-healthcare-map
description: Authenticate to Komodo Health and run SQL against the account's Komodo-managed Snowflake warehouse, including schema exploration and failure diagnosis.
generated: '2026-07-19'
method: generated
source: >-
  Grounded in real command and function names published at
  https://docs.komodohealth.com/guides-tutorials/guides/1-quickstart,
  /3-executing-queries, /2-authentication and /reference/cli.
api: Komodo Health — Marmot Development Kit
surface: cli+sdk
operations:
  - komodo login
  - komodo account set
  - komodo account get
  - komodo sql-execute
  - komodo sql-shell
  - komodo sql-diagnostics
  - get_snowflake_connection
  - execute_query_async
  - is_still_running
  - get_query_diagnostics
---

# Query the Komodo Healthcare Map

Komodo does not expose a public REST API for data access. Every query runs
against the account's **dedicated Komodo-managed Snowflake warehouse** through
the `komodo` package (the Marmot Development Kit).

## Prerequisites

- Python `>=3.11, <3.14`
- `uv add komodo` (or `pip install komodo`)

## 1. Authenticate

```
uv run komodo login
```

Completes the OAuth 2.0 Device Authorization Flow in the browser and writes the
JWT plus refresh token to the `[default]` profile in `~/.komodo/credentials`.
You will not need to log in again until the token expires.

For automation, use a service principal instead — see
`komodo-health-provision-service-principal.md`.

## 2. Select the account

```
uv run komodo account set
```

Every query is scoped to one account, and accounts are isolated from each other.
If the login maps to a single account it is selected automatically. Confirm with
`uv run komodo account get`. Skipping this step raises `UnsetAccountError`.

## 3. Run a query

From the CLI, for a single statement:

```
uv run komodo sql-execute "SELECT column_name FROM INFORMATION_SCHEMA.COLUMNS LIMIT 10"
```

Or interactively — errors in one statement do not exit the shell:

```
uv run komodo sql-shell
```

From the SDK:

```python
from komodo import get_snowflake_connection

conn = get_snowflake_connection()
cursor = conn.cursor()
cursor.execute("USE DATABASE DATA")
cursor.execute("SELECT column_name, table_name FROM INFORMATION_SCHEMA.COLUMNS LIMIT 20")
for row in cursor.fetchall():
    print(row)
cursor.close()
conn.close()
```

## Conventions to respect

- **Set context first.** Issue `USE DATABASE`, `USE SCHEMA` and `USE ROLE` as
  the account requires before querying. Start from `INFORMATION_SCHEMA` when
  exploring.
- **Bound the result set** with SQL `LIMIT`. There is no HTTP pagination
  envelope — rows come back through DB-API cursor semantics
  (`fetchall` / `fetchone` / `fetchmany`).
- **Do not block on long queries.** Use `execute_query_async` and poll with
  `is_still_running`.

## When a query fails

Komodo surfaces the full Snowflake error rather than masking it:

```
Komodo Error: <message> | Error code: <code> | SQL state: <state> | Query ID: <sfqid> (Trace ID: ...)
```

1. Capture the query ID from the message, or from `cursor.sfqid`.
2. Pull recorded diagnostics — error code, execution status, warehouse, timing:

```python
from komodo import get_query_diagnostics
print(get_query_diagnostics(conn, query_id))
```

or `uv run komodo sql-diagnostics <query-id>`.

Diagnostics read `INFORMATION_SCHEMA.QUERY_HISTORY`, so a database must be set
on the connection and the query must fall inside the retention window. If the
cause is still unclear (for example `Error in secure object` from a view you do
not own), report the issue with **both** the query ID and the trace ID.
