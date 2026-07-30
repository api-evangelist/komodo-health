# Komodo Health

Komodo Health combines a de-identified real-world data foundation — the Healthcare
Map, over one trillion linked records covering 330M+ patients — with a healthcare
AI layer called Marmot, serving life sciences, payers, providers and consultancies.

Its developer surface is the **Marmot Development Kit**: the first-party `komodo`
package on PyPI, which ships a Python SDK, the `komodo` CLI, and a first-party
**Model Context Protocol server**. Authentication is OAuth 2.0 — device flow for
users, service-principal client credentials for machines — and the SDK brokers
access to a dedicated, per-account Komodo-managed Snowflake warehouse. Komodo does
not publish a public OpenAPI document; the REST control plane is auth-gated.

- Website — https://www.komodohealth.com/
- Developer docs — https://docs.komodohealth.com/
- Package — https://pypi.org/project/komodo/
- Trust center — https://trust.komodohealth.com/

Backed by: a16z, iconiq-capital

## Artifacts

| Path | Type |
|---|---|
| `packages/` | Packages / SDKs — the `komodo` PyPI distribution |
| `cli/` | CLI — full `komodo` command surface incl. App Builder |
| `mcp/` | MCPServer — stdio server, `list_snowflake` + 26 App Builder tools |
| `authentication/` | Authentication — OAuth 2.0 device flow + service principals |
| `conventions/` | Conventions — tenancy, async, tracing, diagnostics |
| `errors/` | ErrorCatalog — SDK exceptions + documented failure modes |
| `lifecycle/` | Lifecycle — semver, beta tier, status page, support |
| `changelog/` | ChangeLog — published PyPI versions + notable changes |
| `conformance/` | Conformance — OAuth 2.0, MCP, SOC 2, CMS QE, ICD-10/CPT/NDC |
| `security/` | TrustCenter + DomainSecurity |
| `well-known/` | WellKnown — probe results (none published) |
| `llms/` | LLMsTxt — verbatim from komodohealth.com/llms.txt |
| `skills/` | AgentSkill — three packaged flows |
