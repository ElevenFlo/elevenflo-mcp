# Tool catalog

The live `tools/list` response is the exact input and output contract. Inputs
reject unknown fields, errors return `{code, message, retryable, recovery?, details?}`,
and public TextContent is the JSON copy of the same `structuredContent` payload.
Every list-returning tool returns bounded rows. ElevenFlo MCP
exposes 18 read-only bankruptcy research tools. This page is the complete list,
and a tool absent here is not available. See
[Permissions and data access](https://elevenflo.com/docs/mcp/permissions-and-data-access)
for what ElevenFlo MCP cannot do.

Cases are addressed by `case_id`, documents by UUID `document_id`. Use
identifiers exactly as returned. ElevenFlo MCP accepts no aliases. When a
document row carries `has_primary_pdf: true`, open the filing at
`/api/search/open/<uuid>/` using that same UUID.

## The research loop

1. `find_cases` to resolve a `case_id`.
2. `list_docket_entries`, `search_filings`, or `find_key_documents` to
   select documents.
3. `search_document_chunks` to find supporting text.
4. `read_document_chunks` or `extract_document_passages` for exact language.
5. `search_document_summaries` or `get_document_summary` for synthesis.

## Case and taxonomy

### `find_cases`

Find cases by debtor identity or by structured case metadata. Case-type tags
group cases by how ElevenFlo covers them. They do not name the bankruptcy
chapter.

`query` matches case name, case number, jurisdiction, judge, and indexed
industry and case-type tags. It does not match counsel, docket text, or filing
content. Use `search_filings` for those. The other filters listed below are
exact, apply on top of `query`, and also work without one.

- Inputs: optional `query`, `case_type`, `petition_date_from`,
  `petition_date_to`, and `assets_over_usd`. `limit` is 1 to 50. At least one
  search criterion is required. Dates must be real `YYYY-MM-DD` calendar dates,
  and the from date cannot follow the to date. Use exact `case_type_tags` values
  returned by this tool; unknown values return a typed error.
- Returns: `case_id`, identifying metadata, petition date, disclosed petition
  asset and liabilities ranges, coverage state, and case-type tags.
  Filter-only results are newest first.

### `list_document_types`

List filing categories and tags.

- Inputs: optional `query`.
- Returns: category keys, labels, and canonical tags.

### `list_docket_entries`

List observed docket entries for one case, including full entry text, docket
evidence, court citations, and links to held documents. Entries can exist
without a PDF or searchable filing text. For current case status, verify the
relevant terminal notice rather than relying on a projected date.

- Inputs: `case_id`; optional `limit` from 1 to 25, `query`,
  `document_type_tags`, inclusive `date_filed_from`/`date_filed_to` dates, up to
  25 positive `docket_numbers`, and an opaque `cursor` from the preceding page.
  A cursor is valid only with the same filters. Dates describe filing dates,
  not independently verified entry or signature dates.
- Text search: `query` matches a literal case-insensitive substring. When a
  date or docket number is known, start with those filters and omit `query`
  and type filters. If needed, use one distinctive word or an exact phrase.
- Filters: `document_type_tags` accepts only category keys and canonical tags
  returned by `list_document_types`; unknown values return a typed error.
- Returns: version 2 `entries`, evidence, `has_more`, and `next_cursor` (null
  when exhausted); PDF/text flags describe held artifacts. Every held document
  with a public UUID appears once, inside its entry, and includes a `filing_url`
  that opens the filing.
- Court locator: each entry and document can carry `court_locator`: the court
  `citation` (`Bankr. D. Del., No. 22-11068, ECF No. 512`; adversary and
  claim-register forms use `Adv. No.` and `Claim No.`), `court`, `case_number`,
  `docket_number` or `claim_number`, `case_docket_url`, and an optional `pacer`
  link whose `target` is `case`, `entry`, or `document`. It says where the filing
  sits on the court's docket, not where ElevenFlo's copy came from. A PACER link
  opens with the reader's own PACER login; PACER fees may apply. Document,
  summary, chunk, hub, and relationship results carry the same object.

## Case updates

### `list_case_updates`

List typed docket events, hearings, and milestones for named cases or the
connected account's saved tracked cases. This tool reads updates; it cannot
create or change tracked cases, schedule checks, or send notifications.

- Inputs: either one to 25 `case_ids` or `watched=true`, never both. Resolve a
  named case with `find_cases` first. Optional `since` is an ISO-8601 timestamp
  within the last 90 days; it defaults to seven days ago. Optional `triggers`
  filters event types. `limit` is 1 to 50. Optional `cursor` continues a
  previous page.
- Returns: resolved `case_ids`, newest-first `updates`, filing citations, and
  any published narrative. `status` distinguishes `updates_available`,
  `no_watched_cases`, `no_matching_triggers`, and `no_recent_updates`.
  `has_more` and `next_cursor` continue after the last returned event; the
  cursor is bound to the same case scope, triggers, and `since` window, so
  pass it with the same scope and triggers and omit `since`.
- Limits: no recent typed updates does not establish that no filings occurred.
  Continue with `list_docket_entries` for the returned case IDs and requested
  filing-date window. Detection time and the event's occurrence date can differ.

## Cross-document search

These tools require every substantive query term to appear in the matching
evidence. One common word cannot pull in unrelated results.

### `search_filings`

Search filing evidence across all cases or within one case.

- Inputs: `query`; optional `case_id`, `document_type_tags`, and `limit`.
- Returns: document rows with matched excerpts and a `filing_url` when available.
  `document_type_tags` accepts only values returned by `list_document_types`.

### `search_transcripts`

Search hearing and court transcripts.

- Inputs: `query`; optional `case_id` and `limit`.
- Returns: transcript document rows.

## Summaries

### `search_document_summaries`

Search stored document summaries within one case.

- Inputs: `case_id`, `query`, optional `limit`.
- Returns: summary rows, their document IDs, and a `filing_url` that opens each
  filing.

### `get_document_summary`

Read the stored summary for one document.

- Input: `document_id`.
- Returns: one summary or a typed not-found error whose `missing_document_ids`
  identifies the requested UUID. The summary and each related filing include a
  `filing_url` that opens the filing.

## Exact text

### `search_document_chunks`

Find supporting chunks within selected documents.

- Inputs: one to 25 `document_ids` and `query`.
- Returns: matching chunk IDs, 1-based page numbers (0 when unavailable), exact
  text, and offsets grouped by document. `missing_document_ids` lists requested
  UUIDs that were not found or were not public.

### `read_document_chunks`

Read selected chunks from one document.

- Inputs: `document_id` and one to eight `chunk_ids`.
- Returns: available chunk text, page numbers when available, offsets,
  `total_chunks`, and `missing_chunk_ids` (empty when every requested chunk exists).
  Valid IDs range from 0 to `total_chunks - 1`; an all-missing request returns
  `chunk_not_found` with the missing IDs and range.

### `extract_document_passages`

Extract passages answering a focused question from one document.

- Inputs: `document_id`, `query`, optional `max_chunks` from 1 to 10.
- Returns: selected exact chunks and offsets, or a typed not-found error that
  identifies the requested UUID.

## Document relationships

### `find_key_documents`

Find key filings in a case, ranked by incoming citation count. Citation frequency
is a starting point for research, not a judgment of legal importance.

- Inputs: `case_id`, optional `limit` from 1 to 25.
- Returns: document rows with citation counts and a `filing_url` that opens each
  filing.

### `get_related_documents`

Find filings that cite, or are cited by, selected documents.

- Inputs: one to 25 `document_ids`, optional `direction`, and optional `limit`.
- Returns: source and related document pairs. Each related filing includes its
  `filing_url` and a `summary_state` of `available` or `unavailable`.
  `missing_document_ids` lists requested UUIDs that were not found or were not
  public; `omitted_without_public_id` continues to count related rows omitted
  because no public UUID could be resolved.

## Structured data

Structured datasets carry typed, source-linked fields extracted from filings.
Use them when the question is about typed fields, comparable records, or many
cases at once rather than the text of one filing. Responses return only public
fields and operations, never internal extraction metadata.

Free accounts can list datasets and describe their fields and examples.
Searches, aggregates and value suggestions require ElevenFlo Pro.

### `list_structured_datasets`

List the datasets available to you.

- Inputs: optional `limit` and signed `cursor`.
- Returns: dataset slugs, grain, freshness, coverage window, available
  operations, and provenance.

### `describe_structured_dataset`

Call this before you query an unfamiliar dataset.

- Input: `dataset`, using a slug returned by `list_structured_datasets`.
- Returns: public fields and meanings, typed filter operators, selectable
  columns, sorts, facets, aggregate capabilities, examples, and `search_limits`
  (`max_sort_clauses`, `max_page_size`).
- DIP filters use `stage` and `financing_structure`; `stages` and
  `financing_structures` are declared aliases. Sale-process uses
  `latest_source_date_filed` and fee-applicant-rollups uses `latest_date_filed`;
  both retain `date_filed` as a declared alias. Calendar-date ranges support
  `eq`, `gt`, `lt`, `gte`, `lte`, and `between`; numeric ranges support
  `gte`, `lte`, and `between`.

### `search_structured_data`

Return typed rows from one dataset.

- Inputs: `dataset`; optional typed `where`, `select`, `sort`, `page_size`,
  `cursor`, and requested `facets` as declared by the dataset description.
- Returns: public rows, signed continuation cursor, requested facets, snapshot
  freshness, coverage window, provenance, and the effective `page_size`.
  Wide projections clamp page size to the select budget.

### `aggregate_structured_data`

Count and summarize records across one dataset.

- Inputs: `dataset`; optional typed `where`, `group_by`, metrics, ordering, and
  bounded result limit as declared by the dataset description.
- Returns: aggregate-safe grouped values and metrics with snapshot freshness,
  coverage window, and provenance.

### `suggest_structured_values`

Suggest normalized values for a field the dataset marks suggestable.

- Inputs: `dataset`, `field`, optional query text, typed `where`, and bounded
  limit.
- Returns: normalized public values matching the declared field contract.

### Available datasets

- `ballot-tabulation`
- `ballot-class-results`
- `bar-dates`
- `dip-financing`
- `dip-approval-history`
- `case-disclosure-solicitation`
- `case-contract-treatment-events`
- `hearing-sessions`
- `monthly-operating-reports`
- `case-outcomes`
- `committee-appointments`
- `exclusivity-periods`
- `fee-applicant-rollups` — Cumulative requested and allowed fee economics by applicant, case, and currency; group by `firm_name` for requested fees by firm. Requested fees are not court-allowed fees. Affiliates appear separately without parent-firm consolidation, and roles may be unknown. The public `requested_amount_rejected` boolean indicates that requested economics exclude rejected filings; surviving requests in the same rollup still contribute. Monetary sorts require `currency_code` with `eq`; the default sort is latest filing date. `sources` identifies contributors separately for each monetary field, with availability, counts and truncation. Null provenance awaits reprojection. Order-date filters select applicants without rebasing their lifetime totals to the selected month.
- `fee-timekeepers`: named historical billing rates by firm and case. Filter
  `timekeeper_name` with name tokens in any order, `organization_name`,
  `timekeeper_title`, or `hourly_rate`. Group partner rates by firm, filing year,
  and currency; aggregates default to monthly, final, and first-and-final
  applications. Rates are observed billing evidence, not current market rates.
- `fee-decisions`
- `hearings-case-rollup`
- `keip-kerp`
- `post-confirmation-reports`
- `rule-2019-statements`
- `sale-process`
- `schedule-summary-totals`
- `top-unsecured-creditors`
- `executory-contracts`
- `schedule-ab-assets`
- `schedule-ab-real-property`
- `codebtors`
- `transfers-of-claim`
- `voluntary-petitions`

This page is a static copy of the public catalog. Call
`list_structured_datasets` for the live list and `describe_structured_dataset`
for each dataset's fields, operations, and limits. Datasets outside the public
catalog return `unknown_dataset`.

`hearing-sessions` uses schema `hearing-sessions.v2`. `scope_kind` distinguishes
bankruptcy-case, single-adversary, joint-adversary and unresolved ownership.
`adversary_proceedings` contains canonical court numbers, captions and available
tracking URLs; `scope_sources` contains immutable public-filing/hash references
for qualified joint conferences. Unresolved scope does not assert case ownership.

`ballot-tabulation` is **Plan voting results**. It returns one current ballot
declaration with projected class totals. Its operative flag is true only for
the latest non-supplemental declaration for the case and plan. `ballot-class-results`
returns immutable class-row evidence from those declarations. These class rows
are not canonical plan votes.

## Credits

Research credits measure returned data. Each successful response costs the largest
of 1 credit, returned records divided by 10 (rounded up), or canonical response
bytes divided by 10,000 (rounded up). Protocol duplicates are counted only once.
A 100-row response of 20 KB costs 10 credits; a 45 KB text response costs 5.
Failed calls are not charged. Successful empty responses cost 1 credit.

Free includes 200 research credits per calendar month and 100 per rolling 24 hours.
Pro includes 10,000 per calendar month and 2,000 per rolling 24 hours. All clients
share the account balance and its 120-request-per-minute limit. Monthly balances
reset at 00:00 UTC on the first of the month; rolling capacity recovers as usage
ages past 24 hours. A result that exceeds remaining capacity is withheld and not
charged. Narrow the request, retry after the stated time, or upgrade for a larger
monthly allowance. See current balances on your account page.

Website document access has a separate rolling-day budget of 75 distinct
documents on Pro. Free accounts keep their monthly download allowance instead.
Reopening the same document and PDF range requests do not consume additional
rolling-day slots. Publicly published blog documents remain public.
