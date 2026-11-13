# Before you connect

Any ElevenFlo account with a verified email can connect. Free includes 200 MCP
credits a month and Pro 10,000. Reading dataset rows, or aggregates grouped by
case, firm or party name, requires Pro.

Add this server URL in your client, then sign in through the browser:

```text
https://elevenflo.com/mcp
```

Use `elevenflo` if the client asks for a server label.

The transport is remote HTTP: `--transport http` in Claude Code, "streaming
HTTP" in ChatGPT developer mode, "Streamable HTTP" elsewhere.

Sign-in is OAuth. ElevenFlo MCP takes no bearer token, API key or manual
`Authorization` header.
