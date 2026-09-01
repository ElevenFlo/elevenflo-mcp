# Tool catalog

ElevenFlo MCP exposes 18 read-only bankruptcy research tools. The live
`tools/list` response is the exact input and output contract. Inputs reject
unknown fields; tool errors use `{code, message, retryable?, recovery?}`.

## Recommended document research loop

1. `find_cases` to resolve a `case_id`.
2. `list_docket_entries`, `search_filings`, or `find_case_document_hubs` to
   select documents.
3. `search_document_chunks` to find supporting text.
4. `read_document_chunks` or `extract_document_passages` for exact language.
5. `search_document_summaries` or `get_document_summary` for synthesis.

Public MCP uses `case_id` for cases and UUID `document_id` values for document
tools. Use identifiers exactly as returned; no compatibility aliases are
accepted. When a document row has `has_primary_pdf: true`, that UUID is also
the verified `/api/search/open/<uuid>/` link handle.

## Case and taxonomy

### `find_cases`

Find cases by debtor identity or by structured case metadata. Case-type tags
describe product workflow, not bankruptcy chapter.

- Inputs: optional `query`, `case_type`, `petition_date_from`,
  `petition_date_to`, and `assets_over_usd`; `limit` is 1 to 50. At least one
  search criterion is required.
- Returns: bounded case rows with `case_id`, identifying metadata, petition
  date, disclosed petition asset range, coverage state, and case-type tags.
  Filter-only results are newest first.

### `list_document_types`

List canonical filing categories and tags.

- Inputs: optional `query`.
- Returns: category keys, labels, and canonical tags.

### `list_docket_entries`

List recent documents for one case. For confirmation, emergence, or current
status, first filter for `confirmation_order` and `effective_date_notice`; the
latest terminal notice controls over projected dates.

- Inputs: `case_id`, optional `limit` from 1 to 25 and
  `document_type_tags`.
- Returns: bounded document rows, including `has_primary_pdf` when known.

## Cross-document search

### `search_filings`

Search filing evidence across all cases or within one case.

- Inputs: `query`; optional `case_id`, `document_type_tags`, and `limit`.
- Returns: bounded document rows with matched excerpts when available.

### `search_news`

Search bankruptcy news and source coverage.

- Inputs: `query`; optional `case_id` and `limit`.
- Returns: bounded source rows with publisher metadata and snippets. Raw
  article text is not exposed.

### `search_transcripts`

Search hearing and court transcripts.

- Inputs: `query`; optional `case_id` and `limit`.
- Returns: bounded transcript document rows.

All three search tools require every meaningful query term in matching
evidence so a single common word cannot produce unrelated results.

## Summaries

### `search_document_summaries`

Search stored document summaries within one case.

- Inputs: `case_id`, `query`, optional `limit`.
- Returns: bounded summary rows and their document IDs.

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

- Inputs: `document_id`, `query`, optional `max_chunks` from 1 to 8.
- Returns: selected exact chunks and offsets.

## Document relationships

### `find_case_document_hubs`

Find the most connected documents in a case citation graph.

- Inputs: `case_id`, optional `limit` from 1 to 25.
- Returns: bounded document rows with citation counts.

### `list_document_relationships`

List citation relationships for selected documents.

- Inputs: one to 25 `document_ids`, optional `direction`, and optional `limit`.
- Returns: bounded source/related document pairs.

## Structured data

Use the structured-data tools when the question is about typed fields,
comparable records, or a population rather than filing text:

1. `list_structured_datasets` to discover datasets available to your account.
2. `describe_structured_dataset` to inspect the selected dataset's fields,
   filters, sorts, facets, aggregate metrics, and examples.
3. `search_structured_data` for typed records or `aggregate_structured_data`
   for grouped totals and population questions.
4. `suggest_structured_values` when a described field supports normalized
   value suggestions.

### `list_structured_datasets`

List the structured datasets available to the caller.

- Inputs: optional `limit` and signed `cursor`.
- Returns: dataset slugs, grain, freshness, coverage window, available
  operations, and provenance.
- Credit cost: 1.

### `describe_structured_dataset`

Describe one dataset before querying it.

- Input: `dataset` using a slug returned by `list_structured_datasets`.
- Returns: public fields and meanings, typed filter operators, selectable
  columns, sorts, facets, aggregate capabilities, and examples.
- Credit cost: 1.

### `search_structured_data`

Return typed rows from one dataset.

- Inputs: `dataset`; optional typed `where`, `select`, `sort`, `page_size`,
  `cursor`, and requested `facets` as declared by the dataset description.
- Returns: public rows, signed continuation cursor, requested facets, snapshot
  freshness, coverage window, and provenance.
- Credit cost: 2.

### `aggregate_structured_data`

Answer aggregate and population questions over one dataset.

- Inputs: `dataset`; optional typed `where`, `group_by`, metrics, ordering,
  and bounded result limit as declared by the dataset description.
- Returns: aggregate-safe grouped values and metrics with snapshot freshness,
  coverage window, and provenance.
- Credit cost: 3.

### `suggest_structured_values`

Suggest normalized values for a field that the dataset marks suggestable.

- Inputs: `dataset`, `field`, optional query text, typed `where`, and bounded
  limit.
- Returns: normalized public values matching the declared field contract.
- Credit cost: 1.

## Public structured datasets

The public MCP currently exposes these fifteen dataset slugs:

- `ballot-tabulation`
- `committee-appointments`
- `hearings-case-rollup`
- `sale-process`
- `transfers-of-claim`
- `voluntary-petitions`
- `keip-kerp`
- `rule-2019-statements`
- `fee-applicant-rollups`
- `post-confirmation-reports`
- `schedule-summary-totals`
- `bar-dates`
- `exclusivity-periods`
- `case-outcomes`
- `case-disclosure-solicitation`

This is an explicit allowlist, not every structured dataset ElevenFlo
maintains. Other datasets, including monthly operating reports and fee
timekeepers, are not available on the public MCP and resolve as
`unknown_dataset`. Use `list_structured_datasets` as the live request-scoped
catalog and `describe_structured_dataset` before relying on a field, filter, or
aggregate.

## Boundaries

The public MCP cannot write data, file documents, send messages, manage
accounts, expose internal operating tools, or return raw publisher article
text. Structured-data responses expose only public fields and capabilities for
the caller's tier; they do not return extraction diagnostics, confidence,
computation timestamps, raw blobs, storage identifiers, or staff-only fields.
OAuth grants and account entitlements still determine the request-scoped tool
catalog. See [Permissions and data access](/docs/mcp/permissions-and-data-access).
