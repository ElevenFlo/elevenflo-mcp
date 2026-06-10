# Quickstart

Setup takes about 5 minutes if you are already on ElevenFlo Pro.

## 1. Confirm access

You need:

- an ElevenFlo Pro account (MCP is included with Pro)
- an MCP-compatible client
- browser access for sign-in and consent

MCP is provisioned automatically when you upgrade to Pro — there is no separate access request. If a tool call returns an access error, confirm your Pro plan is active.

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

Use OAuth sign-in only. Do not manually paste a bearer token, API token, or custom `Authorization` header for ElevenFlo MCP.

## 3. Sign in

Start a tool call from your MCP client. The client should open ElevenFlo sign-in and consent.

Approve access only for the client and account you recognize.

## 4. Run a smoke test

Ask:

```text
Use ElevenFlo MCP to find the FTX Trading Ltd. Chapter 11 case. Return the top match with case name, court, case number, and case identifier.
```

`case_id` is the stable case identifier returned by ElevenFlo MCP. The response also includes `case_watch_id`, the legacy name for the same value; use either identifier in follow-up MCP calls.

Expected result:

```text
FTX Trading Ltd.
case_id: 1857
case_watch_id: 1857
```

## 5. Try a research prompt

```text
Find recent filings in the case, identify the plan or disclosure statement, and summarize the key recovery terms using source-backed citations.
```

## Guardrails

- Use `/mcp` as the endpoint path.
- Reconnect through OAuth if access was revoked or the grant expired.
- Revoke unused client grants from Account → AI connections.
- Use `read_text` for exact filing language.
- Use `analyze_document` only after selecting relevant documents and chunks.
