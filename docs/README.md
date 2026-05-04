# ElevenFlo MCP Customer Docs

ElevenFlo MCP connects an AI tool to ElevenFlo's bankruptcy and restructuring intelligence over hosted remote MCP.

Use these pages for the launch access program:

- [Request access](request-access.md)
- [Quickstart](quickstart.md)
- [Setup](setup.md)
- Client setup (per client): [Claude Code](client-claude-code.md) · [Claude Desktop](client-claude-desktop.md) · [Codex CLI](client-codex-cli.md) · [Cursor](client-cursor.md) · [VS Code](client-vs-code.md) · [ChatGPT](client-chatgpt.md) · [Other clients](client-other.md)
- [Tool catalog](tool-catalog.md)
- [Permissions and data access](permissions-and-data-access.md)
- [Security and trust posture](security.md)
- [Examples](examples.md)
- [Registry metadata](registry.md)
- [Troubleshooting](troubleshooting.md)
- [Support](support.md)

Customer-facing endpoint:

```text
https://elevenflo.com/mcp
```

Authentication uses ElevenFlo web-app sign-in, OAuth 2.1, PKCE, and explicit consent.

The launch tool set is read-oriented. It supports case lookup, docket research, court-document search, source-text retrieval, summaries, filing analysis, document graph exploration, intel-event search, and case-law lookup.

`search` and `fetch` are available for OpenAI-compatible retrieval surfaces. They are compatibility tools; the native catalog remains the primary interface for Chapter 11 workflow-specific research.

The public catalog is the source of truth for the marketed launch subset. The
served MCP `tools/list` metadata mirrors that subset with read-only annotations
and enum-backed input schemas for constrained parameters.

`generate_bankruptcy_document` is not part of the customer launch subset.
