# ElevenFlo MCP Customer Docs

ElevenFlo MCP connects your AI tool to ElevenFlo's Chapter 11 and restructuring research over a hosted, remote MCP endpoint.

Use these pages to connect and use ElevenFlo MCP:

- [Access](access.md)
- [Quickstart](quickstart.md)
- Client setup (per client): [Claude Code](client-claude-code.md) · [Claude Desktop](client-claude-desktop.md) · [Codex CLI](client-codex-cli.md) · [Cursor](client-cursor.md) · [VS Code](client-vs-code.md) · [ChatGPT](client-chatgpt.md) · [Other clients](client-other.md)
- Workflow recipes: [Overview](workflows.md) · [Daily case brief](workflow-daily-case-brief.md) · [First-day filings triage](workflow-first-day-filings-triage.md) · [DIP and cash-collateral terms](workflow-dip-cash-collateral-terms.md) · [Scheduling and notifications](workflow-scheduling-and-notifications.md) · [Skill files](workflow-skill-files.md) · [Safety and verification](workflow-safety-and-verification.md)
- [Tool catalog](tool-catalog.md)
- [Permissions and data access](permissions-and-data-access.md)
- [Troubleshooting](troubleshooting.md)
- [Support](support.md)

Customer-facing endpoint:

```text
https://elevenflo.com/mcp
```

Authentication uses ElevenFlo web-app sign-in, OAuth 2.1, PKCE, and explicit consent.

The tool set is read-only. It supports case lookup, docket research, court-document search, source discovery with metadata/snippets/publisher links, summaries, filing analysis, and document graph exploration.

Workflow recipes show copy-paste prompts and host-specific skill guidance for
repeatable Chapter 11 research. Scheduling, notifications, email
drafts, and workspace storage belong to the host client, not to ElevenFlo MCP.

`search` and `fetch` are also exposed for OpenAI-compatible clients; the native catalog is the primary interface.

The tool catalog is the source of truth for what the connector exposes.

`build_case_context_pack`, `search_intel_events`, `lookup_case_law`, and
`generate_bankruptcy_document` are not part of the current customer tool set.
