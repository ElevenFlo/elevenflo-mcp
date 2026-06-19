# Tool catalog

ElevenFlo MCP exposes read-only research tools for Chapter 11 workflows. Use them to discover cases, search dockets and filings, retrieve exact court-document text, discover source/news metadata and snippets, analyze selected document chunks, summarize case materials, and inspect filing relationships.

These tools cannot file documents, send email, modify dockets, administer accounts, manage billing or access, or generate legal documents.

If you are following a workflow recipe, you usually do not need to call these
tools by name. The AI client selects them to follow the recipe, cite the record,
and retrieve exact text when it matters.

To combine these tools into repeatable, cited research, start with the
[Chapter 11 workflow recipes](/docs/mcp/workflows).

The live MCP `tools/list` response includes read-only annotations and structured input schemas; parameters with fixed allowed values are advertised as enums.

`search` and `fetch` are also exposed for OpenAI-compatible retrieval surfaces. They are compatibility tools, not part of the native catalog below: use them when a client expects a general search/fetch profile, and the native tools when you need Chapter 11-specific control. In that profile, `search` returns filing IDs, titles, and canonical ElevenFlo links; `fetch` returns the filing text for a selected `id` (a handle such as `filing:<document_id>`).

## Common research loop

Most source-backed research follows the same path:

1. `search_cases` — find the case and its `case_id`.
2. `browse_docket` or `search_content` — scope the relevant filings.
3. `search_within` — narrow long filings to the chunks that matter.
4. `read_text` or `analyze_document` — quote exact language or extract structured terms.
5. `search_summaries` — widen back out for synthesis where summary coverage exists.

Cite the filing, docket entry, or retrieved text behind every output.

## Source grounding

Tools return stable source identifiers such as `case_id`, `case_watch_id`, `docket_number`, `activity_id`, `document_id`, `source_content_id`, and `chunk_id`. `case_id` is the preferred public case identifier. ElevenFlo also returns `case_watch_id`, the legacy name for the same value; use either identifier in follow-up MCP calls. Use `document_id` with MCP tools such as `search_within`, `read_text`, `analyze_document`, and `get_document_summary` only when `document_search_available` is true. Some docket entries are notice-only (ingested from court RSS feeds) and have no searchable filing text; these RSS-only entries have an `activity_id` and may have an `rss_docket_entry_id`. Use `source_content_id` only as a source/news citation handle that resolves to publisher metadata and links. Use `document_public_id` only for browser-facing filing links such as `/api/search/open/<document_public_id>/`. Pass document tool identifiers to `read_text` when exact wording, dates, amounts, deadlines, vote percentages, releases, injunctions, or defined terms matter.

Document-bearing native tools support `response_mode`:

| Mode | Use when | Citation fields |
| --- | --- | --- |
| `compact` | discovering and ranking filings | rows include `citation_ref` when available, but omit repeated UUIDs/URLs |
| `citations` | preparing a sourced answer or opening filings | rows keep `citation_ref`; top-level `citation_map` expands refs to canonical filing links |
| `full` | debugging or support inspection | rows include full citation envelopes and existing metadata |

`citation_ref` values such as `d1` are local to one tool response. They are not browser IDs, URL prefixes, or values to persist. When you need a user-visible filing link, request `response_mode="citations"` or `response_mode="full"` and use the returned `citation_map` URL.

Compact example:

```json
{
  "response_mode": "compact",
  "entries": [
    {
      "document_id": 301,
      "citation_ref": "d1",
      "document_name": "DIP Motion",
      "docket_number": 43
    }
  ]
}
```

Citation-ready example:

```json
{
  "response_mode": "citations",
  "entries": [
    {
      "document_id": 301,
      "citation_ref": "d1",
      "document_name": "DIP Motion",
      "docket_number": 43
    }
  ],
  "citation_map": {
    "d1": {
      "document_id": 301,
      "document_public_id": "11111111-2222-4333-8444-555555555555",
      "url": "https://elevenflo.com/api/search/open/11111111-2222-4333-8444-555555555555/",
      "label": "DIP Motion [Dkt. 43]"
    }
  }
}
```

A *chunk* is a bounded slice of a single document — typically a few paragraphs. Long filings are split into ordered chunks so you can search inside them and return exact language without loading the entire document.

## Case discovery

Find the right case and move through its docket.

### `search_cases`

- **Use when:** you need to locate a Chapter 11 case by debtor, counsel, judge, jurisdiction, or industry.
- **Required inputs:** a query (debtor name, alias, or descriptor).
- **Returns:** matching cases with `case_id`, `case_watch_id`, identifying metadata, coverage state, and downstream availability fields.
- **Next step:** `browse_docket` to scope filings, or `search_content` to search the case corpus.
- **Example:** *What is the Chapter 11 case for FTX?*

### `browse_docket`

- **Use when:** you have a case and want a filtered list of docket entries.
- **Required inputs:** `case_id` or `case_watch_id`; optional filters (date range, docket number, judge, jurisdiction, document type).
- **Returns:** docket activity entries. Document-backed entries include `document_id` for tool calls, optional compact `citation_ref`, and, in citation-bearing modes, `citation_map` browser filing links. Document-backed entries also carry `has_primary_pdf`; when it is `false` the filing has no downloadable PDF yet (e.g. some hearing transcripts or very fresh filings), so cite it without an `/api/search/open/<document_public_id>/` link. The entry is still returned and stays searchable when `document_search_available=true` — the flag only gates the open link, never whether the row appears or whether you can `read_text`/`analyze_document` it. RSS-only entries include activity/RSS identifiers and `document_search_available=false`; they are likewise surfaced as notice-only docket activity, just without a filing link.
- **Next step:** `search_within` or `read_text` on a specific filing only when an entry has `document_search_available=true`.
- **Example:** *List the first-day filings in this case with docket numbers.*

