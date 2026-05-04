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

ElevenFlo MCP advertises only the launch subset to enabled accounts. If a tool from the catalog isn't showing up:

- Check the [Tool catalog](/mcp/tool-catalog) — that page is the canonical list.
- Confirm your MCP access; some tools may be gated by entitlement and only appear once your organization is enabled.
- Reconnect through OAuth if the client cached an older `tools/list` response.

`generate_bankruptcy_document` is not part of the customer launch subset.

If the tool is in the catalog and you have MCP access but the client still does not list it, contact ElevenFlo support with:

- client name and version
- endpoint URL
- account email
- approximate time of the failed connection

## Search results are empty

Try:

- checking the case name
- using `search_cases` first
- narrowing by `case_watch_id`
- broadening date filters
- searching summaries before full text
- using exact phrases only when the phrase is likely to appear in the filing

## Analysis fails on a long document

Use `search_within` first, then pass selected chunk IDs to `analyze_document`.

For exact language, use `read_text`.

## Rate limit or usage error

Retry later or contact ElevenFlo support. Include the client name, account email, endpoint, and approximate time.

## Unsupported client

Use a client that supports hosted remote MCP over HTTP with OAuth.

If your client only supports local stdio MCP servers or manual bearer tokens, it is not a supported customer path for ElevenFlo MCP.
