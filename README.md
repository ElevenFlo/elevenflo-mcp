# ElevenFlo MCP

ElevenFlo MCP is a hosted remote MCP server for court-grounded Chapter 11
bankruptcy and restructuring research.

> [!NOTE]
> This repository is a generated mirror. Its `README.md`, `server.json`,
> `llms.txt`, and everything under `docs/` are published from the ElevenFlo
> monorepo by `scripts/sync_mcp_mirror.sh`. Edits made here are overwritten on
> the next sync; send changes to the monorepo instead.

## Endpoint

```text
https://elevenflo.com/mcp
```

Transport: remote MCP over HTTP / Streamable HTTP. Clients label this
differently (Claude Code calls it `--transport http`, private/workspace ChatGPT
Developer Mode calls it "streaming HTTP"), but it is the same surface. Use
`elevenflo` when a client asks for a server name or label.

Claude Code, from any directory:

```bash
claude mcp add --scope user --transport http elevenflo https://elevenflo.com/mcp
claude mcp login elevenflo
claude mcp get elevenflo
```

Other clients, including Claude Desktop, Codex CLI, and ChatGPT, are covered
in the client setup pages linked below.

## Authentication

ElevenFlo web-app sign-in, OAuth 2.1, PKCE, and explicit consent. Start a tool
call from your client and it opens the ElevenFlo sign-in and consent flow. Do
not configure a static bearer token, API token, or custom `Authorization`
header for ElevenFlo MCP.

Access tokens refresh automatically; the default refresh-token family lifetime
is 30 days from OAuth approval. Revoke unused client grants from
Account, then MCP connections.

Unauthenticated browser requests to `https://elevenflo.com/mcp` may return
`401`. That path is the protected MCP protocol endpoint, not a browser
documentation page.

## Smoke test

```text
Use ElevenFlo MCP to find the FTX Trading Ltd. Chapter 11 case. Return the top match with case name, court, case number, and case identifier.
```

Expected result:

```text
FTX Trading Ltd.
case_id: 1857
```

`case_id` is the stable public case identifier returned by ElevenFlo MCP; use
it in follow-up MCP calls.

## What it covers

ElevenFlo MCP exposes 18 read-only research tools. The live `tools/list`
response and the [tool catalog](docs/tool-catalog.md) are the canonical
contract; the groups below are the shape of it.

**Case research.** Resolve a Chapter 11 case by debtor identity or by
structured case metadata, list the canonical filing categories and tags, and
browse a case docket.

**Documents and filings.** Search filing evidence across all cases or within
one case, retrieve exact court-document text with offsets, extract passages
answering a focused question, read stored filing summaries, and explore the
citation graph between documents in a case.

**Transcripts and news.** Search indexed hearing and court transcripts, and
search bankruptcy news and source coverage. News results carry publisher
metadata, links, and snippets; raw article text is not exposed.

**Structured data.** Query typed datasets when the question is about
comparable records or a population rather than filing text: discover the
datasets available to your account, describe one before querying it, then
return typed rows, grouped aggregates, or normalized value suggestions. Fifteen
datasets are on the public contract:

- `ballot-tabulation`
- `ballot-class-results`
- `committee-appointments`
- `hearings-case-rollup`
- `sale-process`
- `transfers-of-claim`
- `voluntary-petitions`
- `keip-kerp`
- `rule-2019-statements`
- `fee-applicant-rollups`
- `post-confirmation-reports`
- `schedule-summary-totals`
- `bar-dates`
- `exclusivity-periods`
- `case-outcomes`
- `case-disclosure-solicitation`

That is an explicit allowlist, not every structured dataset ElevenFlo
maintains. Other datasets resolve as `unknown_dataset`.

## Read-only boundary

The public MCP cannot write data, file documents, send messages, modify
dockets, manage accounts or billing, grant access to other users, expose
internal operating tools, or return raw publisher article text. Structured-data
responses expose only public fields and capabilities for the caller's tier.
OAuth grants and account entitlements still determine the request-scoped tool
catalog.

Scheduling, notifications, email drafts, and workspace storage belong to the
host client, not to ElevenFlo MCP.

## Plans and credits

ElevenFlo MCP is available on every ElevenFlo plan and is provisioned
automatically; there is no separate access request. You need an ElevenFlo
account with a verified email address and an MCP-compatible client.

Each tool call costs a fixed 1, 2, 3, or 5 credits. Free accounts include 500
credits per month and Pro includes 100,000, resetting at the start of each
calendar month. Failed tool calls are never charged. Current usage appears on
your account page under MCP connections.

## Documentation

Published docs live at
[elevenflo.com/docs/mcp/overview](https://elevenflo.com/docs/mcp/overview).
The copies in this repository are the same pages.

- Overview: [elevenflo.com](https://elevenflo.com/docs/mcp/overview)
- Setup and quickstart:
  [elevenflo.com](https://elevenflo.com/docs/mcp/setup) ·
  [docs/quickstart.md](docs/quickstart.md) ·
  [docs/verify.md](docs/verify.md)
- Tool catalog:
  [elevenflo.com](https://elevenflo.com/docs/mcp/tool-catalog) ·
  [docs/tool-catalog.md](docs/tool-catalog.md)
- Permissions and data access:
  [elevenflo.com](https://elevenflo.com/docs/mcp/permissions-and-data-access) ·
  [docs/permissions-and-data-access.md](docs/permissions-and-data-access.md)
- Workflow recipes:
  [elevenflo.com](https://elevenflo.com/docs/mcp/workflows) ·
  [docs/workflows.md](docs/workflows.md)
- Troubleshooting: [docs/troubleshooting.md](docs/troubleshooting.md)

Client setup: [Claude Code](docs/client-claude-code.md) ·
[Claude Desktop](docs/client-claude-desktop.md) ·
[Codex CLI](docs/client-codex-cli.md) ·
[ChatGPT](docs/client-chatgpt.md) ·
[Other clients](docs/client-other.md)

ChatGPT users should start with the
[published app directory](https://chatgpt.com/apps/elevenflo/asdk_app_6a27946962bc819180664633b81cc507)
unless using private/workspace Developer Mode.

## Support

Email support@elevenflo.com. Run the smoke test above first and include its
output, plus your MCP client and version, the endpoint URL, the case or filing
involved, the tool or prompt attempted, the approximate time, the error
message, and whether it affects one user or several.
[docs/support.md](docs/support.md) has the full template and severity
definitions.

## Registry metadata

The official MCP Registry record for `com.elevenflo/mcp` is generated from
[server.json](server.json) in this repository. Publishing a new version to
`registry.modelcontextprotocol.io` is a separate deliberate step.

## Repository boundary

This repository contains public setup docs, workflow recipes, tool catalog
copy, `llms.txt`, and registry metadata.

It does not contain ElevenFlo backend source, ingestion or ranking logic,
private schemas, customer identifiers, secrets, billing internals, or provider
integrations.
