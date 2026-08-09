---
name: Set up machine-to-machine access to Komodo
description: >-
  Create a Komodo service principal and wire non-interactive, automation-safe
  access to the platform using client-credentials OAuth and named credential
  profiles.
api: Marmot Development Kit (komodo Python SDK + CLI)
docs: https://docs.komodohealth.com/guides-tutorials/guides/2-authentication/
grounded_in: docs.komodohealth.com authentication guide and CLI reference
operations:
  - komodo service-principal create
  - komodo service-principal list
  - komodo service-principal delete
  - komodo account get
  - get_snowflake_connection
---

# Set up machine-to-machine access to Komodo

Use this for CI/CD, scheduled jobs, and services — anything that cannot open a
browser. Interactive users should use `komodo login` instead (see the
`komodohealth-connect-and-query` skill).

## 1. Create a service principal

You need an interactive session first to mint the credentials:

```
uv run komodo login
uv run komodo account set
uv run komodo service-principal create --name "my-service" --description "Nightly refresh job"
```

This returns a `client_id` and a `client_secret`. **The secret is shown once** —
capture it into your secret manager immediately.

Audit and clean up with:

```
uv run komodo service-principal list
uv run komodo service-principal delete <SERVICE_PRINCIPAL_ID>
```

## 2. Record the account

Every call is account-scoped. Get the account UUID:

```
uv run komodo account get
```

Accounts are isolated — each has its own data subscriptions and its own
Komodo-managed Snowflake warehouse, and data never moves between them.

## 3. Connect

Preferred — a named profile in `~/.komodo/credentials`:

```ini
[production]
client_id = <client id>
client_secret = <client secret>
account_id = <account uuid>
account_slug = prod-org
```

```python
from komodo import get_snowflake_connection
conn = get_snowflake_connection(profile="production")
```

Or pass credentials explicitly, e.g. when they arrive from a secret manager as
environment variables:

```python
conn = get_snowflake_connection(
    client_id=os.environ["KOMODO_CLIENT_ID"],
    client_secret=os.environ["KOMODO_CLIENT_SECRET"],
    account_id=os.environ["KOMODO_ACCOUNT_ID"],
)
```

## 4. Non-writable home directories

In containers and CI runners `~` is often not writable. Point the kit at a
mounted path:

```
export KOMODO_CREDENTIALS_PATH="/opt/komodo/credentials"
```

The CLI and SDK must agree on this value or they will resolve different
credentials — a common cause of "works locally, fails in CI".

## Rules

- Use exactly one authentication method per call. Mixing `profile` with
  `client_id`, or `profile` with `account_id`, raises `ValueError` — a named
  profile already carries its account.
- Named profiles support M2M credentials only, never JWTs.
- JWTs and explicit M2M credentials both require `account_id`.
- If no valid credentials are found the SDK will try to open a **browser login**.
  In an automated context that hangs or fails — always pass credentials
  explicitly so the fallback never triggers.
- Never print or log the `client_secret`, and never place it in
  `query_tag_extra` (that value is written to Snowflake query history).
- Rotate by creating a new service principal, cutting over, then deleting the
  old one.
