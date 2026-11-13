# Troubleshooting

## Sign-in never opens, or the client reports 401

Confirm the server URL is `https://elevenflo.com/mcp`, remove any manual
`Authorization` header or static bearer-token setting, then reconnect through
OAuth. The same steps fix a revoked grant, a grant made for another endpoint
and a client that never saved its refreshed token.

A connection stays signed in while the client keeps refreshing it: each refresh
renews the 30-day refresh-token lifetime. After 30 days without one, sign in
again. Authorizing the same account from another client does not revoke this
connection.

## The tool list is missing a tool

The [tool catalog](https://elevenflo.com/docs/mcp/tool-catalog) is the canonical
list, and every plan gets the same 16 tools. If one is missing, confirm your
email address is verified and reconnect through OAuth; the client may have
cached an older `tools/list` response. A published or organization-approved app
can keep a frozen tool snapshot until its owner refreshes or republishes it.

A `not_entitled` error means the query needs Pro, not that a tool is missing.
Reading dataset rows and grouping an aggregate by a case, firm or party name
require Pro.

## Search results are empty

Resolve the case with `find_cases` first and narrow by `case_id`. Broaden date
filters, and search summaries before full text. Use an exact phrase only when
that phrase is likely to appear in the document.

## Analysis fails on a long document

Run `search_document_pages` first, then read only the pages it returns with
`read_document_pages`.

## Rate limit exceeded

Wait for the retry interval stated in the error before your client calls the
tool again. Reduce parallel calls and stop repeated retries while the limit is
active. Reconnecting does not reset the rate limit.

## Monthly credits exhausted

Billable calls draw on one monthly balance shared across your clients, separate
from the rate limit. A call fails when its cost exceeds the remaining balance.
Free includes 200 credits a month, reset on the first of the month;
[Pro](https://elevenflo.com/pricing) includes 10,000, with a rolling 24-hour
limit of 2,000. **Settings → Usage** shows the remaining balance.

When a limit is reached, the error says when more credits become available, for
example "More become available at 2:38 PM ET on Oct 15 (18:38 UTC)." Your client
should pass this on rather than retry. Reconnecting does not add credits. If the
error persists after that time, contact support with the tool name, error code
and time of the failed call.
