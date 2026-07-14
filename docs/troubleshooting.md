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

Authorizing the same ElevenFlo account from another compatible client should
not revoke this connection. Access tokens refresh automatically, and the default
refresh-token family lifetime is 30 days from OAuth approval; daily
reauthorization is not expected. Reauthorization is expected after the grant is
revoked, entitlement changes remove access, or the refresh-token family reaches
its absolute lifetime.

Use:

```text
https://elevenflo.com/mcp
```

## The client says unauthorized or 401

Likely causes:

- OAuth was not completed.
- The grant was revoked.
- The client is using credentials created for a different endpoint.
- The client failed to persist a rotated refresh token and is replaying stale
  local credentials.
- A stale manual `Authorization` header is still configured.

Fix:

- Remove manual headers.
- Reconnect through OAuth.
- Retry the FTX smoke test.

## The tool list is missing expected tools

ElevenFlo MCP advertises only its research tool set. If a tool from the catalog isn't showing up:

- Check the [Tool catalog](/docs/mcp/tool-catalog) — that page is the canonical list.
- Confirm your email address is verified. The same tool set is available on
  every plan.
- Reconnect through OAuth if the client cached an older `tools/list` response.

`build_case_context_pack`, `search_intel_events`, `lookup_case_law`, and
`generate_bankruptcy_document` are not part of the current customer tool set.

If the tool is in the catalog and the client still does not list it, contact ElevenFlo support with:

- client name and version
- endpoint URL
- account email
- approximate time of the failed connection

## Search results are empty

Try:

- checking the case name
- using `find_cases` first
- narrowing by `case_id`
- broadening date filters
- searching summaries before full text
- using exact phrases only when the phrase is likely to appear in the filing

## Analysis fails on a long document

Use `search_within_documents` first, then pass selected chunk IDs to `analyze_document`.

For exact language, use `read_document_text`.

## Rate limit or usage error

Check the Account → MCP connections usage meter for your current allowance and remaining balance. MCP usage is credit-based and resets at the start of each calendar month.

The credit cap is connector-wide. Lightweight lookup and retrieval tools use fewer credits than document-analysis tools, but they still count against the monthly balance.

Free accounts include 2,500 credits per month. If a tool call reports the
credit limit was reached, the balance resets at the start of the next calendar
month, or [upgrade to Pro](/pricing) for 100,000 credits per month.

If the account has credits remaining and the client still reports a usage
error, reconnect through OAuth and retry. If the error persists, contact
ElevenFlo support. Include the client name, account email, endpoint, tool name,
and approximate time.

## Unsupported client

Use a client that supports hosted remote MCP over HTTP with OAuth.

If your client only supports local stdio MCP servers or manual bearer tokens, it is not a supported customer path for ElevenFlo MCP.
