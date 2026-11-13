# ElevenFlo MCP

ElevenFlo MCP is a hosted remote MCP server for court-grounded chapter 11
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

Claude (web and desktop) can connect from the
[ElevenFlo listing in Claude's connector directory](https://claude.ai/directory/connectors/elevenflo).
Other clients, including Codex CLI and ChatGPT, are covered in the client setup
pages linked below.

## Authentication

ElevenFlo web-app sign-in, OAuth 2.1, PKCE, and explicit consent. Start a tool
call from your client and it opens the ElevenFlo sign-in and consent flow. Do
not configure a static bearer token, API token, or custom `Authorization`
header for ElevenFlo MCP.

Clients that support and persist OAuth refresh tokens can refresh access
tokens. Each successful refresh renews the default 30-day refresh-token lifetime.
Revoke unused client grants from Account, then MCP connections.

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

ElevenFlo MCP has 16 tools: 13 for research and three that manage the
signed-in user's own monitors. The live `tools/list` response and the
[tool catalog](docs/tool-catalog.md) are the canonical contract; the groups
below are the shape of it.

**Cases and dockets.** Resolve a chapter 11 case by debtor identity and browse
its docket. Save monitors (a watched case, or market filters such as chapter,
state, status and asset class) and read what changed for them, or for named
cases, from a resumable cursor that also delivers corrections and removals.
Zero updates does not prove an inactive docket.

**Documents and transcripts.** Search court documents and machine hearing
transcripts across all cases or within one case, see which pages hold a few
keywords, then read exact pages, the pages matching a query, or a transcript at
a time. Read stored document summaries, and find the most-cited documents in a
case.

**Datasets.** Query typed datasets when the question is about comparable
records or a population rather than document text: list the datasets, describe
one before querying it, then search typed rows or aggregate them. Free can list
and describe datasets and run aggregates that do not group by a case, firm or
party name; reading rows requires Pro. The
[tool catalog](docs/tool-catalog.md#structured-data) lists the public datasets;
any other slug resolves as `unknown_dataset`.

## Boundary

Apart from saving, editing and deleting the signed-in user's own monitors, the
public MCP cannot write data, file documents, send messages, modify
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

Each successful tool response uses MCP credits based on returned records
and content bytes. Free includes 200 per calendar month with no daily limit;
Pro includes 10,000 per month and 2,000 per rolling day. Clients share the account's
120 requests per minute. See the [credit formula](https://elevenflo.com/docs/mcp/tool-catalog#credits)
and current account balances. Failed calls are not charged.

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

Claude users can start from the
[connector directory listing](https://claude.ai/directory/connectors/elevenflo). ChatGPT
users should start with the
[published app directory](https://chatgpt.com/apps/elevenflo/asdk_app_6a27946962bc819180664633b81cc507)
unless using private/workspace Developer Mode.

## Support

Email support@elevenflo.com. Run the smoke test above first and include its
output, plus your MCP client and version, the endpoint URL, the case or document
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

Server implementation and account credentials are not part of this repository.
