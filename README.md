# Komodo Health

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Komodo Health is a healthcare technology company that combines a de-identified real-world data foundation — the Healthcare Map, over one trillion linked records covering 330M+ patients and refreshed daily — with a validated healthcare AI layer called Marmot, serving life sciences, payers, providers, financial services and consultancies. Its developer surface is the Marmot Development Kit: a first-party Python SDK and `komodo` CLI distributed on PyPI that authenticate with OAuth 2.0 (device flow for users, service-principal client credentials for machines) and broker access to a dedicated, per-account Komodo-managed Snowflake warehouse. The same package ships a first-party Model Context Protocol server that lets AI assistants explore warehouse schemas and drive the Komodo App Builder, a Marmot Analysis API reachable from `client.marmot` and `komodo marmot -c`, and a beta Cohort API for interactive patient-cohort exploration over ICD-10, CPT/HCPCS and NDC codes. Authentication runs against Komodo's own OpenID Connect authorization server at auth.komodohealth.com, which publishes anonymous OIDC and RFC 8414 discovery documents.

**Areas:** Company · Healthcare · Health Data · Life Sciences · Real-World Data · Healthcare Analytics · Artificial Intelligence · Data · Snowflake · MCP

## Komodo Health on APIs.io

Everything profiled for Komodo Health is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/komodo-health/](https://apis.io/providers/komodo-health/)** — or go straight to one of the APIs or properties below.

### APIs (1)

- [Komodo Health Marmot Development Kit](https://apis.io/apis/komodo-health/komodo-health-marmot-development-kit/) — The Marmot Development Kit is Komodo Health's developer surface — a Python SDK and `komodo` CLI published to PyPI that handle OAuth 2.0 authentication,…

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [komodo](https://apis.io/servers/komodo-health/komodo/)

#### Security (3)

Authentication, trust centre, and vulnerability disclosure.

- [Komodo Health Authentication](https://apis.io/security/komodo-health/komodo-health-authentication/)
- [Komodo Health Domain Security](https://apis.io/security/komodo-health/komodo-health-domain-security/)
- [Komodo Health Trust Center](https://apis.io/security/komodo-health/komodo-health-trust-center/)

#### OAuth scopes (1)

The permissions the API asks for.

- [Komodo Health Scopes](https://apis.io/scopes/komodo-health/komodo-health-scopes/)

#### Plans and pricing (1)

Published plans, tiers and prices.

- [Komodo Health Plans Pricing](https://apis.io/plans/komodo-health/komodo-health-plans-pricing/)

#### Rate limits (1)

Published request limits.

- [Komodo Health Rate Limits](https://apis.io/rate-limits/komodo-health/komodo-health-rate-limits/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://www.komodohealth.com/)
- [Documentation](https://docs.komodohealth.com/)
- [Getting started](https://docs.komodohealth.com/guides-tutorials/guides/1-quickstart/)
- [API reference](https://docs.komodohealth.com/reference/sdk/)
- [Sign up](https://www.komodohealth.com/get-demo/)
- [Support](https://komodohealthsupport.zendesk.com/hc/en-us)
- [Changelog](https://docs.komodohealth.com/reference/changelog/)
- [Blog](https://www.komodohealth.com/perspectives/)
- [Compliance](https://trust.komodohealth.com/)
- [Terms of service](https://www.komodohealth.com/terms-of-service/)
- [Privacy policy](https://www.komodohealth.com/privacy-notice/)
- [GitHub](https://github.com/komodohealth)
- [Status](https://statuspage.komodohealth.com/)

## API Evangelist rating

**Kin Score 39.4** — developing · rubric 0.24.0 · scored 2026-10-20

| Facet | Score |
|---|---|
| Discoverability | 73.3 |
| Contract quality | 0.0 |
| Contract governance | 18.2 |
| Operational transparency | 34.2 |
| Developer ergonomics | 70.8 |
| Access clarity | 42.1 |

**Agent readiness 12.3** — agent-aware

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | — |
| Agentic Access | — |
| Reversibility Documented | — |
| MCP Server | documented (partial credit) |
| Auth Clarity | bearer (partial credit) |
| Idempotency | — |
| Error Semantics | documented (partial credit) |
| OpenAPI Examples | — |
| Rate Limit Signal | documented (partial credit) |
| Event Surface Described | — |
| Agent Skills | derived (partial credit) |
| Well Known Catalog | — |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | — |
| Protected Resource Metadata | — |
| Dynamic Client Registration | — |
| Agentic Commerce | — |

**Regulatory alignment 30.9** — Health

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/komodo-health/](https://apis.io/providers/komodo-health/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Komodo Health: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/komodo-health/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
