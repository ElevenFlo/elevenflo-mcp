# Access

ElevenFlo MCP is included with Pro and provisioned automatically when you
upgrade.

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

- an ElevenFlo Pro account
- a supported MCP client
- browser access for ElevenFlo sign-in and OAuth consent

## Credits

Each MCP tool call costs a fixed number of credits. Pro includes 100,000
credits per month, resetting at the start of each calendar month.

Most tools cost 1–3 credits. `explore_document_graph` costs 5.
`analyze_document`, which runs AI analysis over filing or transcript text,
costs 15.

In practice:

- Identifying a case and scanning its docket runs about 5–10 credits.
- A focused research session — locating filings, searching within them,
  running several deep analyses — typically runs 50–200 credits.
- The monthly allowance covers several hundred sessions at that rate.

Failed tool calls are never charged. Current usage appears on your account
page under **AI connections**.

## Endpoint

```text
https://elevenflo.com/mcp
```

## Connect

1. Confirm you are on ElevenFlo Pro.
2. Add the endpoint above in your client — see the [Quickstart](quickstart.md).
3. Sign in with your ElevenFlo account and approve OAuth consent.
