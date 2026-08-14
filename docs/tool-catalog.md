# Tool catalog

ElevenFlo MCP exposes 13 read-only Chapter 11 research tools. The live
`tools/list` response is the exact input and output contract. Inputs reject
unknown fields; tool errors use `{code, message, retryable?, recovery?}`.

## Recommended research loop

1. `find_cases` to resolve a `case_id`.
2. `list_docket_entries`, `search_filings`, or `find_case_document_hubs` to
   select documents.
3. `search_document_chunks` to find supporting text.
4. `read_document_chunks` or `extract_document_passages` for exact language.
5. `search_document_summaries` or `get_document_summary` for synthesis.

Public MCP uses `case_id` for cases and UUID `document_id` values for document
tools. Use identifiers exactly as returned; no compatibility aliases are
accepted.

## Case and taxonomy

### `find_cases`

Find cases by debtor name, case number, or related identifier.

- Inputs: `query`, optional `limit` from 1 to 25.
- Returns: bounded case rows with `case_id` and identifying metadata.

### `list_document_types`

List canonical filing categories and tags.

- Inputs: optional `query`.
- Returns: category keys, labels, and canonical tags.

### `list_docket_entries`

List recent documents for one case.

- Inputs: `case_id`, optional `limit` from 1 to 25 and
  `document_type_tags`.
- Returns: bounded document rows.

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

## Boundaries

The public MCP cannot write data, file documents, send messages, manage
accounts, expose internal operating tools, or return raw publisher article
text. See [Permissions and data access](/docs/mcp/permissions-and-data-access).
