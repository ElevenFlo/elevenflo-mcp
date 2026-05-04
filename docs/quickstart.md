# Quickstart

## 1. Confirm access

You need:

- an ElevenFlo account
- MCP access enabled for your account or organization
- an MCP-compatible client
- browser access for sign-in and consent

If MCP is not enabled yet, ask your ElevenFlo contact to enable launch access before configuring a client.

## 2. Add the endpoint

Transport: Remote MCP over HTTP / Streamable HTTP. Clients label this differently — Claude Code uses `--transport http`, VS Code uses `"type": "http"`, ChatGPT calls it "streaming HTTP" — but it is the same surface.

Add this MCP server URL in your client:

```text
https://elevenflo.com/mcp
```

Use this name if your client asks for a server name or label:

```text
elevenflo
```

Remote MCP over HTTP / Streamable HTTP. Use OAuth sign-in only. Do not manually paste a bearer token, API token, or custom `Authorization` header for ElevenFlo MCP.

## 3. Sign in

Start a tool call from your MCP client. The client should open ElevenFlo sign-in and consent.

Approve access only for the client and account you recognize.

## 4. Run a smoke test

Ask:

```text
Use ElevenFlo MCP to find the Chapter 11 case for FTX and return the top match with the case name and case_watch_id.
```

`case_watch_id` is ElevenFlo's stable internal case identifier. Use it to scope follow-up MCP calls to the same case.

Expected result:

```text
FTX Trading Ltd.
case_watch_id: 1857
```

## 5. Try a research prompt

```text
Find recent filings in the case, identify the plan or disclosure statement, and summarize the key recovery terms using source-backed citations.
```

## Guardrails

- Use `/mcp` as the endpoint path.
- Reconnect through OAuth if access was revoked or the grant expired.
- Revoke unused client grants from account settings -> Connected AI tools.
- Use `read_text` for exact filing language.
- Use `analyze_document` only after selecting relevant documents and chunks.
