# Troubleshooting

## The client does not open sign-in

Confirm the MCP URL is:

```text
https://elevenflo.com/mcp
```

Remove any manual `Authorization` header or static bearer-token setting.

Restart the client and try the FTX smoke test again.

## Sign-in works but the tool call fails

The OAuth grant may have expired, been revoked, or been created for a different endpoint.

Reconnect the `elevenflo` MCP server and complete sign-in and consent again.

Use:

```text
https://elevenflo.com/mcp
```

## The client says unauthorized or 401

Likely causes:

- OAuth was not completed.
- The grant was revoked.
- The client is using credentials created for a different endpoint.
- A stale manual `Authorization` header is still configured.

Fix:

- Remove manual headers.
- Reconnect through OAuth.
- Retry the FTX smoke test.

## The tool list is missing expected tools

ElevenFlo MCP advertises only its research tool set. If a tool from the catalog isn't showing up:

- Check the [Tool catalog](/docs/mcp/tool-catalog) — that page is the canonical list.
- Confirm your Pro plan is active. MCP tools appear once you are on Pro.
- Reconnect through OAuth if the client cached an older `tools/list` response.

`build_case_context_pack`, `search_intel_events`, `lookup_case_law`, and
`generate_bankruptcy_document` are not part of the current customer tool set.

If the tool is in the catalog and your Pro plan is active but the client still does not list it, contact ElevenFlo support with:

- client name and version
- endpoint URL
- account email
- approximate time of the failed connection

## Search results are empty

Try:

- checking the case name
- using `search_cases` first
- narrowing by `case_id` or `case_watch_id`
- broadening date filters
- searching summaries before full text
- using exact phrases only when the phrase is likely to appear in the filing

## Analysis fails on a long document

Use `search_within` first, then pass selected chunk IDs to `analyze_document`.

For exact language, use `read_text`.

## Rate limit or usage error

Check the Account → AI connections usage meter for your current allowance and remaining balance. MCP usage is credit-based and resets at the start of each calendar month (UTC).

The credit cap is connector-wide. Lightweight lookup and retrieval tools use fewer credits than document-analysis tools, but they still count against the monthly balance.

If the account has credits remaining and the client still reports a usage
error, reconnect through OAuth and retry. If the error persists, contact
ElevenFlo support. Include the client name, account email, endpoint, tool name,
and approximate time.

## Unsupported client

Use a client that supports hosted remote MCP over HTTP with OAuth.

If your client only supports local stdio MCP servers or manual bearer tokens, it is not a supported customer path for ElevenFlo MCP.
