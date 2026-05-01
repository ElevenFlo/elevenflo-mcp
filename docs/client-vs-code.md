# VS Code

Use the **Add to VS Code** button above. It opens VS Code and pre-fills the MCP server entry. Approve OAuth when prompted.

Manual path: open your user `settings.json` and add:

```json
"mcp.servers": {
  "elevenflo": {
    "type": "http",
    "url": "https://elevenflo.com/mcp"
  }
}
```

Then run a GitHub Copilot chat prompt that needs ElevenFlo data to trigger the OAuth flow.

> [!NOTE]
> GitHub Copilot MCP support requires a recent VS Code version with the MCP preview enabled.

> _Last verified 2026-04-29 against current VS Code stable. Microsoft is iterating on the MCP surface; the `mcp.servers` setting key is stable but the UI for managing servers may move._
