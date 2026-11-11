# Tool catalog

The live `tools/list` response is the exact input and output contract. Inputs
reject unknown fields, errors return `{code, message, retryable, recovery?, details?}`,
and public TextContent is the JSON copy of the same `structuredContent` payload.
Every list-returning tool returns bounded rows. ElevenFlo MCP
exposes 21 tools: research tools plus monitor management (18 bankruptcy research
tools and three tools that manage the connected user's own monitors). This page is the complete list,
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
3. `search_document_chunks` to preview where keywords appear across up to 25
   filings.
4. `extract_document_passages` for the full matching passages in one filing, or
   `read_document_chunks` for exact passages by ID.
5. `search_document_summaries` or `get_document_summary` for synthesis.

## Case and taxonomy

### `find_cases`

Resolve a case by identity. `query` matches case name, case number,
jurisdiction and judge with typo tolerance; legal-form words such as Inc.,
LLC, Holdings and Group do not count toward a name match. It does not match
counsel, docket text or filing content (use `search_filings` for those).
Screening is not this tool: filter or count a population with the `cases`
structured dataset.

- Inputs: `query` (required) and `limit` (1 to 50). Any other argument is
  rejected with `invalid_input` naming the argument, never an empty result.
- Returns: `case_id`, identifying metadata, petition date, chapter, the
  petition asset and liability brackets with `assets_basis_quality` and
  `liabilities_basis_quality` (`evidenced` or `legacy_unverified`), document
  coverage and case-type tags. Best match first.

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
- Text search: `query` matches an entry when every whitespace-separated term
  appears, in any order and ignoring case. When a date or docket number is
  known, start with those filters and omit `query` and type filters.
- Filters: `document_type_tags` accepts only category keys and canonical tags
  returned by `list_document_types`; unknown values return a typed error. Only
  entries with classified documents carry tags; entries known only from court
  RSS carry the court's short label and no tags, so tag and text filters can
  exclude them.
- Returns: version 2 `entries`, evidence, `has_more`, and `next_cursor` (null
  when exhausted); PDF/text flags describe held artifacts. Every held document
  with a public UUID appears once, inside its entry, with its
  `document_type_tags` and a `filing_url` that opens the filing. An empty first
  page carries `coverage_note`, which says whether the filters excluded recorded
  entries or none are recorded in that window, the case's coverage source, and
  whether the docket is known to be incomplete or its completeness is unknown.
- Court locator: each entry and document can carry `court_locator`: the court
  `citation` (`Bankr. D. Del., No. 22-11068, ECF No. 512`; adversary and
  claim-register forms use `Adv. No.` and `Claim No.`), `court`, `case_number`,
  `docket_number` or `claim_number`, `case_docket_url`, and an optional `pacer`
  link whose `target` is `case`, `entry`, or `document`. It says where the filing
  sits on the court's docket, not where ElevenFlo's copy came from. A PACER link
  opens with the reader's own PACER login; PACER fees may apply. Document,
  summary, chunk, hub, and relationship results carry the same object.

## Monitors and case updates

### `list_case_updates`

Read what changed for the connected user's saved monitors, or for named cases,
as a resumable feed. It reads; it does not schedule checks or send
notifications.

- Inputs: optional `monitor_ids` (from `list_monitors`) or one to 25 `case_ids`,
  never both; with neither it reads every saved monitor. Resolve a named case
  with `find_cases` first. Optional `since` is an ISO-8601 timestamp within the
  last 90 days for the first call; it defaults to seven days ago. `limit` is 1
  to 50. Optional `cursor` continues or resumes; omit `since` with it.
- Returns: `changes` in sequence order. An `upsert` carries the publication
  (headline, body, case, sources and citation ranges), its record `status` and
  the matching `monitor_ids`; a `remove` names a development that no longer
  matches, such as a corrected asset scope or a withdrawal. The first call
  pages a fixed `snapshot`; keep `next_cursor` and pass it later to get just
  the `changes` since. `coverage` states the scope, what unknown facts left out,
  and what the covered cases hold.
- Limits: zero changes does not mean nothing was filed. After a monitor is
  edited, added or deleted the cursor returns `monitor_changed`: call again
  without `cursor` for a fresh snapshot. `watched` and `triggers` were removed:
  omit `watched` to read all your monitors, and set `topics` on a monitor with
  `save_monitor` instead of `triggers`. Monitor scopes need a signed-in user;
  market monitors need Pro.

### `save_monitor`

Save a monitor for the connected user, or edit one by `monitor_id`.

- Inputs: `filters`: either `case_id` alone (a case watch) or market filters
  combined with AND: `chapters`, `subchapter_v`, `court_states`, `statuses`,
  `event_kinds`, `asset_classes` (a class includes its subclasses), `min_units`
  (requires `asset_classes`) and `keyword` (every word must appear). Optional
  `name`, and `topics` to narrow publication topics such as `sale`.
- Returns: the monitor and a 28-day dry run: publications it matches and those
  left out because a fact a filter needs is unknown.
- Limits: Free accounts hold five case watches; market monitors need Pro, up
  to 20. An edit keeps the id and makes earlier cursors return `monitor_changed`.

### `list_monitors`

List the connected user's monitors, oldest first, with their ids, filters,
topics and `filter_revision`. `limit` is 1 to 100 (default 50); `offset` pages
on, and `total` and `has_more` say how many there are.

### `delete_monitor`

Delete one of the connected user's monitors by `monitor_id`. Its feed and email
updates stop. This cannot be undone.

## Cross-document search

These tools require every substantive query term to appear in the matching
evidence. One common word cannot pull in unrelated results.

### `search_filings`

Search whole filings across all cases or within one case. Every term must
appear somewhere in the filing, not necessarily in one passage. Quoted text is
an exact phrase; uppercase `OR`, `NOT` and parentheses combine terms, for
example `"cash collateral" AND (objection OR NOT interim)`. A malformed query
returns `invalid_query` naming the position to fix.

- Inputs: `query`; optional `case_id`, `document_type_tags`, and `limit`.
- Returns: document rows, newest first, with matched excerpts and a
  `filing_url` when available. `document_type_tags` accepts only values
  returned by `list_document_types`.

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

Preview where keywords appear across up to 25 selected filings before reading
full text.

- Inputs: one to 25 `document_ids` and `query`.
- Returns: matching chunk IDs, 1-based page numbers (0 when unavailable), exact
  text, and offsets grouped by document. `missing_document_ids` lists requested
  UUIDs that were not found or were not public.

### `read_document_chunks`

Read selected passages from one document by passage ID.

- Inputs: `document_id` and one to eight `chunk_ids`.
- Returns: available chunk text, page numbers when available, offsets,
  `total_chunks`, and `missing_chunk_ids` (empty when every requested chunk exists).
  Valid IDs range from 0 to `total_chunks - 1`; an all-missing request returns
  `chunk_not_found` with the missing IDs and range.

### `extract_document_passages`

Return the full text of the passages in one filing or transcript that match a
few keywords. Every keyword must appear in a passage.

- Inputs: `document_id`, `query`, optional `max_chunks` from 1 to 10.
- Returns: selected exact chunks and offsets. A searchable filing with no matching
  passage returns an empty `chunks` list, `matched_chunks: 0`, and advice on how
  to refine the search. An unknown document ID returns a typed not-found error.

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
- A filter has one name, the one `describe_structured_dataset` lists; there
  are no aliases. Sale-process dates filter on `latest_source_date_filed` and
  fees-by-professional on `latest_date_filed`. Calendar-date ranges support
  `eq`, `gt`, `lt`, `gte`, `lte`, and `between`; numeric ranges support
  `gte`, `lte`, and `between`. `exists` tests whether a value is stated.

### `search_structured_data`

Return typed rows from one dataset.

- Inputs: `dataset`; optional typed `where`, `select`, `sort`, `page_size`,
  `cursor`, and requested `facets` as declared by the dataset description.
- Returns: public rows, signed continuation cursor, requested facets, snapshot
  freshness, coverage window, provenance, and the effective `page_size`.
  Wide projections clamp page size to the select budget.
- Limits: a `cases` screen on `assets_range` or `liabilities_range` cannot
  match a case whose petition reports no bracket. Such a search or aggregate
  also returns `coverage.unknown_excluded`, the number of cases that match
  every other filter but were left out for that reason, and its statement says
  how many of them are index-only (listed from a court feed, no petition on file).

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

- `ballot-class-results`
- `bar-dates`
- `dip-financing`
- `dip-facilities`
- `dip-milestones`
- `dip-cash-flow-budgets`
- `dip-orders`
- `disclosure-solicitation-events`
- `contract-treatment-events`
- `hearing-sessions`: one row per current hearing session. A `hearing_date`
  sort is calendar order: one day's sessions follow the clock. For per-case
  totals group by `case_id`: the count is sessions, the sum of `matter_count`
  is agenda matters, and the minimum and maximum `hearing_date` are the first
  and last hearing.
- `monthly-operating-reports`
- `conversions-dismissals-closures`: court orders converting, dismissing or
  closing a case, newest-filed first. Rows default to rulings on the case
  itself; filter `applies_to_tracked_case` to `no` or `unresolved` for orders
  that end only other jointly administered cases or leave the scope open.
  `affected_case_numbers` lists the case numbers the order prints.
- `committees`: one row per official committee with its current roster.
  `notices` lists every appointment notice with the roster that notice states,
  oldest first.
- `committee-members`: one row per member's time on a committee. Filter
  `is_current` to `true` for today's roster. For the members on a date, filter
  `joined_date` with `lte` that date and `on_committee_until` with `gt` it.
  `joined_date_basis` and `left_date_basis` say whether a notice states the
  date or a filing date bounds it; count departures with `end_transition`
  equal to `actual_departure`.
- `exclusivity-periods`
- `fees-by-professional` — Cumulative requested and allowed fee economics by applicant, case, and currency; group by `firm_name` for requested fees by firm. Requested fees are not court-allowed fees. Affiliates appear separately without parent-firm consolidation, and roles may be unknown. The public `requested_amount_rejected` boolean indicates that requested economics exclude rejected filings; surviving requests in the same rollup still contribute. `allowance_note` states in plain words when allowed totals are blank, when a final basis also counts interim awards, or when combined or undated awards are left out or added unchecked. Monetary sorts require `currency_code` with `eq`; the default sort is latest filing date. `sources` identifies contributors separately for each monetary field, with availability, counts and truncation. Null provenance awaits reprojection. Order-date filters select applicants without rebasing their lifetime totals to the selected month.
- `fee-timekeepers`: named historical billing rates by firm and case. Filter
  `timekeeper_name` with name tokens in any order, `organization_name`,
  `timekeeper_title`, or `hourly_rate`. Group partner rates by firm, filing year,
  and currency; aggregates default to monthly, final, and first-and-final
  applications. Rates are observed billing evidence, not current market rates.
- `fee-decisions`
- `professional-retentions`: one row per professional engagement (counsel,
  financial advisers, bankers, claims agents and others) for a retaining party
  in a case, with its lifecycle status, the application and order it rests on,
  each objection, withdrawal and order in `lifecycle_events`, and stated rate
  tiers. Proposed is not approved; a status comes from an entered order.
- `professional-retention-rates`: one row per stated rate tier. Compare like
  with like: filter `band`, `period=hour` and `currency_code`, and filter
  `basis`, because a requested rate is not an approved one.
- `keip-kerp`
- `plans`: each case's current Chapter 11 plan with lifecycle status, type,
  proponent, and filing, confirmation, and effective dates. A withheld date is
  null, never zero; an empty result never proves a plan event did not occur.
- `post-confirmation-reports`
- `adversary-proceedings`: one row per adversary proceeding under a court
  number, with the parties and nature of suit the clerk announces on the
  bankruptcy docket and the dates of judgment, dismissal, closing and appeal
  entries read since. There is no status: a null date does not establish an
  open proceeding.
- `bankruptcy-appeals`: one row per notice of appeal, with appellants, the
  docket numbers of the orders appealed, the court the appeal was first
  directed to, its case number there and the ruling once the bankruptcy docket
  records one. A null disposition does not establish a pending appeal.
- `rule-2019-statements`
- `rule-2019-memberships`
- `rule-2019-positions`
- `asset-sales`
- `schedule-summary-totals`
- `top-unsecured-creditors`
- `executory-contracts`
- `schedule-assets`: one disclosed asset item on a Schedule A/B filing; filter
  `asset_category` to `real_property` for real estate.
- `codebtors`
- `claim-transfers`
- `voluntary-petitions`
- `cases`: one row per canonical bankruptcy case for screening by chapter,
  Subchapter V election, court and state, petition date, entity form and
  NAICS code from the lead petition, industry and case-type tags, petition
  creditor, asset and liability brackets with their evidence basis, stage,
  typed outcome and document coverage. Closed facts spell absence
  as `unknown`, `missing` or `none`, so a group-by on them counts every case
  once; tag group-bys count a case once per tag and overlap.

This page is a static copy of the public catalog. Call
`list_structured_datasets` for the live list and `describe_structured_dataset`
for each dataset's fields, operations, and limits. Datasets outside the public
catalog return `unknown_dataset`.

`hearing-sessions` uses schema `hearing-sessions.v4`. `scope_kind` distinguishes
bankruptcy-case, single-adversary, joint-adversary and unresolved ownership.
`adversary_proceedings` contains canonical court numbers, captions and available
tracking URLs; `scope_sources` contains immutable public-filing/hash references
for qualified joint conferences. Unresolved scope does not assert case ownership.

`ballot-class-results` uses schema `ballot-class-results.v3`. It returns one
row per plan class on each current ballot tabulation declaration, with the
declaration's filing date, tabulation date, voting deadline and plan on every
row. For one declaration's class counts, group by `tabulation_id` and
`reported_result` and count rows. `is_operative` is true only for the latest
non-supplemental declaration for the case and plan, or the latest supplemental
one when the plan has no other. `classes_complete` is false when the
declaration reports classes that are not rows. A declaration with no class row
does not appear. These class rows are what the declaration reports; they are
not canonical plan votes.

## Credits

MCP credits measure returned data. Each successful response costs the largest
of 1 credit, returned records divided by 10 (rounded up), or canonical response
bytes divided by 10,000 (rounded up). Protocol duplicates are counted only once.
A 100-row response of 20 KB costs 10 credits; a 45 KB text response costs 5.
Failed calls are not charged. Successful empty responses cost 1 credit.

Free includes 200 MCP credits per calendar month, with no daily limit.
Pro includes 10,000 per calendar month and 2,000 per rolling 24 hours. Agents
without an account buy prepaid tabs of 100 credits for USD 5.00; see
[Pay as an agent](agent-purchases.md). All clients
share the account balance and its 120-request-per-minute limit. Monthly balances
reset at 00:00 UTC on the first of the month; Pro's rolling capacity recovers as
usage ages past 24 hours. A result that exceeds remaining capacity, or the caller's
`elevenflo/max_credits` request budget, is withheld and not charged. Every
successful result reports its cost and the remaining balance in `_meta`. See
current balances on your account page.

Website document access has a separate rolling-day budget of 75 distinct
documents on Pro. Free accounts keep their monthly download allowance instead.
Reopening the same document and PDF range requests do not consume additional
rolling-day slots. Publicly published blog documents remain public.
