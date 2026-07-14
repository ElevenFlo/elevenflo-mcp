# Quickstart

Setup takes about 5 minutes with any ElevenFlo account.

## 1. Confirm access

You need:

- an ElevenFlo account with a verified email address (MCP is available on every plan — free accounts include 2,500 credits per month, Pro includes 100,000)
- an MCP-compatible client
- browser access for sign-in and consent

MCP is provisioned automatically — there is no separate access request. If a tool call returns an access error, confirm your email address is verified.

## 2. Add the endpoint

Transport: Remote MCP over HTTP / Streamable HTTP. Clients label this differently — Claude Code uses `--transport http`, VS Code uses `"type": "http"`, and private/workspace ChatGPT Developer Mode calls it "streaming HTTP" — but it is the same surface.

ChatGPT users should start with the
[published app directory](https://chatgpt.com/apps/elevenflo/asdk_app_6a27946962bc819180664633b81cc507)
unless using private/workspace Developer Mode.

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

`case_id` is the stable public case identifier returned by ElevenFlo MCP. The
response may also include `case_watch_id`, the legacy name for the same value;
use `case_id` in follow-up MCP calls.

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
- Each OAuth approval creates an independent client grant.
- Access tokens refresh automatically. The default refresh-token family lifetime
  is 30 days from OAuth approval; daily reauthorization is not expected.
- Reconnect through OAuth if access was revoked, entitlement changed, or the
  grant expired after its refresh-token family lifetime.
- Revoke unused client grants from Account → MCP connections.
- Use `read_document_text` for exact filing language.
- Use `analyze_document` only after selecting relevant documents and chunks.
