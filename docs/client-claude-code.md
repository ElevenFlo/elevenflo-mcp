# Claude Code

Run this from any directory:

```bash
claude mcp add --transport http elevenflo https://elevenflo.com/mcp
```

Then start a prompt that needs ElevenFlo data. Claude Code opens the ElevenFlo OAuth sign-in and consent flow in your browser.

Smoke test:

```text
Use the elevenflo MCP tool search_cases to find the Chapter 11 case FTX and return the top match.
```

> [!TIP]
> If Claude Code already has an `elevenflo` server registered, run `claude mcp list` and remove the old entry before re-adding.
