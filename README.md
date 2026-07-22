# ElevenFlo MCP

ElevenFlo MCP is a hosted remote MCP server for court-grounded Chapter 11
bankruptcy and restructuring research.

Production endpoint:

```text
https://elevenflo.com/mcp
```

Transport: remote MCP over HTTP / Streamable HTTP.

Authentication: ElevenFlo web-app sign-in, OAuth 2.1, PKCE, and explicit
consent. Do not configure static bearer tokens or pasted API keys for ElevenFlo
MCP.

ElevenFlo MCP is available on every plan, including free accounts, and is
provisioned automatically — see [Access](docs/access.md).

## Documentation

These pages mirror the live docs at <https://elevenflo.com/docs> and are
published from the ElevenFlo monorepo. Do not edit them here.

- [Access](docs/access.md)
- [Quickstart](docs/quickstart.md)
- [Tool catalog](docs/tool-catalog.md)
- [Permissions and data access](docs/permissions-and-data-access.md)
- [Troubleshooting](docs/troubleshooting.md)
- [Support](docs/support.md)

Client setup:

- [Claude Code](docs/client-claude-code.md)
- [Claude Desktop](docs/client-claude-desktop.md)
- [Codex CLI](docs/client-codex-cli.md)
- [Cursor](docs/client-cursor.md)
- [VS Code](docs/client-vs-code.md)
- [ChatGPT](docs/client-chatgpt.md)
- [Other clients](docs/client-other.md)

Workflow recipes:

- [Overview](docs/workflows.md)
- [Daily case brief](docs/workflow-daily-case-brief.md)
- [First-day filings triage](docs/workflow-first-day-filings-triage.md)
- [DIP and cash-collateral terms](docs/workflow-dip-cash-collateral-terms.md)
- [Scheduling and notifications](docs/workflow-scheduling-and-notifications.md)
- [Skill files](docs/workflow-skill-files.md)
- [Safety and verification](docs/workflow-safety-and-verification.md)

## Registry Metadata

The official MCP Registry metadata lives in [server.json](server.json).

- Registry name: `com.elevenflo/mcp`
- Display title: `ElevenFlo MCP`
- Transport: `streamable-http`
- Remote URL: `https://elevenflo.com/mcp`
- Namespace authentication: domain-based authentication for `elevenflo.com`

OpenAI/ChatGPT public app review is a separate platform review path and is not
controlled by this registry metadata.

Unauthenticated browser requests to `https://elevenflo.com/mcp` may return
`401`. That path is the protected MCP protocol endpoint, not a browser
documentation page.

## Tool Set

The tool set is read-only, OAuth-only, and source-grounded. It supports
Chapter 11 case lookup, docket browsing, filing and source discovery, hearing
transcript search where indexed, exact court-document text retrieval, filing
summaries, targeted filing analysis, document relationship exploration, and
OpenAI-compatible `search`/`fetch`.

The tool set does not support filing documents, sending email, modifying
dockets, creating legal-document artifacts, changing account settings, managing
billing, or granting access to other users.

The [tool catalog](docs/tool-catalog.md) is the canonical list of what the
connector exposes.

## Public Repo Boundary

This repository contains public setup docs, workflow recipes, tool catalog
copy, changelog, `llms.txt`, and registry metadata.

It does not contain ElevenFlo backend source, ingestion or ranking logic,
private schemas, customer identifiers, secrets, billing internals, or provider
integrations.
