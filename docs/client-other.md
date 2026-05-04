# Other MCP clients

Any client that supports hosted remote MCP over HTTP with OAuth 2.1 can connect.

Use the canonical URL in your client's remote MCP setup flow:

```text
https://elevenflo.com/mcp
```

Use `elevenflo` when the client asks for a server label. Pick OAuth sign-in when prompted.

> [!WARNING]
> Do not paste a static bearer token into the client. OAuth is the only supported authentication path for customer MCP access.

If the client can only drive stdio MCP servers or only supports manual bearer tokens, it is not a supported customer path for ElevenFlo MCP. Contact ElevenFlo support for options.
