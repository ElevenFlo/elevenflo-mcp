# Claude Code

Run this from any directory:

```bash
claude mcp add --transport http elevenflo https://elevenflo.com/mcp
```

Then start a prompt that needs ElevenFlo data. Claude Code opens the ElevenFlo OAuth sign-in and consent flow in your browser.

Smoke test:

```text
Use ElevenFlo MCP to find the FTX Trading Ltd. Chapter 11 case. Return the top match with case name, court, case number, and case identifier.
```

> [!TIP]
> If Claude Code already has an `elevenflo` server registered, run `claude mcp list` and remove the old entry before re-adding.

For Claude Code skill-file packaging, see
[Skill files](/docs/mcp/workflows/skill-files).
