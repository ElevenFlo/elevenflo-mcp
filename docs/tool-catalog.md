# Tool catalog

The live `tools/list` response is the exact input and output contract. Inputs
reject unknown fields, errors return `{code, message, retryable?, recovery?}`,
and every list-returning tool returns bounded rows. ElevenFlo MCP
exposes read-only bankruptcy research tools; this page is the complete
list, and a tool absent here is not available. See
[Permissions and data access](https://elevenflo.com/docs/mcp/permissions-and-data-access)
for what the connector cannot do.

Cases are addressed by `case_id`, documents by UUID `document_id`. Use
identifiers exactly as returned; no aliases are accepted. When a document row
carries `has_primary_pdf: true`, open the filing at `/api/search/open/<uuid>/`
using that same UUID.

## The research loop

1. `find_cases` to resolve a `case_id`.
2. `list_docket_entries`, `search_filings`, or `find_case_document_hubs` to
   select documents.
3. `search_document_chunks` to find supporting text.
4. `read_document_chunks` or `extract_document_passages` for exact language.
5. `search_document_summaries` or `get_document_summary` for synthesis.

## Case and taxonomy

### `find_cases`

Find cases by debtor identity or by structured case metadata. Case-type tags
group cases by how ElevenFlo covers them; they do not name the bankruptcy
chapter.

`query` matches case name, case number, jurisdiction, judge, and indexed
industry and case-type tags. It does not match counsel, docket text, or filing
content; use `search_filings` for those. `case_type`, `petition_date_from`,
`petition_date_to`, and `assets_over_usd` are exact filters applied on top of
`query`, and they also work without one.

- Inputs: optional `query`, `case_type`, `petition_date_from`,
  `petition_date_to`, and `assets_over_usd`; `limit` is 1 to 50. At least one
  search criterion is required.
- Returns: `case_id`, identifying metadata, petition date, disclosed petition
  asset range, coverage state, and case-type tags. Filter-only results are
  newest first.

### `list_document_types`

List canonical filing categories and tags.

- Inputs: optional `query`.
- Returns: category keys, labels, and canonical tags.

### `list_docket_entries`

List recent documents for one case. For confirmation, emergence, or current
status, filter for `confirmation_order` and `effective_date_notice`. The latest
terminal notice controls over any projected date.

- Inputs: `case_id`, optional `limit` from 1 to 25 and `document_type_tags`.
- Returns: document rows, including `has_primary_pdf` when known.

## Cross-document search

Each of these requires every substantive query term to appear in the matching
evidence, so one common word cannot pull in unrelated results.

### `search_filings`

Search filing evidence across all cases or within one case.

- Inputs: `query`; optional `case_id`, `document_type_tags`, and `limit`.
- Returns: document rows with matched excerpts when available.

### `search_news`

Search bankruptcy news and source coverage.

- Inputs: `query`; optional `case_id` and `limit`.
- Returns: source rows with publisher metadata and snippets. Raw article text
  is not exposed.

### `search_transcripts`

Search hearing and court transcripts.

- Inputs: `query`; optional `case_id` and `limit`.
- Returns: transcript document rows.

## Summaries

### `search_document_summaries`

Search stored document summaries within one case.

- Inputs: `case_id`, `query`, optional `limit`.
- Returns: summary rows and their document IDs.

### `get_document_summary`

Read the stored summary for one document.

- Input: `document_id`.
- Returns: one summary or a typed not-found error.

## Exact text

### `search_document_chunks`

Find supporting chunks within selected documents.

- Inputs: one to 25 `document_ids` and `query`.
- Returns: matching chunk IDs, exact text, and offsets grouped by document.

### `read_document_chunks`

Read selected chunks from one document.

- Inputs: `document_id` and one to eight `chunk_ids`.
- Returns: exact chunk text and offsets.

### `extract_document_passages`

Extract passages answering a focused question from one document.

- Inputs: `document_id`, `query`, optional `max_chunks` from 1 to 10.
- Returns: selected exact chunks and offsets.

## Document relationships

### `find_case_document_hubs`

Find the most connected documents in a case citation graph.

- Inputs: `case_id`, optional `limit` from 1 to 25.
- Returns: document rows with citation counts.

### `list_document_relationships`

List citation relationships for selected documents.

- Inputs: one to 25 `document_ids`, optional `direction`, and optional `limit`.
- Returns: source and related document pairs.

## Structured data

Structured datasets carry typed, source-linked fields extracted from filings.
Use them when the question is about typed fields, comparable records, or a
population rather than filing text.

Call `describe_structured_dataset` before querying an unfamiliar dataset: it
declares the filters, sorts, facets, and metrics that dataset accepts.
Responses return only public fields and operations, never extraction
diagnostics, confidence scores, computation timestamps, raw blobs, or storage
identifiers.

### `list_structured_datasets`

List the datasets available to the caller.

- Inputs: optional `limit` and signed `cursor`.
- Returns: dataset slugs, grain, freshness, coverage window, available
  operations, and provenance.

### `describe_structured_dataset`

Describe one dataset before querying it.

- Input: `dataset`, using a slug returned by `list_structured_datasets`.
- Returns: public fields and meanings, typed filter operators, selectable
  columns, sorts, facets, aggregate capabilities, and examples.

### `search_structured_data`

Return typed rows from one dataset.

- Inputs: `dataset`; optional typed `where`, `select`, `sort`, `page_size`,
  `cursor`, and requested `facets` as declared by the dataset description.
- Returns: public rows, signed continuation cursor, requested facets, snapshot
  freshness, coverage window, and provenance.

### `aggregate_structured_data`

Answer population questions over one dataset.

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

`ballot-tabulation`, `bar-dates`, `case-disclosure-solicitation`,
`case-outcomes`, `committee-appointments`, `exclusivity-periods`,
`fee-applicant-rollups`, `hearings-case-rollup`, `keip-kerp`,
`post-confirmation-reports`, `rule-2019-statements`, `sale-process`,
`schedule-summary-totals`, `transfers-of-claim`, `voluntary-petitions`.

This allowlist is narrower than the set ElevenFlo maintains. Monthly operating
reports and fee timekeepers, among others, are not exposed, and naming one
returns an `unknown_dataset` error. `list_structured_datasets` is the live,
request-scoped catalog.

## Credits

| Credits | Tools |
| --- | --- |
| 1 | `find_cases`, `list_document_types`, `list_docket_entries`, `list_structured_datasets`, `describe_structured_dataset`, `suggest_structured_values` |
| 2 | `search_filings`, `search_news`, `search_transcripts`, `search_structured_data` |
| 3 | `search_document_chunks`, `aggregate_structured_data` |
| 5 | `search_document_summaries`, `get_document_summary`, `read_document_chunks`, `extract_document_passages`, `find_case_document_hubs`, `list_document_relationships` |

Free accounts include 500 credits a month and Pro includes 100,000, resetting
at the start of each calendar month. Failed calls are not charged. Current
usage appears on your account page under **MCP connections**.
