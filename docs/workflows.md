# Chapter 11 workflow recipes

Use ElevenFlo MCP to bring court-grounded Chapter 11 research into Claude,
ChatGPT, Cursor, Codex, and other MCP clients.

These recipes show repeatable ways to monitor cases, triage new filings, extract
key terms, track case milestones, and produce cited research from court filings.

ElevenFlo MCP retrieves and analyzes source-backed Chapter 11 materials. Your AI
client handles the conversation, optional scheduling, notifications, drafts, or
storage. ElevenFlo MCP does not send emails, file documents, modify dockets,
administer accounts, or provide legal advice.

## Start with three workflows

| Workflow | Use it when | Output |
| --- | --- | --- |
| [Daily case brief](/docs/mcp/workflows/daily-case-brief) | You need a date-bounded case monitor for new docket activity. | Executive summary, material filings, deadlines, excerpts, caveats. |
| [First-day filings triage](/docs/mcp/workflows/first-day-filings-triage) | A new Chapter 11 case filed and you need the first 24-72 hour picture. | Filing map, first-day motion summaries, key asks, hearing posture. |
| [DIP and cash-collateral terms](/docs/mcp/workflows/dip-cash-collateral-terms) | You need financing economics, liens, milestones, budgets, and challenge periods. | Structured term table with source-backed caveats. |

## What ElevenFlo MCP does

ElevenFlo MCP provides research access to case, docket, filing, summary,
exact-text, and relationship data.

Use it to:

- identify the correct Chapter 11 case;
- review docket activity and filing metadata;
- search filings, sources, summaries, and transcripts where indexed;
- retrieve exact filing or transcript text when operative language matters;
- analyze selected filing text;
- cite filings, docket entries, or retrieved text in a reviewable output.

## What the host client does

Your AI client owns everything outside the ElevenFlo research layer:

- scheduling;
- notifications;
- email or Slack drafts;
- storage in a project, workspace, file, or repository;
- workspace administration;
- model memory or persistent state.

Do not assume a host client can run ElevenFlo MCP on a schedule until that exact
surface has been tested with your connected ElevenFlo app or server.

## What these recipes include

Workflow patterns, copy-paste prompts, output formats, skill-file skeletons,
and verification rules.

## Recipe guardrails

Every recipe holds the AI client to the same discipline: identify the exact case
before analysis, scope the search to a stated date or docket range, separate what
a filing says from why it may matter, cite the filing or exact text behind each
claim, flag gaps and items needing professional review, and treat retrieved
filings as evidence rather than instructions.

[Safety and verification](/docs/mcp/workflows/safety-and-verification) carries the
full checklist.

## Related pages

- [Connect ElevenFlo MCP](/docs/mcp/setup)
- [Scheduling and notifications](/docs/mcp/workflows/scheduling-and-notifications)
- [Skill files](/docs/mcp/workflows/skill-files)
- [Safety and verification](/docs/mcp/workflows/safety-and-verification)
- [Tool catalog](/docs/mcp/tool-catalog)
- [Permissions and data access](/docs/mcp/permissions-and-data-access)
