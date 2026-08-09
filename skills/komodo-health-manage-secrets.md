---
name: Manage secrets with the Komodo Secrets Service
description: >-
  Store, read, list, update, move, and delete secrets in the Komodo Secrets
  Service using the SecretsApi, with correct user / application / shared
  scoping and cursor pagination.
api: Komodo Secrets Service (komodo.secrets_api)
docs: https://docs.komodohealth.com/reference/sdk/
grounded_in: docs.komodohealth.com SDK reference (SecretsApi)
operations:
  - create_secret
  - read_secret
  - list_secrets
  - update_secret
  - delete_secret
  - move_secret
---

# Manage secrets with the Komodo Secrets Service

`SecretsApi` gives programmatic access to the Komodo Secrets Service. Every
synchronous method has an `_async` counterpart (`create_secret_async`, …) for
async runtimes.

```python
from komodo.secrets_api import SecretsApi, SecretType

api = SecretsApi(environment="production")
```

The constructor also accepts `api_client`, `session`, `access_token` (useful in
MCP/service contexts), and `account_id` to override the resolved account.

## Choose the scope first

`SecretType` determines the namespace, and it is the decision that matters most:

| Scope                    | Meaning                                              |
|--------------------------|------------------------------------------------------|
| `SecretType.USER`        | Personal secrets tied to the authenticated user      |
| `SecretType.APPLICATION` | Application-scoped — **requires `app_id`**            |
| `SecretType.SHARED`      | Organization-wide, readable by all account members    |

Default to `USER`. Use `SHARED` only when every member of the account is meant
to read the value.

## Operations

```python
# Create
api.create_secret(
    secret_type=SecretType.USER,
    secret_path="my-api-key",
    data={"key": "abc123"},
)

# Read — returns .data, .metadata, .success, .message
secret = api.read_secret(SecretType.USER, "my-api-key")
print(secret.data)

# Update — replaces data and creates a new version
api.update_secret(SecretType.USER, "my-api-key", data={"key": "new-value"})

# Delete — permanent
api.delete_secret(SecretType.USER, "my-api-key")
```

Application-scoped secrets must carry `app_id`:

```python
api.create_secret(
    secret_type=SecretType.APPLICATION,
    secret_path="db-password",
    data={"password": "..."},
    app_id="<app uuid>",
)
```

## Listing and pagination

`list_secrets` is the one paginated surface in the kit. It uses an opaque
cursor token:

```python
next_token = None
paths = []
while True:
    result = api.list_secrets(SecretType.USER, prefix="svc/", next_token=next_token)
    paths.extend(result.secrets)
    if not result.has_more:
        break
    next_token = result.next_token
```

Always loop on `has_more` — a single call is not the full set. `prefix` filters
to paths starting with that string.

## Moving between scopes

```python
api.move_secret(
    source_type=SecretType.USER,
    source_path="my-api-key",
    dest_type=SecretType.SHARED,
    dest_path="team/my-api-key",
    delete_original=False,
)
```

`move_secret` copies by default; pass `delete_original=True` to actually move.

## Rules

- `delete_secret` is permanent — read and back up the value first if it is not
  reproducible.
- `update_secret` **replaces** `data` rather than merging it. Read, merge in
  your own code, then write.
- Register the app (`register_app`) before performing app-scoped secret
  operations — RBAC/FGA needs the app resource to exist first, or behavior is
  unreliable.
- There is no idempotency-key contract. `create_secret` on an existing path is
  not a safe blind retry — read first, or use `update_secret`.
- Never log `secret.data`, and never place secret values in `query_tag_extra`.
- `platform=True` targets the platform secrets endpoint and is advanced usage;
  leave it `False` unless the docs direct otherwise.
