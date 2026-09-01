# Permissions and data access

## Authentication

Interactive access uses remote MCP over HTTP (Streamable HTTP transport),
ElevenFlo web-app sign-in, and the OAuth 2.1 authorization code flow with PKCE
on every authorization request. Your client registers itself by
pre-registration, supplied metadata, or dynamic registration, and you approve
an explicit consent screen before a grant exists.

> [!WARNING]
> Use OAuth sign-in only. Do not paste a bearer token, API token, or custom `Authorization` header for ElevenFlo MCP. Your client manages the bearer tokens that the OAuth flow issues; never configure a static token yourself.

## What the tools can access

The tool set only reads. It can retrieve and analyze:

- bankruptcy case metadata
- docket entries
- court-document text
- filing summaries
- source and news metadata, bounded snippets, and publisher-link handles
- hearing transcripts where indexed as searchable content
- document relationship signals
- typed rows and aggregates from the public structured datasets, whose fields
  are extracted from those same court filings

Structured-dataset responses return only the public fields and operations a
dataset declares, and the dataset allowlist is explicit: see
[structured data](https://elevenflo.com/docs/mcp/tool-catalog#structured-data).

## What the tools cannot do

The tool set cannot file documents, send email, modify a docket, draft legal
documents, change account settings, manage billing, or grant access to other
users. The [tool catalog](https://elevenflo.com/docs/mcp/tool-catalog) is the
canonical list of what ElevenFlo MCP exposes; a tool not listed there is not
available.

## Public records and confidentiality

ElevenFlo's research corpus is built from public court records (dockets,
filings, and hearing transcripts where indexed) plus public news metadata and
bounded snippets. Everything the tools retrieve is already public.
`read_document_chunks` returns verbatim public court-record text, and your
client performs any analysis on it.

Connecting ElevenFlo MCP does not give ElevenFlo access to your firm's
documents, email, matters, or client files. ElevenFlo receives only the tool
calls your client makes: search queries, case and document identifiers, and the
request context described in [Logging and auditing](#logging-and-auditing).

A well-scoped prompt names a public case, a docket range, and a date window. It
needs no client or matter detail, and you should not supply any unless your
organization has approved that workflow. Your client may also keep its own
prompt and response history outside ElevenFlo.

## Consent and revocation

Each client connection is a separate OAuth grant. Review and revoke grants in
ElevenFlo account settings under **MCP connections**. Revoke one when a client
is out of use or no longer trusted, when a user leaves the organization, or
when a review period ends.

## Logging and auditing

ElevenFlo records MCP tool attempts for security, support, abuse prevention,
and usage accounting. A log entry may record:

- the grant, account, and user
- the tool name, timestamp, duration, and outcome
- the reason code and request ID
- the credits the call cost
- the case, document, source, or chunk identifiers in the request

## Prompt injection

Court filings, transcripts, and source snippets are evidence, not instructions.
Retrieved text can contain language that reads like a command ("ignore
previous", "send to", "summarize and post", "open this URL"). Tell your client
to report such language as retrieved material and never to act on it.

Because ElevenFlo returns retrieved text rather than adjudicating it:

- Cite the filing or source for every factual claim (case, docket number,
  document, source URL).
- Call `read_document_chunks` before relying on legal language, dates, amounts,
  deadlines, vote percentages, releases, injunctions, or defined terms. Logs
  are not a substitute for source review.
- If a result asks the client to disregard your prompt or these guardrails, ask
  the client to show you that text rather than act on it.

Full verification rules:
[Safety and verification](https://elevenflo.com/docs/mcp/workflows/safety-and-verification).