To filter by type, pass `browse_docket(document_type_tags=[...])` with either
canonical tags (such as `chapter_11_plan`) or category keys from
`list_document_type_filters`. The filter uses OR semantics, returns matching
entries with their canonical tags, and runs the same docket path as unfiltered
browsing.

### `list_document_type_filters`

- **Use when:** you need the current document-type category keys, canonical tags, labels, filter policy, or global exclusions before choosing a type filter.
- **Required inputs:** none; optional `query`, `category_key`, `include_raw_tags`, and `include_display_only`.
- **Returns:** taxonomy metadata only: `categories`, optional `tags`, `global_exclusions`, and examples for the type-constrained tools.
- **Next step:** `browse_docket(document_type_tags=[...])` for retrieval by type, or `search_content(content_type="filing", document_type_tags=[...])` for type-scoped filing text search.
- **Example:** *Which document type filter should I use for Rule 2004 filings?*

## Search and retrieval

Find documents and pull exact language.

### `search_content`

- **Use when:** you need filings, source/news metadata and snippets, or hearing transcripts that mention specific terms.
- **Required inputs:** a query; optional `case_id` or `case_watch_id`, source-type, document-type, and date filters.
- **Returns:** matching filings/transcripts with `document_id` for tool calls, optional compact `citation_ref`, and, in citation-bearing modes, `citation_map` browser filing links; source results return `source_content_id`, source metadata, bounded snippets, and publisher-link handles.
- **Next step:** for filings/transcripts, use `search_within` to narrow long results, then `read_text`; for sources, use the source handle for citation/linking.
- **Example:** *Which filings in this case discuss cash collateral and adequate protection?*

For filing searches, `search_content` can combine `text` with
`document_type_tags`, for example searching only fee applications for a name or
firm. Use `list_document_type_filters` for vocabulary discovery; use
`browse_docket(document_type_tags=[...])` when the job is retrieval by type
without a text query.

### `search_within`

- **Use when:** you already have a specific filing or transcript document.
- **Required inputs:** `document_id` and a query.
- **Returns:** chunk IDs, excerpts, relevance signals, optional compact `citation_ref`, and, in citation-bearing modes, `citation_map` browser filing links.
- **Next step:** `read_text` for exact language or `analyze_document` for structured extraction.
- **Example:** *Within this DIP motion, which chunks cover maturity, pricing, milestones, and covenants?*

### `read_text`

- **Use when:** exact wording matters — legal language, dates, amounts, deadlines, vote percentages, releases, injunctions, defined terms.
- **Required inputs:** `document_id` plus either `chunk_id`s or a bounded character window.
- **Returns:** exact court-document text for the selection, offsets, metadata, optional compact `citation_ref`, and, in citation-bearing modes, `citation_map` browser filing links.
- **Next step:** cite the result in your answer.
- **Example:** *Return the exact adequate-protection language from these chunks.*

## Analysis and summaries

Compress long filings into structured extracts and case-level synthesis.

### `analyze_document`

- **Use when:** you need a structured extract from a filing or transcript and have already selected the relevant chunks.
- **Required inputs:** `document_id` and a list of `chunk_id`s plus the extraction request.
- **Returns:** structured fields with grounding offsets back to the selected document chunks.
- **Next step:** `read_text` for exact wording on any extracted field.
- **Example:** *From these DIP financing chunks, extract rate, maturity, covenants, milestones, and liens.*

### `get_document_summary`

- **Use when:** you want the current summary for a single filing.
- **Required inputs:** `document_id`.
- **Returns:** the most recent summary record for the document. If no usable
  summary has completed, returns `available: false` with a reason instead of
  treating absence as a tool error.
- **Next step:** `read_text` if exact language matters.
- **Example:** *Summarize the confirmation order.*

### `search_summaries`

- **Use when:** you want compressed summary segments across a case.
- **Required inputs:** `case_id` or `case_watch_id` and a query.
- **Returns:** matching summary segments with provenance to underlying filings.
- **Next step:** `read_text` on the source filings before relying on legal language.
- **Example:** *Which filings discuss plan recoveries, settlements, releases, and the liquidation trust?*

## Relationships and signals

Map how filings connect.

### `explore_document_graph`

- **Use when:** you want to see how filings reference each other.
- **Required inputs:** `case_id`, `case_watch_id`, or `document_id`.
- **Returns:** document relationships, highly-cited filings, and motion/order threads.
- **Next step:** `read_text` or `analyze_document` on anchor filings.
- **Example:** *Which filings anchor the plan and disclosure statement in this case?*

## Not currently included

ElevenFlo MCP does not generate legal documents. `generate_bankruptcy_document` is not part of the hosted MCP tool set.

`build_case_context_pack`, `search_intel_events`, and `lookup_case_law` are not
part of the hosted MCP tool set today. They may be added later, after separate
testing and documentation.

## Permissions and data access

Hosted MCP uses OAuth consent, read-only tools, grant revocation, usage logging, data-access boundaries, and prompt-injection guardrails. See [Permissions and data access](/docs/mcp/permissions-and-data-access) for the full policy.
