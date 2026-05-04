# Tool catalog

ElevenFlo MCP exposes read-only research tools for Chapter 11 workflows. Use them to discover cases, search dockets and filings, retrieve exact source text, analyze selected document chunks, summarize case materials, inspect filing relationships, and surface case events.

The launch capability set does not include filing, email, docket modification, account administration, billing, access management, or legal-document generation.

ElevenFlo MCP advertises 12 read-oriented native research tools today. Tools added to launch access will be listed here first.

The live MCP `tools/list` response includes read-only annotations and structured input schemas for these tools. Parameters with fixed allowed values are advertised as enums so clients can render and validate the allowed values directly.

`search` and `fetch` are also exposed for OpenAI-compatible retrieval surfaces. They are compatibility tools, not the main native catalog below. Use them when a client expects a general search/fetch profile; use the native tools when the user needs Chapter 11 workflow-specific control.

## Source grounding

Most tools return stable source identifiers — `case_watch_id`, `docket_number`, `document_id`, `chunk_id`. Pass those identifiers to `read_text` when exact wording, dates, amounts, deadlines, vote percentages, releases, injunctions, or defined terms matter.

A *chunk* is a bounded slice of a single document — typically a few paragraphs. Long filings are split into ordered chunks so you can search inside them and return exact language without loading the entire document.

## Case discovery

Find the right case and move through its docket.

### `search_cases`

- **Use when:** you need to locate a Chapter 11 case by debtor, counsel, judge, jurisdiction, or industry.
- **Required inputs:** a query (debtor name, alias, or descriptor).
- **Returns:** matching cases with `case_watch_id` and identifying metadata.
- **Next step:** `browse_docket` to scope filings, or `search_content` to search the case corpus.
- **Example:** *What is the Chapter 11 case for FTX?*

### `browse_docket`

- **Use when:** you have a case and want a filtered list of docket entries.
- **Required inputs:** `case_watch_id`; optional filters (date range, docket number, judge, jurisdiction, document type).
- **Returns:** docket entries with `docket_number`, document IDs, and entry metadata.
- **Next step:** `search_within` or `read_text` on a specific filing.
- **Example:** *List the first-day filings in this case with docket numbers.*

## Search and retrieval

Find documents and pull exact language.

### `search_content`

- **Use when:** you need filings, news/intel sources, or hearing transcripts that mention specific terms.
- **Required inputs:** a query; optional `case_watch_id`, source-type, and date filters.
- **Returns:** matching documents with `document_id` and relevance signals.
- **Next step:** `search_within` to narrow long results, then `read_text`.
- **Example:** *Which filings in this case discuss cash collateral and adequate protection?*

### `search_within`

- **Use when:** you already have a specific filing, transcript, or source item.
- **Required inputs:** `document_id` (or equivalent source ID) and a query.
- **Returns:** chunk IDs, excerpts, relevance signals, and source metadata.
- **Next step:** `read_text` for exact language or `analyze_document` for structured extraction.
- **Example:** *Within this DIP motion, which chunks cover maturity, pricing, milestones, and covenants?*

### `read_text`

- **Use when:** exact wording matters — legal language, dates, amounts, deadlines, vote percentages, releases, injunctions, defined terms.
- **Required inputs:** `document_id` plus either `chunk_id`s or a bounded character window.
- **Returns:** exact source text for the selection, with offsets.
- **Next step:** cite the result in your answer.
- **Example:** *Return the exact adequate-protection language from these chunks.*

## Analysis and summaries

Compress long filings into structured extracts and case-level synthesis.

### `analyze_document`

- **Use when:** you need a structured extract from a filing or transcript and have already selected the relevant chunks.
- **Required inputs:** `document_id` and a list of `chunk_id`s plus the extraction request.
- **Returns:** structured fields with grounding offsets back to the source chunks.
- **Next step:** `read_text` for exact wording on any extracted field.
- **Example:** *From these DIP financing chunks, extract rate, maturity, covenants, milestones, and liens.*

### `get_document_summary`

- **Use when:** you want the current summary for a single filing.
- **Required inputs:** `document_id`.
- **Returns:** the most recent summary record for the document.
- **Next step:** `read_text` if exact language matters.
- **Example:** *Summarize the confirmation order.*

### `search_summaries`

- **Use when:** you want compressed summary segments across a case.
- **Required inputs:** `case_watch_id` and a query.
- **Returns:** matching summary segments with provenance to underlying filings.
- **Next step:** `read_text` on the source filings before relying on legal language.
- **Example:** *Which filings discuss plan recoveries, settlements, releases, and the liquidation trust?*

### `build_case_context_pack`

- **Use when:** you need a case-orientation bundle for a new deal or briefing.
- **Required inputs:** `case_watch_id`; optional focus areas.
- **Returns:** a structured pack drawn from summary runs and selected segments.
- **Next step:** drill into individual filings with `read_text` or `analyze_document`.
- **Example:** *Build a context pack for this case focused on plan recoveries and confirmation issues.*

## Relationships and signals

Map how filings connect and what has happened in the case.

### `explore_document_graph`

- **Use when:** you want to see how filings reference each other.
- **Required inputs:** `case_watch_id` or `document_id`.
- **Returns:** document relationships, highly-cited filings, and motion/order threads.
- **Next step:** `read_text` or `analyze_document` on anchor filings.
- **Example:** *Which filings anchor the plan and disclosure statement in this case?*

### `search_intel_events` — Search case events and signals

- **Use when:** you want a unified feed of activity in a case.
- **Required inputs:** `case_watch_id` (or a query for cross-case search); optional date and signal-type filters.
- **Returns:** news, filings, hearings, milestones, and distress signals on a single timeline.
- **Next step:** open a referenced filing with `read_text` or `analyze_document`.
- **Example:** *What recent milestone and hearing events are in this case?*

### `lookup_case_law`

- **Use when:** you have a citation, opinion identifier, or bounded court/date and want indexed case-law metadata.
- **Required inputs:** citation, opinion ID, or court + date range.
- **Returns:** indexed case-law records with metadata and previews.
- **Scope note:** indexed metadata and previews; not a substitute for primary legal research.
- **Example:** *Look up this citation and return the opinion metadata and preview.*

## Common workflow

Most research loops run through these in order:

1. `search_cases` — find the case and its `case_watch_id`.
2. `browse_docket` or `search_content` — scope the relevant filings.
3. `search_within` — narrow long filings to the chunks that matter.
4. `read_text` or `analyze_document` — pull exact language or extract structured terms.
5. `build_case_context_pack` or `search_summaries` — widen back out for synthesis.

## Held back from launch access

`generate_bankruptcy_document` is not included in launch access. Document-generation tools require separate write/generation entitlement, audit, and support handling.
