---
name: komodo-health-build-and-deploy-app
description: Scaffold, validate, register, build, deploy and share a Komodo App using the App Builder MCP tools and the komodo CLI.
generated: '2026-07-19'
method: generated
source: >-
  Grounded in the published App Builder MCP tool reference
  https://docs.komodohealth.com/reference/app-builder-mcp and CLI reference
  https://docs.komodohealth.com/reference/app-builder-cli
api: Komodo Health — App Builder
surface: mcp+cli
operations:
  - get_available_templates
  - scaffold_app
  - validate_devfile
  - generate_devfile
  - register_app
  - build_app
  - get_build_scan_report
  - cancel_build
  - list_apps
  - get_app
  - get_app_status
  - get_app_logs
  - enable_app
  - disable_app
  - delete_app
  - create_secret
  - list_secrets
  - share_secret
  - list_grantable_roles
  - grant_app_role
  - revoke_app_role
---

# Build and deploy a Komodo App

The Komodo MCP server registers 26 App Builder tools and appends workflow
instructions to its own prompt, so a connected assistant can take an app from
template to running deployment. The same surface is available from the CLI under
`komodo app`, `komodo build`, `komodo infra` and `komodo secrets`.

Prerequisite: the MCP server is configured and authenticated — see
`komodo-health-connect-mcp-server.md`. All tools operate against `production`.

## The workflow, in order

1. **`get_available_templates`** — always call first. Returns the templates
   (FastAPI, Streamlit, React, multi-container, react-advanced, restate-agent)
   so you can recommend the right one rather than guessing.
2. **`scaffold_app`** — generates the project from the chosen template: source
   code, `Dockerfile`, `devfile.yaml` and `README.md`.
3. **`validate_devfile`** — always validate before building. Catches schema
   errors, missing fields, port mismatches and invalid app names early. Use
   `generate_devfile` if you need to produce one from scratch.
4. **`register_app`** — pre-register the app (name, display name, `project_path`
   so a `.app` file is written). **Required before `build_app` when the app uses
   Komodo secrets** — RBAC/FGA needs the app Custom Resource to exist before
   secret operations behave reliably. Recommended even without secrets;
   `build_app` can create the app inline if you skip it (legacy flow).
5. **`build_app`** — by default packages the project and builds through the
   **remote Build API** (platform worker, ECR, image signing); only Komodo OAuth
   is required. Set `build_mode: local` to run BuildKit on the client
   (`buildctl` → docker image tar) and upload through the Build API `prebuilt`
   path — Trivy, ECR and signing still run on the platform. Neither path needs
   `AWS_ROLE_ARN` or a client-side registry push.
6. **After the build**, print the `app_id` and **every** entry in
   `service_urls` (one URL per service). Then *ask* whether the user wants
   deployment polled.
7. **`get_app_status`** — only if asked. Poll every 10–15 seconds until the app
   reaches `RUNNING` or `FAILED`. On failure, call **`get_app_logs`** to
   diagnose.

## Verifying a build

- Confirm `success: true` and a valid `app_id` in the response.
- Retry up to 2 times on transient errors (timeouts, network issues).
- On remote Build API or local BuildKit / prebuilt-upload failure, tell the user
  and retry.
- On app-create failure, read the error message before retrying.
- `get_build_scan_report` returns the security scan; `cancel_build` stops a run.

## Multi-container apps

For the `multi-container` and `react-advanced` templates, all services are one
app with a single root `devfile.yaml` and one `app_id`. Running `build_app` from
the project root:

- builds all service images **in parallel** on a shared build context,
- registers images in **dependency order** derived from `dependsOn`,
- returns `build_ids`, `image_uris`, `deploy_order` and `service_urls`.

To rebuild one service, pass `service_name` to `build_app`. Always print every
`service_urls` entry after a successful build.

## Secrets and sharing

Secrets are scoped, including app-scoped secrets: `create_secret`, `get_secret`,
`list_secrets`, `update_secret`, `delete_secret`, `share_secret`. **Register the
app before touching app-scoped secrets.**

Permissions: `list_grantable_roles` to see what can be granted,
`list_app_permissions` for current state, then `grant_app_role` /
`revoke_app_role`. `list_account_users` enumerates who is available to grant to.

## Local build gotcha

For `build_mode: local`, `BUILDKIT_HOST` must be configured **on the MCP server
process**, not only in your interactive shell.
