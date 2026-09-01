# Claude Code

Run this from any directory:

```bash
claude mcp add --scope user --transport http elevenflo https://elevenflo.com/mcp
claude mcp login elevenflo
claude mcp get elevenflo
```

Then run a prompt that needs ElevenFlo data. Claude Code opens sign-in in your
browser when the grant is missing or expired.

> [!TIP]
> If `claude mcp list` already shows an `elevenflo` server, run
> `claude mcp remove elevenflo` before adding it again.
