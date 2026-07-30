---
name: komodo-health-connect-mcp-server
description: Configure and run the first-party Komodo MCP server so an AI assistant can explore the account's Snowflake schemas and drive the Komodo App Builder.
generated: '2026-07-19'
method: generated
source: >-
  Grounded in the published MCP server guide
  https://docs.komodohealth.com/guides-tutorials/guides/6-mcp-server and tool
  reference https://docs.komodohealth.com/reference/app-builder-mcp
api: Komodo Health — Komodo MCP Server
surface: mcp
operations:
  - komodo mcp show-config
  - komodo mcp run
  - list_snowflake
  - get_available_templates
  - scaffold_app
  - validate_devfile
  - register_app
  - build_app
  - get_app_status
  - get_app_logs
---

# Connect the Komodo MCP server

The Komodo MCP server ships inside the `komodo` PyPI package. It is a **local
stdio server** the AI client launches — there is no hosted remote endpoint. All
tools operate against the `production` environment.

## Prerequisites

1. Install the Marmot Development Kit: `uv add komodo`
2. `uv run komodo login`
3. `uv run komodo account set`

## Generate the client configuration

```
uv run komodo mcp show-config
```

The CLI prints the correct JSON for your environment and copies it to the
clipboard. Pass `--name` / `-n` to use a server name other than `komodo`.

The inner server definition is identical across clients — only the outer wrapper
key changes:

```json
"komodo": {
  "command": "uv",
  "args": ["run", "komodo", "mcp", "run"],
  "cwd": "/path/to/your/komodo/project"
}
```

| Client | File | Wrapper key |
|---|---|---|
| Cursor | `~/.cursor/mcp.json` | `mcpServers` |
| VS Code | user or workspace `settings.json` | `mcp.servers` |
| Claude Desktop | Claude Desktop config | `mcpServers` |

`cwd` **must** be the directory containing the `pyproject.toml` where `komodo`
is installed — usually your app or workspace root. Getting this wrong is the
single most common startup failure.

## Verify

Ask the assistant: *"What are my Komodo databases?"*

## Tool surface

**Schema exploration** — one tool, `list_snowflake`, dispatching on `list_type`:

| `list_type` | Returns | Required parameters |
|---|---|---|
| `database` | accessible databases | — |
| `schema` | schemas in a database | `database` |
| `table` | tables in a schema (metadata / row counts) | `database`, `schema` |
| `column` | column definitions | `database`, `schema`, `table` |

**App Builder** — 26 further tools (15 app development, 6 secrets, 5 sharing).
See `komodo-health-build-and-deploy-app.md`.

## Notes

- Schema exploration failing while the CLI works almost always means the MCP
  server resolved different credentials or a different account than your shell.
- If you set `KOMODO_CREDENTIALS_PATH`, the MCP server process must see it too.
- In internal Komodo environments the `komodo-internal-tools` plugin registers
  additional MCP tools; that list is not public.
