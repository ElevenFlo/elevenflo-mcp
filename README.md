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

## Documentation

- [Quickstart](docs/quickstart.md)
- [Setup](docs/setup.md)
- [Tool catalog](docs/tool-catalog.md)
- [Permissions and data access](docs/permissions-and-data-access.md)
- [Security and trust posture](docs/security.md)
- [Examples](docs/examples.md)
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

## Registry Metadata

The official MCP Registry metadata lives in [server.json](server.json).

- Registry name: `com.elevenflo/mcp`
- Display title: `ElevenFlo MCP`
- Transport: `streamable-http`
- Remote URL: `https://elevenflo.com/mcp`
- Namespace authentication: domain-based authentication for `elevenflo.com`

OpenAI/ChatGPT public app review is a separate platform review path and is not
controlled by this registry metadata.

## Beta Scope

The customer beta is controlled, read-oriented, OAuth-only, and source-grounded.
It supports Chapter 11 case lookup, docket browsing, filing and source search,
exact source-text retrieval, summaries, filing analysis, document graph
exploration, case event search, and case-law metadata lookup.

The beta does not support filing documents, sending email, modifying dockets,
creating legal-document artifacts, changing account settings, managing billing,
or granting access to other users.

## Public Repo Boundary

This repository contains public setup docs, tool catalog copy, examples,
security posture, changelog, `llms.txt`, and registry metadata.

It does not contain ElevenFlo backend source, ingestion or ranking logic,
private schemas, customer identifiers, secrets, billing internals, or provider
integrations.
