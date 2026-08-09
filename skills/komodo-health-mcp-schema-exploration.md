---
name: Explore Komodo data from an AI client over MCP
description: >-
  Wire the first-party Komodo MCP server into an AI client and use its
  list_snowflake tool to discover databases, schemas, tables, and columns before
  writing SQL.
api: Komodo MCP server (komodo mcp run)
docs: https://docs.komodohealth.com/guides-tutorials/guides/6-mcp-server/
grounded_in: docs.komodohealth.com MCP server guide and CLI reference
operations:
  - komodo mcp show-config
  - komodo mcp run
  - list_snowflake
---

# Explore Komodo data from an AI client over MCP

Komodo ships a first-party MCP server inside the `komodo` PyPI package. It is
**local/stdio** — there is no hosted or remote endpoint to point a client at.

## 1. Prerequisites

The server runs from a project directory that depends on `komodo`:

```
uv init --python "python >=3.11, <3.14"
uv add komodo
uv run komodo login
uv run komodo account set
```

The MCP server inherits this session from the `[default]` profile in
`~/.komodo/credentials`. Authenticate **before** starting the client.

## 2. Generate the client config

```
uv run komodo mcp show-config
```

The JSON is printed and copied to the clipboard. Paste it into
`~/.cursor/mcp.json`, VS Code Copilot `settings.json`, or the Claude Desktop
config. Use `--name` / `-n` to override the default server name `komodo`.

The command the client launches is `uv` with args `run komodo mcp run`. The
`cwd` in that config **must** be the project directory containing the
`pyproject.toml` that depends on `komodo` — a wrong `cwd` is the most common
reason the server fails to start.

## 3. Explore the warehouse

The generally available tool is `list_snowflake`, which takes an operation:

| Operation  | Returns                                          | Requires                     |
|------------|--------------------------------------------------|------------------------------|
| `database` | Accessible databases                             | —                            |
| `schema`   | Schemas in a database                            | `database`                   |
| `table`    | Tables in a schema, with metadata and row counts  | `database`, `schema`         |
| `column`   | Column definitions                                | `database`, `schema`, `table` |

Walk it top-down — `database` → `schema` → `table` → `column` — to ground SQL in
what the account is actually subscribed to, rather than guessing table names.
Then hand the query to `komodo sql-execute` or `get_snowflake_connection()`.

## 4. App Builder tools

When the `komodo-internal-tools` plugin is installed, the server additionally
registers 26 App Builder tools (15 app development, 6 secrets, 5 sharing) for
scaffolding, building, deploying, and sharing Komodo Apps. These are not
available to standard external accounts. Check what is registered with:

```
uv run komodo plugin list
```

## Rules

- If schema exploration fails while the CLI works, the MCP process is on a
  different account — re-run `uv run komodo account set` and confirm.
- Row counts from `table` are metadata estimates; do not treat them as exact.
- Tools operate against the `production` environment.
- Nothing returned by these tools should be echoed into a `query_tag_extra`
  value or any other field written to Snowflake query history if it could carry
  PHI.
