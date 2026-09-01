# Troubleshooting

## Sign-in never opens, or the client reports 401

Confirm the server URL is `https://elevenflo.com/mcp`, remove any manual
`Authorization` header or static bearer-token setting, reconnect through OAuth,
and retry.

The same failure covers a grant that was revoked, a grant created against a
different endpoint, and a client that never saved its refreshed token.

Tokens refresh automatically for 30 days, so you will not sign in daily. Signing
in again is only required when someone revoked the grant, a plan change removed
access, or the grant reached its 30-day limit. Authorizing the same account from
another client does not revoke this one.

## The tool list is missing a tool

The [tool catalog](https://elevenflo.com/docs/mcp/tool-catalog) is the canonical
list, and the same tools are available on every plan. If a catalog tool is
missing, confirm your email address is verified and reconnect through OAuth in
case the client cached an older `tools/list` response.

## Search results are empty

Resolve the case with `find_cases` first and narrow by `case_id`. Broaden date
filters, and search summaries before full text. Use an exact phrase only when
that phrase is likely to appear in the filing.

## Analysis fails on a long document

Run `search_document_chunks` first, then pass the selected chunk IDs to
`read_document_chunks`.

## Rate limit or usage error

Every tool call draws on one monthly balance shared across your clients, and
calls fail once it reaches zero. Free accounts include 500 credits a month and
reset on the first of the month; [Pro](https://elevenflo.com/pricing) includes
100,000. Account → MCP connections shows the allowance and the remaining
balance.

If credits remain and the client still reports a usage error, reconnect through
OAuth and retry, then contact support.
