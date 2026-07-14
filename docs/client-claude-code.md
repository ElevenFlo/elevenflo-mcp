# Claude Code

Run this from any directory:

```bash
claude mcp add --scope user --transport http elevenflo https://elevenflo.com/mcp
claude mcp login elevenflo
claude mcp get elevenflo
```

Then start a prompt that needs ElevenFlo data. If OAuth has expired, Claude
Code opens the ElevenFlo sign-in and consent flow in your browser.

Smoke test:

```text
Use ElevenFlo MCP to find the FTX Trading Ltd. Chapter 11 case. Return the top match with case name, court, case number, and case identifier.
```

> [!TIP]
> If Claude Code already has an `elevenflo` server registered, run `claude mcp list` and remove the old entry before re-adding.

For Claude Code skill-file packaging, see
[Skill files](/docs/mcp/workflows/skill-files).
