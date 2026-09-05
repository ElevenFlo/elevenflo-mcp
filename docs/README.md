# ElevenFlo MCP Customer Docs

ElevenFlo MCP connects your client to ElevenFlo's chapter 11 and restructuring
research over a hosted, remote MCP endpoint:

```text
https://elevenflo.com/mcp
```

Authentication uses ElevenFlo web-app sign-in, OAuth 2.1, PKCE, and explicit
consent. The tool set is read-only: case lookup, docket research,
court-document search and exact text, filing summaries, source discovery with
metadata, snippets, and publisher links, document-graph exploration, and typed
structured-dataset search and aggregation.

- [Before you connect](quickstart.md)
- [Verify the connection](verify.md)
- Client setup (per client): [Claude Code](client-claude-code.md) · [Claude Desktop](client-claude-desktop.md) · [Codex CLI](client-codex-cli.md) · [ChatGPT](client-chatgpt.md) · [Microsoft 365 Copilot](client-microsoft-365-copilot.md) · [Gemini](client-gemini.md) · [Other clients](client-other.md)
- Workflows: [Overview](workflows.md) · [Daily case brief](workflow-daily-case-brief.md) · [First-day filings triage](workflow-first-day-filings-triage.md) · [DIP and cash-collateral terms](workflow-dip-cash-collateral-terms.md) · [Automation](workflow-automation.md) · [Safety and verification](workflow-safety-and-verification.md)
- [Tool catalog](tool-catalog.md)
- [Permissions and data access](permissions-and-data-access.md)
- [Troubleshooting](troubleshooting.md)
- [Support](support.md)

The tool catalog is the source of truth for what the connector exposes.
`build_case_context_pack`, `search_intel_events`, and `lookup_case_law` are not
part of the current customer tool set. Scheduling, notifications, email drafts,
and client-side storage belong to your client, not to ElevenFlo MCP.

ChatGPT users should start with the
[published Plugin Directory listing](https://chatgpt.com/plugins/elevenflo)
unless using Developer Mode for a private or organization-managed connection.
