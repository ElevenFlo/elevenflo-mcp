# ChatGPT

> [!IMPORTANT]
> ChatGPT support depends on plan, workspace settings, and the exact ChatGPT
> surface. ChatGPT Apps / Developer Mode-compatible MCP usage is the private or
> workspace setup path. Deep Research and company-knowledge compatibility use
> read-only `search` and `fetch` where available. Agent mode is not currently
> the custom-MCP automation path.

For private or workspace setup, ChatGPT currently uses **Apps & Connectors** in
**Developer mode**. Public app listing is a separate OpenAI review and
publication flow.

Requirements:

- ChatGPT web
- ChatGPT access with Apps enabled for the workspace
- Developer mode enabled where your plan and workspace allow it

Create an app and paste:

```text
https://elevenflo.com/mcp
```

Use `ElevenFlo MCP` as the app name or `elevenflo` as the server label if
ChatGPT asks for one. Keep OAuth enabled. ChatGPT should open the ElevenFlo
sign-in and consent flow in your browser.

Smoke test:

```text
Use ElevenFlo MCP to find the FTX Trading Ltd. Chapter 11 case. Return the top match with case name, court, case number, and case identifier.
```

Company knowledge / Deep Research compatibility smoke:

```text
Search ElevenFlo for FTX DIP financing filings, fetch the most relevant filing, and cite the source URL.
```

> [!NOTE]
> Workspace Agents, Tasks, Deep Research, and company knowledge have separate
> availability and tool-access rules. For scheduled workflow posture, use
> [Scheduling and notifications](/docs/mcp/workflows/scheduling-and-notifications).

Workflow recipes:

- [Daily case brief](/docs/mcp/workflows/daily-case-brief)
- [First-day filings triage](/docs/mcp/workflows/first-day-filings-triage)
- [DIP and cash-collateral terms](/docs/mcp/workflows/dip-cash-collateral-terms)

> _Reviewed 2026-06-04 against OpenAI ChatGPT Developer Mode, Tasks, Workspace Agents, and Codex docs. OpenAI iterates on plan availability and UI paths; verify ElevenFlo access in the exact ChatGPT surface before relying on recurring workflows._
