# Access

ElevenFlo MCP is available on every plan. Free accounts include a monthly
credit allowance, and Pro includes a much larger one. Access is provisioned
automatically — there is no separate access request.

Use it to connect Claude, ChatGPT, Cursor, Codex, VS Code, or another
MCP-compatible client to ElevenFlo's Chapter 11 and restructuring research.

## Who it's for

ElevenFlo MCP is built for the people who work in Chapter 11 dockets every day:

- restructuring and turnaround lawyers
- financial advisors and investment bankers
- distressed-debt and claims-trading analysts
- journalists and market researchers covering active cases

## What you can do

- Find the right Chapter 11 case.
- Search dockets and court filings.
- Retrieve exact filing language.
- Analyze motions, orders, plans, and disclosure statements.
- Build case briefs and research notes grounded in primary court sources.
- Research docket activity, hearing context, filed-material milestones, and indexed source metadata/snippets.

## What you need

- an ElevenFlo account with a verified email address (any plan)
- a supported MCP client
- browser access for ElevenFlo sign-in and OAuth consent

## Credits

Each MCP tool call costs a fixed number of credits. Free accounts include
2,500 credits per month and Pro includes 100,000, resetting at the start of
each calendar month.

Most tools cost 1–3 credits. `explore_document_graph` costs 5.
`analyze_document`, which runs AI analysis over filing or transcript text,
costs 15.

In practice:

- Identifying a case and scanning its docket runs about 5–10 credits.
- A focused research session — locating filings, searching within them,
  running several deep analyses — typically runs 50–200 credits.
- The Pro allowance covers several hundred sessions at that rate; the free
  allowance covers roughly a dozen.

Failed tool calls are never charged. Current usage appears on your account
page under **MCP connections**.

## Endpoint

```text
https://elevenflo.com/mcp
```

## Connect

1. Confirm your ElevenFlo email address is verified.
2. Add the endpoint above in your client — see the [Quickstart](quickstart.md).
3. Sign in with your ElevenFlo account and approve OAuth consent.
