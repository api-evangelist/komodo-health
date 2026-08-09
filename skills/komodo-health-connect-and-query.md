---
name: Connect to Komodo and run a query
description: >-
  Authenticate to the Komodo Health platform with the Marmot Development Kit and
  run SQL against the account's Komodo-managed Snowflake warehouse, including
  async submit-and-poll and failure diagnosis.
api: Marmot Development Kit (komodo Python SDK + CLI)
docs: https://docs.komodohealth.com/guides-tutorials/guides/3-executing-queries/
grounded_in: docs.komodohealth.com SDK and CLI reference
operations:
  - komodo login
  - komodo account set
  - komodo sql-execute
  - komodo sql-diagnostics
  - get_snowflake_connection
  - execute_query_async
  - get_query_diagnostics
---

# Connect to Komodo and run a query

Komodo Health does not expose a public REST data API. All data access goes
through the Marmot Development Kit (MDK), which returns a **DB-API 2.0**
connection to the Snowflake warehouse provisioned for your Komodo account.

## 1. Install

```
uv init --python "python >=3.11, <3.14"
uv add komodo
```

Python must be `>=3.11, <3.14`. Verify with `uv run komodo --version`.

## 2. Authenticate

Interactive (OAuth 2.0 Device Authorization Flow — opens a browser):

```
uv run komodo login
uv run komodo account set
```

`login` writes the JWT and refresh token to the `[default]` profile in
`~/.komodo/credentials`. If the login maps to a single account, it is selected
automatically; otherwise `account set` picks one. Confirm with
`uv run komodo account get`.

For automation, use a service principal instead — see the
`komodohealth-machine-to-machine-access` skill.

## 3. Open a connection

```python
from komodo import get_snowflake_connection

conn = get_snowflake_connection()
cur = conn.cursor()
```

With no arguments the SDK resolves credentials in this order: explicit
arguments → `[default]` profile → browser web login (in-memory only, not
persisted). Never mix authentication methods in one call — passing `jwt`
together with `client_id`/`client_secret`, or `profile` together with
`account_id`, raises `ValueError`.

## 4. Query

Set the database first — `INFORMATION_SCHEMA` will not resolve without it.

```python
cur.execute("USE DATABASE DATA")
cur.execute("SELECT column_name, table_name FROM INFORMATION_SCHEMA.COLUMNS LIMIT 20")
rows = cur.fetchall()
```

Use `fetchone()`, `fetchmany(size=n)`, or `fetchall()`. For dataframes use
`cur.fetch_pandas_all()` or `cur.fetch_pandas_batches()`. Column metadata is on
`cur.description`.

Start with `INFORMATION_SCHEMA` queries to discover what the account is
subscribed to before querying data tables. You may also need `USE SCHEMA` and
`USE ROLE` depending on what the account admin assigned.

For ad-hoc work, skip Python entirely:

```
uv run komodo sql-execute "SELECT column_name FROM INFORMATION_SCHEMA.COLUMNS LIMIT 10"
uv run komodo sql-shell
```

## 5. Long-running queries

Do not block on large queries. Submit and poll:

```python
import asyncio
from komodo import get_snowflake_connection, execute_query_async

async def main():
    conn = get_snowflake_connection()
    cur = conn.cursor()
    cur = await execute_query_async(cur, "SELECT CURRENT_TIMESTAMP()")
    print(cur.fetchone())
    conn.close()

asyncio.run(main())
```

`execute_query_async` returns the same cursor with results attached.

## 6. Diagnose failures

Errors surface the full Snowflake detail:

```
Komodo Error: Failure during expansion of view 'MEDICAL_SERVICE_LINES_LATEST':
Error in secure object. | Error code: 002003 | SQL state: 02000 | Query ID: 01b... (Trace ID: ...)
```

Capture the query ID (from the message, or `cur.sfqid`) and pull the recorded
diagnostics rather than escalating to support:

```python
from komodo import get_query_diagnostics
print(get_query_diagnostics(conn, cur.sfqid))
```

or `uv run komodo sql-diagnostics <query-id>`. This returns `EXECUTION_STATUS`,
`ERROR_CODE`, `ERROR_MESSAGE`, `WAREHOUSE_NAME` and timing from Snowflake's
`INFORMATION_SCHEMA.QUERY_HISTORY`. It requires a current database on the
connection and the query to be inside the history retention window; it returns
`None` if no matching query is found.

## Rules

- Always `conn.close()` when finished.
- `UnsetAccountError` means no account context — run `komodo account set` or
  pass `account_id=`.
- There is **no idempotency-key contract**. Writes are plain SQL or CRUD calls;
  make retries safe yourself.
- No rate limits are published — the practical ceiling is the account's
  warehouse size and data subscription.
- Attribution metadata goes in `query_tag_extra`, which lands in Snowflake query
  history. It must be JSON-serializable and must **never** contain secrets or
  PHI.
