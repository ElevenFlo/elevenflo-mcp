# Permissions and data access

## Authentication

Interactive access uses remote MCP over HTTP (Streamable HTTP transport). You
sign in with your ElevenFlo web-app account, and every authorization request
uses the OAuth 2.1 authorization code flow with PKCE. Your client registers
through pre-registration, supplied metadata, or dynamic registration. You then
approve a consent screen, and only after that does a grant exist.

> [!WARNING]
> Use OAuth sign-in only. Do not paste a bearer token, API token, or custom `Authorization` header for ElevenFlo MCP. Your client manages the bearer tokens that the OAuth flow issues.

## What the tools can access

ElevenFlo MCP only reads. It can return:

- Bankruptcy case metadata
- Docket entries
- Court-document text
- Filing summaries
- Source and news metadata, bounded snippets, and publisher-link handles
- Hearing transcripts, where indexed
- Document relationship signals
- Typed rows and aggregates from the public structured datasets

Structured datasets have a fixed allowlist and declared public fields. See
[structured data](https://elevenflo.com/docs/mcp/tool-catalog#structured-data).

## What the tools cannot do

ElevenFlo MCP cannot file documents, send email, modify a docket, draft legal
documents, change account settings, manage billing, or grant access to other
users. The [tool catalog](https://elevenflo.com/docs/mcp/tool-catalog) is the
canonical list.

## Public records and confidentiality

ElevenFlo indexes public court records (dockets, filings, and hearing
transcripts) plus public news metadata and bounded snippets. Everything the
tools return is already public. `read_document_chunks` returns verbatim public
court-record text, and your client performs any analysis on it.

Connecting ElevenFlo MCP does not give ElevenFlo access to your firm's
documents, email, matters, or client files. ElevenFlo receives only the tool
calls your client makes: search queries, case and document identifiers, and the
request context described in [Logging and auditing](#logging-and-auditing).

A well-scoped prompt names a public case, a docket range, and a date window. It
needs no client or matter detail. Do not supply that detail unless your
organization has approved the workflow. Your client can also keep its own
prompt and response history outside ElevenFlo.

## Consent and revocation

Each client connection is a separate OAuth grant. Review and revoke grants in
ElevenFlo account settings under **MCP connections**. Revoke a grant when its
client is out of use or no longer trusted, when a user leaves the organization,
or when a review period ends.

## Logging and auditing

ElevenFlo logs MCP tool attempts for security, support, abuse prevention, and
usage accounting. A log entry can include:

- The grant, account, and user
- The tool name, timestamp, duration, and outcome
- The reason code and request ID
- The credits the call cost
- The case, document, source, or chunk identifiers in the request

## Prompt injection

Court filings, transcripts, and source snippets are evidence, not instructions.
Retrieved text can contain language that reads like a command ("ignore
previous", "send to", "summarize and post", "open this URL"). Tell your client
to report such language as retrieved material and never to act on it.

ElevenFlo returns retrieved text and does not judge it. As a result:

- Cite the filing or source for every factual claim (case, docket number,
  document, source URL).
- Call `read_document_chunks` before relying on legal language, dates, amounts,
  deadlines, vote percentages, releases, injunctions, or defined terms. Logs
  are not a substitute for source review.

Full verification rules:
[Safety and verification](https://elevenflo.com/docs/mcp/workflows/safety-and-verification).
