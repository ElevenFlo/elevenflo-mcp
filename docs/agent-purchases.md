# Single-purchase data API

The HTTPS purchase API returns one frozen dataset for one case. It uses the
Machine Payments Protocol (MPP) with Stripe card payments. Check
[`GET /api/agent/v1/catalog`](https://elevenflo.com/api/agent/v1/catalog) for the
offer definition and whether new purchases are enabled.

This interface is separate from the read-only ElevenFlo MCP connection.
Use a client that can send HTTPS requests and complete Stripe's MPP payment
approval. An MCP connection in ChatGPT or Claude does not itself provide that
payment capability. For subscribed research, use the existing
[MCP setup](https://elevenflo.com/docs/mcp/setup).

## Offer and delivery

`case_dataset_snapshot_v1` costs **$2 USD** for one case and one dataset.
The catalog lists `dip-financing` and `fee-applicant-rollups`. A listed dataset
does not guarantee a purchasable snapshot for every case.

Before quoting, ElevenFlo checks the selected case for complete, current,
source-linked data. Empty results, incomplete results, unavailable sources,
and snapshots above 100 rows or 256 KiB are rejected without a charge.
The quote states the row count, data timestamp, snapshot digest, merchant,
total price, expiry, and terms version.

The snapshot is fixed when the quote is created. The quote expires after
15 minutes. After payment, the same snapshot can be retrieved for 30 days.
Later changes to a case require a new purchase. This purchase provides the
quoted data response; it does not create an account, subscription, or credit
balance. Use remains subject to the
[terms of use](https://elevenflo.com/terms-of-use).

If ElevenFlo cannot return the stored snapshot during its retrieval window,
the purchase enters `refund_pending` and a full refund is requested. A
refunded or disputed purchase cannot retrieve the data. Save your purchase ID
and contact [support](https://elevenflo.com/contact-us) for payment questions.
Keep your purchase token private.

## Request a quote

Generate two independent values and retain both:

- `Idempotency-Key`: a random UUID v4 identifying this purchase request.
- `X-Purchase-Token`: 32 random bytes encoded as unpadded base64url, producing
  43 characters. This secret grants access to the purchase and its data.

Send `POST https://elevenflo.com/api/agent/v1/purchases` with those headers and
`Content-Type: application/json`. The body has exactly these fields:

```json
{
  "case_id": 42,
  "dataset": "fee-applicant-rollups",
  "offer_version": "case_dataset_snapshot_v1"
}
```

`42` is a placeholder. Use a case ID resolved through ElevenFlo case search.

A valid quote returns HTTP `402` and a standard `WWW-Authenticate: Payment`
challenge. Review the quoted purchase, total, merchant, and terms before
approving payment through your client's MPP flow.

## Pay and retrieve

Retry the same URL, body, `Idempotency-Key`, and `X-Purchase-Token` with the
MPP credential in `Authorization: Payment …`. A successful payment returns
HTTP `200`, the frozen data, and a `Payment-Receipt` header.

Retain the same request identity after a timeout. HTTP `202` means settlement
is pending. Follow `Retry-After` and query
`GET /api/agent/v1/purchases/{purchase_id}` with `X-Purchase-Token`.
Creating another purchase while the first is pending can create a separate
charge. A different payment credential cannot replace one already submitted.

You can also repeat the original POST to retrieve a paid purchase. A paid
replay does not create another charge. Reusing its idempotency key with a
changed request returns `409`.

## Response states

| HTTP status | Meaning | Next action |
| --- | --- | --- |
| `200` | Paid snapshot returned | Save the data and receipt. |
| `202` | Settlement or refund pending | Retain the purchase ID and check its status. |
| `402` | Quote awaiting payment | Review the offer and approve its MPP challenge. |
| `404` | Purchase or token not recognized | Check the retained purchase ID and token. |
| `409` | Request identity or pending credential conflict | Reuse the original request and credential. |
| `410` | Quote, retrieval period, or purchase ended | Read the returned state before creating another purchase. |
| `422` | Case data cannot satisfy the offer | Continue through case research or subscribed MCP queries. |
| `429` | Request limit reached | Wait before retrying the same request. |
| `503` | New purchases or a required service are unavailable | Retry later; retain any existing purchase ID. |

Quote requests are limited to five per minute per source IP and 100 per
minute across the service. Status reads and replays have a separate limit
of 60 per minute per source IP. Payment responses use `Cache-Control:
private, no-store` and are excluded from indexing. Public case pages and
documentation remain outside this payment flow.
