# Before you connect

ElevenFlo MCP is on for every account with a verified email address, on every
plan. There is no access request. Free accounts include 500 credits a month;
Pro includes 100,000.

You need an MCP client and browser access for sign-in and consent.

Add this server URL in your client:

```text
https://elevenflo.com/mcp
```

Use `elevenflo` if the client asks for a server label.

The transport is remote MCP over HTTP. Clients name it differently: Claude Code
uses `--transport http`, ChatGPT developer mode calls it "streaming HTTP", and
some clients label it "Streamable HTTP".

Sign in through OAuth. Do not paste a bearer token, API token, or manual
`Authorization` header for ElevenFlo MCP.
