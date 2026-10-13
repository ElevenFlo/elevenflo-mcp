# Pay as an agent

ElevenFlo research is available to agents without an account. Buy a prepaid
tab over HTTPS with the Machine Payments Protocol (MPP), then use your purchase
token as a bearer credential on the public MCP endpoint. Check
[`GET /api/agent/v1/catalog`](https://elevenflo.com/api/agent/v1/catalog) for
the current offer and whether new purchases are enabled.

People use the same endpoint through
[OAuth sign-in](quickstart.md); a tab is for an agent that holds a wallet and
no ElevenFlo account.

## Offer

One purchase is **USD 5.00 for 100 research credits**. A credit measures
returned data: each successful call costs
`max(1, ceil(records / 10), ceil(bytes / 10000))`, the same formula as
subscribed accounts (see [credits](tool-catalog.md#credits)). Credits do not
expire. Buying again with the same purchase token adds 100 credits to the same
tab. A refund or dispute revokes the tab.

## Request a quote

Generate two independent values and retain both:

- `Idempotency-Key`: a random UUID v4 identifying this purchase request.
- `X-Purchase-Token`: 32 random bytes encoded as unpadded base64url, producing
  43 characters. This secret is your tab. Prefixed with `eft_`, it is also your
  MCP bearer credential, so generate it with a cryptographic random source and
  keep it private.

Send `POST https://elevenflo.com/api/agent/v1/tab/purchases` with those headers
and `Content-Type: application/json`. The body is optional:

```json
{"max_credits_per_call": 50}
```

`max_credits_per_call` is the largest charge one tool call may incur on this
tab (default 50, at most 10,000). A result that measures above it is withheld
and not charged. A later purchase with the same token replaces the setting.

A valid quote returns HTTP `402` and a standard `WWW-Authenticate: Payment`
challenge. Review the quoted amount, merchant and terms before approving
payment through your client's MPP flow.

## Pay

Retry the same URL, body, `Idempotency-Key` and `X-Purchase-Token` with the MPP
credential in `Authorization: Payment …`. A successful payment returns HTTP
`200`, a `Payment-Receipt` header, and:

```json
{
  "purchase_id": "…",
  "status": "paid",
  "credential": "eft_<your purchase token>",
  "tab": {"tab_id": "…", "credits": 100, "credits_used": 0, "credits_remaining": 100, "max_credits_per_call": 50, "revoked": false}
}
```

Retain the same request identity after a timeout. HTTP `202` means settlement
is pending; follow `Retry-After` and read
`GET /api/agent/v1/tab/purchases/{purchase_id}` with `X-Purchase-Token`.
Repeating the original POST returns the paid response again without a new
charge. Creating another purchase while the first is pending can create a
separate charge. A different payment credential cannot replace one already
submitted.

With Stripe's Link Agent Wallet the whole flow is one command:

```bash
link-cli mpp pay https://elevenflo.com/api/agent/v1/tab/purchases \
  --method POST --data '{}' \
  --header "Idempotency-Key: $(uuidgen | tr A-Z a-z)" \
  --header "X-Purchase-Token: $TOKEN" \
  --context "Buying 100 ElevenFlo research credits (USD 5.00) so this agent can read chapter 11 court filings and structured case data on the customer's behalf."
```

## Use the credential

Connect any MCP client to `https://elevenflo.com/mcp` with the header
`Authorization: Bearer eft_<your purchase token>`. Every public tool is
available, with full structured-dataset access. For example, in Claude Code:

```bash
claude mcp add --transport http elevenflo https://elevenflo.com/mcp \
  --header "Authorization: Bearer eft_$TOKEN"
```

Each successful result carries `_meta` with `elevenflo/credits` (what the call
cost) and `elevenflo/credits_remaining`. To cap one call below the tab setting,
send `_meta: {"elevenflo/max_credits": N}` on the `tools/call` request.

When a result would exceed the remaining balance, the tool returns
`payment_required` with `available_credits` and `required_credits`; the result
is withheld and nothing is charged. `budget_exceeded` means the result measured
above the call budget; narrow the request or raise the budget. An identical
request from the same tab within 60 seconds is not charged twice.

## Balance

`GET https://elevenflo.com/api/agent/v1/tab` with `X-Purchase-Token` returns
the tab's credits, usage, setting, revocation state and the last 20 debits.

## Response states

| HTTP status | Meaning | Next action |
| --- | --- | --- |
| `200` | Paid; credential and balance returned | Save the credential and receipt. |
| `202` | Settlement pending | Retain the purchase ID and check its status. |
| `402` | Quote awaiting payment | Review the offer and approve its MPP challenge. |
| `404` | Purchase, tab or token not recognized | Check the retained purchase ID and token. |
| `409` | Request identity or pending credential conflict | Reuse the original request and credential. |
| `410` | Quote expired, payment failed, purchase reversed, or tab revoked | Read the returned state; a revoked tab cannot be topped up, so generate a new purchase token. |
| `429` | Request limit reached | Wait before retrying the same request. |
| `503` | New purchases or a required service are unavailable | Retry later; retain any existing purchase ID. |

Quote requests are limited to five per minute per source IP and 100 per minute
across the service. Status and balance reads have a separate limit of 60 per
minute per source IP. MCP calls share the tab's 120-request-per-minute limit.
Payment responses use `Cache-Control: private, no-store` and are excluded from
indexing. Use remains subject to the
[terms of use](https://elevenflo.com/terms-of-use); contact
[support](https://elevenflo.com/contact-us) with your purchase ID for payment
questions.
