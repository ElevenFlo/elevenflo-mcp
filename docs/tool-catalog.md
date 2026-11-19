# Tool catalog

The live `tools/list` response is the exact input and output contract. Inputs
reject unknown fields, and that rejection's `recovery` lists the arguments the
tool takes. Errors return `{code, message, retryable, recovery?, details?}`,
and public TextContent is the JSON copy of the same `structuredContent` payload.
Every list-returning tool returns bounded rows; a `limit` or `max_pages`
above a tool's maximum reads the maximum. ElevenFlo MCP
exposes 16 tools: 12 bankruptcy research tools, three that manage the
connected user's own monitors, and one that shows the connected account's
plan and credits. This page is the complete list,
and a tool absent here is not available. See
[Permissions and data access](https://elevenflo.com/docs/mcp/permissions-and-data-access)
for what ElevenFlo MCP cannot do.

Cases are addressed by `case_id`, documents by UUID `document_id`. Use
identifiers exactly as returned. ElevenFlo MCP accepts no aliases. When a
document row carries `has_primary_pdf: true`, open the document at
`/api/search/open/<uuid>/` using that same UUID.

## The research loop

1. `find_cases` to resolve a `case_id`.
2. `list_docket_entries`, `search_documents`, or `find_key_documents` to
   select documents, and `list_related_documents` to follow one document to
   the motion, order, objections, replies or later amended version linked to it.
3. `search_document_pages` to see which pages hold the keywords across up to
   25 documents.
4. `read_document_pages` for the text of those pages, or of the pages in one
   document that match a query.

## Cases and dockets

### `find_cases`

Resolve a case by identity. `query` matches case name, case number,
jurisdiction and judge with typo tolerance; legal-form words such as Inc.,
LLC, Holdings and Group do not count toward a name match. It does not match
counsel, docket text or document text (use `search_documents` for those).
Screening is not this tool: filter or count a population with the `cases`
structured dataset.

- Inputs: `query` (required) and `limit` (1 to 50). Any other argument is
  rejected with `invalid_input` naming the argument, never an empty result.
- Returns: `case_id`, identifying metadata, petition date, chapter, the
  petition asset and liability brackets with `assets_basis_quality` and
  `liabilities_basis_quality` (`evidenced` or `legacy_unverified`), document
  coverage and case-type tags. Best match first.

### `list_docket_entries`

List observed docket entries for one case, including full entry text, docket
evidence, court citations, and links to held documents. Entries can exist
without a PDF or searchable document text. For current case status, verify the
relevant terminal notice rather than relying on a projected date.

- Inputs: `case_id`; optional `limit` from 1 to 25, `query`,
  `document_type_tags`, inclusive `date_filed_from`/`date_filed_to` dates, up to
  25 positive `docket_entry_numbers` or an inclusive
  `docket_entry_number_from`/`docket_entry_number_to` range (one bound alone is
  open-ended; the list and the range cannot be combined), and an opaque
  `cursor` from the preceding page.
  A cursor is valid only with the same filters. Dates describe filing dates,
  not independently verified entry or signature dates.
- Text search: `query` matches an entry when every whitespace-separated term
  appears, in any order and ignoring case. When a date or docket entry number is
  known, start with those filters and omit `query` and type filters.
- Filters: `document_type_tags` accepts only category keys and canonical
  tags; an unknown value returns a typed error listing the allowed values. Only
  entries with classified documents carry tags; entries known only from court
  RSS carry the court's short label and no tags, so tag and text filters can
  exclude them.
- Returns: the `case_id`, `entries`, evidence and, when more entries follow,
  `next_cursor`; PDF/text flags describe held artifacts. Each entry
  carries its `docket_entry_number`, `date_filed` and `docket_text`, the court's
  own docket text in full. Every held document with a public UUID appears once,
  inside its entry, with its `document_type_tags` and a `document_url` that
  opens the document. A document repeats `docket_text`, `date_filed`,
  `docket_entry_number` or `court_locator` only where its own value differs
  from the entry's; a field it omits is the entry's. An empty first
  page carries `coverage_note`, which says whether the filters excluded recorded
  entries or none are recorded in that window, the case's coverage source, and
  whether the docket is known to be incomplete or its completeness is unknown.
- Court locator: each entry and document can carry `court_locator`: the court
  `citation` (`Bankr. D. Del., No. 22-11068, ECF No. 512`; adversary and
  claim-register forms use `Adv. No.` and `Claim No.`), `court`, `case_number`,
  `docket_entry_number` or `claim_number`, `case_docket_url`, and an optional `pacer`
  link whose `target` is `case`, `entry`, or `document`. It says where the document
  sits on the court's docket, not where ElevenFlo's copy came from. A PACER link
  opens with the reader's own PACER login; PACER fees may apply. Document,
  summary, page and key-document results carry the same object.

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
  the `changes` since. Every call returns `next_cursor` as a resume point;
  `has_more` is true when more changes are ready now. `coverage` states the scope, what unknown facts left out,
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
topics and `filter_revision`. `limit` is 1 to 100 (default 50); pass `next_cursor`
back as `cursor` for the next page; it is absent on the last page. `total`
counts every monitor.

### `delete_monitor`

Delete one of the connected user's monitors by `monitor_id`. Its feed and email
updates stop. This cannot be undone.

## Cross-document search

These tools require every substantive query term to appear in the matching
evidence. One common word cannot pull in unrelated results.

### `search_documents`

Search whole court documents or hearing transcripts across all cases or within one
case. Every term must
appear somewhere in the document, not necessarily in one passage. Quoted text is
an exact phrase; uppercase `OR`, `NOT` and parentheses combine terms, for
example `"cash collateral" AND (objection OR NOT interim)`. A malformed query
returns `invalid_query` naming the position to fix.

- Inputs: `query`; optional `content_type` (`filing`, the default, or
  `transcript`), `case_id`, `document_type_tags` (not transcripts), inclusive
  `date_filed_from`/`date_filed_to` dates, and `limit` from 1 to 25.
- Returns: document rows with `document_id`, `document_url`, case,
  `docket_text` (the court's docket text in full), `docket_entry_number`, date
  and a matching excerpt. Court documents come newest first and add type tags.
  Transcripts come in relevance order and add `transcript_source_kind`. An
  unknown `document_type_tags` value returns the allowed values.
- The filing type `venue_or_dismissal_motion` covers motions to transfer venue,
  motions to dismiss a bankruptcy case and motions to dismiss an adversary
  complaint. Narrow by the relief sought, for example `"motion to dismiss" AND
  1112`, and check the caption and proceeding. To include responsive filings
  and opinions, search the text across filing types.
- A transcript is one of two kinds. `certified_filing` is a court reporter's
  transcript filed on the docket. Read it by page like other court documents.
  `court_audio_asr` is a machine transcription of court audio, not a
  court-reporter transcript; it can misstate names and figures and reads by
  time.

## Exact text

### `search_document_pages`

See which pages hold a few keywords across up to 25 selected documents or
transcripts before reading full text.

- Inputs: one to 25 `document_ids` and `query`.
- Returns: `hits`, each a `document_id`, 1-based `page` and short snippet, for
  the pages that hold every keyword, in relevance order within a document.
  When a document has no such page, its closest pages come back with
  `matched_terms`. `no_text_document_ids` lists documents with no extracted
  text and `missing_document_ids` those not found or not public.
- Limits: a document with no hit has no entry; that does not establish the
  topic is absent.

### `read_document_pages`

Read pages of one document or transcript.

- Inputs: `document_id` and one of `pages` (numbers and ranges such as
  `3-5,9`), `query` (a few keywords) or, for a court-audio transcript, `time`
  (such as `12:30`); optional `max_pages` from 1 to 10 (default 3). With none
  of the three, the first `max_pages` pages are read.
- Returns: each page's text and number, `page_count` and the `document_url`. For a numbered PDF page, `page_url` opens that page after sign-in.
  One call reads 10 pages. When `pages` names more, the first 10 in page
  order are read and `unread_pages` lists the rest that exist in the
  document, written so it can be passed as `pages` on the next call.
  With `query`, the pages holding every keyword come back in relevance order,
  all of them listed in `matched_pages`, with `has_more_matches` when more
  matched than were returned; if no page holds every keyword, the closest
  pages carry `matched_terms`. A court-audio transcript reads as numbered
  blocks of speaker turns with `start_time` and `end_time`; a court
  reporter's transcript reads by page, and `time` on one returns
  `invalid_request`.
- When text is unavailable, `source_not_searchable` gives the original PDF link if ElevenFlo has a downloadable file. For a docket-only entry, it identifies the case for related-filing or hearing-transcript searches. Results from court audio should be cited as machine transcripts.
- A document with text but no page map can be read by `query`. A page outside the document returns
  `page_not_found`; an unknown document returns a typed not-found error.

## Document relationships

### `find_key_documents`

Find key documents in a case, ranked by incoming citation count. Citation frequency
is a starting point for research, not a judgment of legal importance.

- Inputs: `case_id`, optional `limit` from 1 to 25.
- Returns: document rows with citation counts, each document's `docket_text`
  and `docket_entry_number`, and a `document_url` that opens it.

### `list_related_documents`

List the documents one document's docket entry refers to and the documents
whose docket entries refer to it, such as the motion an order rules on, the
objections and replies to a motion, or the later amended version, redline
notice or order whose docket entry cites an earlier filing.

- Input: `document_id`.
- Returns: two lists, `references` and `referenced_by`, holding up to 20
  documents between them, newest filing first in each. Each list holds up to
  10, and either list fills the room the other leaves. Each row has
  `document_id`, `document_url`, the court's `docket_text`,
  `docket_entry_number`, `date_filed` and `relationship_type`. `truncated` is
  true when more exist; the oldest in a full list are not listed.
- Limits: a link exists where one docket entry cites another by number. It
  does not establish that a later filing amends or replaces the cited one. An
  empty list does not establish that nothing relates to the document.

## Structured data

Structured datasets carry typed, source-linked fields extracted from documents.
Use them when the question is about typed fields, comparable records, or many
cases at once rather than the text of one document. Responses return only public
fields and operations, never internal extraction metadata.

Free accounts can list and describe datasets and run aggregates that do not
name a case. Reading rows with `search_dataset`, and grouping by a field that
names a case (`case_id`, `id`, `case_name`, `case_number`,
`lead_case_number`, `case_url`), require ElevenFlo Pro.

### `list_datasets`

List every dataset, or count one case's records in each.

- Inputs: optional `case_id`, a case identifier returned by `find_cases`.
- Returns: `columns` once (`dataset`, `one_row_per`), then `stages` in the
  order a case runs. Each stage holds its `families`, and each family one
  array per dataset in the order of `columns`: the `dataset` name (the value
  the other dataset tools take) and what one row is. With `case_id`, each
  array ends with `case_rows`, the records the dataset holds for that case;
  `search_dataset` filtered on `case_id` reads them. A count of zero does not
  establish that an event did not occur.
- The whole list is one credit. `describe_dataset` gives a dataset's title,
  freshness and coverage.

### `describe_dataset`

Call this before you query an unfamiliar dataset.

- Inputs: `dataset`, a name returned by `list_datasets`; optional `fields`,
  up to 60 field names.
- Returns: what one row is, `as_of`, `complete_since`, `grade`,
  `cases_covered`, caveats, one `fields` table and example calls. Each field
  row gives its name, type, unit, `fill` (the share of rows that hold a
  value), allowed values, the filter operators it accepts and its meaning.
  `extra_filters` lists filters that are not fields; `sort` and `facets` list
  the fields that sort and facet.
- Every description is one credit. A wide dataset describes its main fields
  in `fields` and names the rest by type in `more_fields`. Pass any of those
  names as `fields` to get their rows; that response holds the dataset, its
  title, what one row is and the rows asked for, in the order asked.
- `case_fields` lists the case facts every dataset except `cases` carries
  (chapter, court, judge, petition date, size ranges and the like) with each
  one's type and its allowed values when the list is short. They filter,
  select and group like the dataset's own fields; pass one as `fields` for its
  full row.
- A filter has one name, the one `describe_dataset` lists; there
  are no aliases. Sale-process dates filter on `latest_source_date_filed` and
  fees-by-professional on `latest_date_filed`. Calendar-date ranges support
  `eq`, `gt`, `lt`, `gte`, `lte`, and `between`; numeric ranges support
  `gte`, `lte`, and `between`. `exists` tests whether a value is stated.

### `search_dataset`

Return typed rows from one dataset.

- Inputs: `dataset`; optional typed `where`, `select`, `sort`, `limit`,
  `cursor`, and requested `facets` as listed by the dataset description.
- Returns: `columns` once, then one array per record in that order, with
  `total` (null when not counted), facet counts when asked, `next_cursor`
  when more records match, `as_of` (null when unknown) and `complete_since`. Wide projections clamp page size to the
  select budget.
- Stored spellings: to find how a name is stored, filter the field with
  `contains` and request it as a facet.
- Limits: a `cases` screen on `assets_range` or `liabilities_range` cannot
  match a case whose petition reports no bracket.

### `aggregate_dataset`

Count and summarize records across one dataset.

- Inputs: `dataset` and `metrics`; optional typed `where`, `group_by`,
  `sort`, and a `limit` of up to 1,000 groups. A date field groups by a
  time bucket and a number field by a fixed `width`. A metric's own `where`
  counts only the rows it matches, so two metrics in one call give a rate.
- Returns: `columns` once, then one array per group: the group fields, each
  metric and, for a field metric, `<metric>_n`, the rows that held a value.
  `<metric>_is_floor` marks a sum that leaves out amounts the documents do not
  state, `partial` marks a time bucket that starts before `complete_since`,
  and `totals` gives each metric over every group.

### Available datasets

- `cases`: one row per canonical bankruptcy case for screening by chapter,
  Subchapter V election, court and state, petition date, entity form and
  NAICS code from the lead petition, industry and case-type tags, petition
  creditor, asset and liability brackets with their evidence basis, stage,
  typed outcome and document coverage. Closed facts spell absence
  as `unknown`, `missing` or `none`, so a group-by on them counts every case
  once; tag group-bys count a case once per tag and overlap.
- `voluntary-petitions`
- `top-unsecured-creditors`
- `capital-instruments`
- `capital-stated-totals`
- `capital-parties`
- `capital-links`
- `dip-facilities`
- `dip-orders`
- `dip-cash-flow-budgets`
- `dip-milestones`
- `committees`: one row per official committee with its current roster.
  `notices` lists every appointment notice with the roster that notice states,
  oldest first.
- `committee-members`: one row per member's time on a committee. Filter
  `is_current` to `true` for today's roster. For the members on a date, filter
  `joined_date` with `lte` that date and `on_committee_until` with `gt` it.
  `joined_date_basis` and `left_date_basis` say whether a notice states the
  date or a filing date bounds it; count departures with `end_transition`
  equal to `actual_departure`.
- `schedule-summary-totals`
- `schedule-assets`: one disclosed asset item on a Schedule A/B; filter
  `asset_category` to `real_property` for real estate.
- `codebtors`: one codebtor a debtor lists on Schedule H. Filter
  `codebtor_is_case_debtor` to `true` for codebtors whose name matches one
  debtor on the case's roster; `false` does not show a codebtor is outside
  the group.
- `codebtor-obligations`: one schedule reference of one Schedule H row, joined
  to the Schedule D or E/F claim or Schedule G contract the same filing
  supports. `case_id` is required. `link_status` says how far the filing
  supports the link: `line_match` (the printed line number and the creditor
  name agree), `name_match` (a checked box and exactly one row for that
  creditor, or the one row whose description is the debt Schedule H notes
  after the creditor's name), `line_match_name_differs`, `ambiguous`, `no_matching_row`,
  `schedule_not_read` and `no_reference` carry no confirmed obligation.
  `obligation_row_id` is the `id` of the row in `scheduled-claims` or
  `executory-contracts`; one debt with several codebtors repeats on each, so
  count distinct `obligation_row_id` and do not sum `obligation_amount_usd`.
  Schedule H does not say whether a codebtor is a guarantor or a co-borrower;
  `codebtor_debt_description_as_filed` carries what a filing prints about the
  debt on Schedule H itself, when it prints anything.
  `codebtor_is_case_debtor` is true when the codebtor is itself a debtor on
  the case's roster. A debtor that files its schedules again has rows from
  each filing: filter `is_latest_filing_for_debtor` to `true` to read one
  filing per debtor, knowing a later filing can list only what it adds. A
  codebtor listed against several creditors or schedules still has one row
  per reference, so count distinct `codebtor_row_id` or codebtor names.
  `candidate_lines` gives the schedule line of each candidate row of an
  ambiguous reference. When the codebtor is another debtor of the case and
  the reference is to Schedule D or E/F, `codebtor_own_row_status` says what
  that debtor's own schedule lists for the creditor (`one_row`,
  `several_rows`, `no_matching_row`, `schedule_not_read`) and
  `codebtor_own_row_id` is its row in `scheduled-claims`: the same debt
  scheduled by a second debtor, never a second exposure.
  `codebtor_own_row_basis` says whether the row's description and lien
  read as the debt or it is only the one row naming the creditor, and
  `codebtor_own_amount_agrees` whether both debtors state one amount.
  `scheduled_debtor_stated_role` and `codebtor_stated_role` carry
  `borrower` or `guarantor` only where a debtor's own row prints it.
- `scheduled-claims`: one claim listed on a Schedule D or E/F, with its
  bucket (`secured`, `priority_unsecured`, `nonpriority_unsecured`), stated
  amounts and disputed, contingent and unliquidated checkboxes. A secured
  claim's `claim_nature_as_filed`, `collateral_description_as_filed` and
  `lien_description_as_filed` are read apart: when one agent holds several
  facilities on the same collateral, the lien names the facility. An aggregate
  needs `case_id` or `search`.
- `sofa-disclosures`: one answer to a Statement of Financial Affairs
  (Official Form 207) question, with its `disclosure_type`, party, amount, dates
  and the source text that states it. An aggregate needs `case_id`.
- `first-day-relief`: one first-day motion or order with its relief type,
  requested and granted payment caps in their stated currency, and the
  rulings the orders state (`granted_statuses`).
- `bar-dates`
- `claim-transfers`
- `rule-2019-statements`
- `rule-2019-memberships`
- `rule-2019-positions`
- `monthly-operating-reports`
- `monthly-operating-report-periods`
- `monthly-operating-report-case-periods`
- `monthly-operating-report-coverage`
- `keip-kerp`
- `keip-kerp-terms`: one row per payout trigger or payment condition of a
  program at one stage. Join `program_id` to a `keip-kerp` id. Compare the
  `requested` and `authorized` rows that share a `term_label`; `order_effect`
  says what the order did. Read `award_amount` with `award_unit` and
  `award_scope`. Select `citations` for the source pages. A program without
  rows has no terms available in this dataset; that does not show it has none.
- `asset-sales`
- `case-assets`
- `contract-treatment-events`
- `executory-contracts`
- `exclusivity-periods`: one row per exclusive period a motion requests or an
  entered order grants, kept separately for filing a plan and soliciting
  acceptances. Order rows (`source_kind` order) carry the granted deadline,
  `granted_days_from_petition`, the linked motion, and the sequence in
  the case: `prior_deadline_date`, `days_extended`, `extension_number` and
  `superseded_on_date`. For the latest surviving grant on file on a historical date, filter
  `order_filed_date` lte and `superseded_on_date` gt that date.
  `order_entered_date` is available when the order’s court stamp explicitly gives an entry date. Motion rows
  carry the requested deadline and, when an entered order is linked,
  `ruling_disposition` and `ruling_deadline_date`. `link_basis` states how the
  motion and order were paired: `docket_entry` (the order's entry names the
  motion) or `matching_deadline` (the order is the first to grant exactly the
  requested deadline; an inference from the dates). A null sequence value or
  ruling means no link or an intermediate order is not on file; it does not
  show that the court did not act. `days_granted_vs_requested` on an order row
  is the granted deadline minus the linked motion's requested deadline; filter
  lte -1 for orders that gave less time than asked. An order a later order
  vacated has `sequence_status` vacated with `vacated_on_date`, and is never
  returned as the operative order. Count requests on `source_kind` motion:
  a `related_paper` row is an objection, statement or other paper about a
  motion, whose date can be another party's proposal; it names the motion it
  concerns and carries no ruling. `objections_on_file` on a motion row counts
  docket entries titled as an objection that cite the motion; 0 does not show
  the request was unopposed.
- `plans`: each case's current Chapter 11 plan with lifecycle status, type,
  proponent, and filing, confirmation, and effective dates. A withheld date is
  null, never zero; an empty result never proves a plan event did not occur.
- `plan-classes`: one row per class, with class-level recovery estimates and
  the published `recoveries` when available. `recovery_mid_percent` can be
  calculated from the class's bounds. Use the individual outcomes for elections.
- `plan-class-recoveries`: one row per published recovery outcome. Filter
  by `case_id`. Use `class_id` to join the class terms and `outcome`
  and `outcome_label` to check which condition applies. Select `citations`
  to verify stated estimates and terms on the source page.
- `disclosure-solicitation-events`
- `ballot-class-results`
- `post-confirmation-reports`
- `post-confirmation-report-quarters`
- `post-confirmation-report-case-quarters`
- `post-confirmation-report-coverage`
- `post-confirmation-plan-comparison`
- `conversions-dismissals-closures`: court orders converting, dismissing or
  closing a case, newest-filed first. Rows default to rulings on the case
  itself; filter `applies_to_tracked_case` to `no` or `unresolved` for orders
  that end only other jointly administered cases or leave the scope open.
  `affected_case_numbers` lists the case numbers the order prints.
- `hearing-sessions`: one row per current hearing session. A `hearing_date`
  sort is calendar order: one day's sessions follow the clock. For per-case
  totals group by `case_id`: the count is sessions, the sum of `matter_count`
  is agenda matters, and the minimum and maximum `hearing_date` are the first
  and last hearing.
- `adversary-proceedings`: one row per adversary proceeding under a court
  number, with the parties and nature of suit the clerk announces on the
  bankruptcy docket and the dates of judgment, dismissal, closing and appeal
  entries read since. There is no status: a null date does not establish an
  open proceeding.
- `bankruptcy-appeals`: one row per notice of appeal, with appellants, the
  docket entry numbers of the orders appealed, the court the appeal was first
  directed to, its case number there, the district judge where the district
  court's own docket is on file, and the ruling once the bankruptcy docket or
  that district docket records one. A null disposition does not establish a
  pending appeal; a panel's or court of appeals' docket is not collected.
- `counsel-appearances`: one row per notice of appearance, pro hac vice motion
  or request for notice that names a client, a firm or an attorney: who
  represents whom, including creditors, lenders and ad hoc groups no retention
  order covers. A firm is recognized only when a retention, a fee application
  or the firm directory already names it. An appearance is not a court-approved
  retention, and a missing row does not show that a party is unrepresented.
- `case-people`: one row per person, firm and case, for the people a case's
  documents name as working on it: what their firm does on the case, whom it acts
  for, the person's title, the latest document naming them and links to the
  person's and the firm's ElevenFlo pages. The firm is the one on that case,
  not current employment; count people with `count_distinct` on `person_name`.
- `professional-retentions`: one row per professional engagement (counsel,
  financial advisers, bankers, claims agents and others) for a retaining party
  in a case, with its lifecycle status, the application and order it rests on,
  each objection, withdrawal and order in `lifecycle_events`, and stated rate
  tiers. Proposed is not approved; a status comes from an entered order.
- `professional-retention-rates`: one row per stated rate tier. Compare like
  with like: filter `band`, `period=hour` and `currency_code`, and filter
  `basis`, because a requested rate is not an approved one.
- `professional-retention-fee-terms` — compare monthly, sale and restructuring terms by firm, role, fee kind, source basis and currency. Select `source_quote` for exceptions and credits and `source_pages` for the filed evidence.
- `fee-applications`
- `fee-decisions`
- `fees-by-professional`: cumulative requested and allowed fee economics by applicant, case, and currency; group by `firm_name` for requested fees by firm. Requested fees are not court-allowed fees. Affiliates appear separately without parent-firm consolidation, and roles may be unknown. The public `requested_amount_rejected` boolean indicates that requested economics exclude rejected documents; surviving requests in the same rollup still contribute. `allowance_note` states in plain words when allowed totals are blank, when a final basis also counts interim awards, or when combined or undated awards are left out or added unchecked. Monetary sorts require `currency_code` with `eq`; the default sort is latest filing date. `sources` identifies contributors separately for each monetary field, with availability, counts and truncation. Null provenance awaits reprojection. Order-date filters select applicants without rebasing their lifetime totals to the selected month.
- `fee-timekeepers`: named historical billing rates by firm and case. Filter
  `timekeeper_name` with name tokens in any order, `organization_name`,
  `timekeeper_title`, or `hourly_rate`. Group partner rates by firm, filing year,
  and currency; aggregates default to monthly, final, and first-and-final
  applications. Rates are observed billing evidence, not current market rates.
- `fee-time-entries`

This page is a static copy of the public catalog. Call
`list_datasets` for the live list and `describe_dataset`
for each dataset's fields, operations, and limits. Datasets outside the public
catalog return `unknown_dataset`, and its `recovery` names the closest dataset names.

`hearing-sessions` uses schema `hearing-sessions.v4`. `scope_kind` distinguishes
bankruptcy-case, single-adversary, joint-adversary and unresolved ownership.
`adversary_proceedings` contains canonical court numbers, captions and available
tracking URLs; `scope_sources` contains immutable public-document/hash references
for qualified joint conferences. Unresolved scope does not assert case ownership.
Select `matter_sources` for docket documents linked to the session's current
matters. Each link names the matter and the filing's role, such as notice,
adjournment or agenda. Pass its `document_id` to `read_document_pages` to
check what the filing says about the setting.

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

## Requests to ElevenFlo

Two tools send ElevenFlo staff something from the signed-in user, and only
when the user asks: a request to add a case or missing documents, and a report
about wrong data, an incorrect citation or a failed tool, or a suggestion.
Neither costs credits. A prepaid tab has no user to answer, so neither is
offered to one. Resending the same request or report returns the one already
received, so a client can retry safely after a lost response.

### `request_coverage`

Ask ElevenFlo to add a case it does not list, or documents missing from a case
it does.

- Inputs: `case_id` from `find_cases` with `missing_documents` naming the
  filings, such as "Docket 412, the sale order". `missing_documents` is
  required when the case already has documents. For a case ElevenFlo does not
  list: `case_name` with `court` or `case_number`, and `confirm_not_listed`
  once the user has ruled out the possible matches a first attempt returned.
- Returns: `request_id`, the reference to quote when following up; `status`
  (`needs_review`, then `fulfilled` or `declined`); `created`;
  `documents_added`; `case_id` once the case is identified; `case_name`; and a
  plain `message`. Staff email the user the outcome.
- Members of one ElevenFlo account share one open request per case and can
  read its notes. A request naming another missing filing adds it to the open
  request.
- If ElevenFlo lists a case matching the name or number, the tool returns
  `case_listed` or `ambiguous_case` with the matches and records nothing.
- A request records interest. It does not promise that ElevenFlo can obtain
  the documents.

### `submit_feedback`

Report a problem with ElevenFlo data or tools, or make a suggestion.

- Inputs: `category` (`data_correction`, `incorrect_citation`, `tool_failure`
  or `suggestion`), a `description` of up to 1,000 characters, and the
  references that locate the problem: `case_id`, `document_id` with `page`,
  `dataset` with `row_id`, `tool_name`, or `request_id`, the Request ID a
  failed tool returned. A data correction or incorrect citation names a case,
  document or dataset; a tool failure names a tool or Request ID.
- Returns: `feedback_id`, the reference to quote when following up; `status`;
  `created`; and a plain `message`.
- The report holds only these fields. ElevenFlo does not receive the
  conversation, prompts or credentials. A Request ID is accepted only from the
  user's own tool calls.

## Credits

MCP credits measure returned data. Each successful response costs the largest
of 1 credit, returned records divided by 10 (rounded up), or canonical response
bytes divided by 10,000 (rounded up). Protocol duplicates are counted only once.
A 100-row response of 20 KB costs 10 credits; a 45 KB text response costs 5.
Failed calls are not charged. Successful empty responses cost 1 credit.
`show_plan_and_credits`, `request_coverage` and `submit_feedback` cost none.

Free includes 200 MCP credits per calendar month, with no daily limit.
Pro includes 10,000 per calendar month and 2,000 per rolling 24 hours. Agents
without an account buy prepaid tabs of 100 credits for USD 5.00; see
[Pay as an agent](agent-purchases.md). All clients
share the account balance and its 120-request-per-minute limit. Monthly balances
reset at 00:00 UTC on the first of the month; Pro's rolling capacity recovers as
usage ages past 24 hours. A result that exceeds remaining capacity, or the caller's
`elevenflo/max_credits` request budget, is withheld and not charged. Every
successful result reports its cost and the remaining balance in `_meta`. See
current balances with `show_plan_and_credits` or on your account page.

### `show_plan_and_credits`

Show the connected account's plan and MCP credits.

- Inputs: none.
- Returns: `plan` (`Free` or `Pro`) and `monthly_credits`: the credits
  included, used and remaining this month and when they reset. Pro adds
  `daily_credits` for the rolling 24 hours. A Free account also receives
  `upgrade`: what ElevenFlo Pro includes, its price, and a link that opens the
  upgrade on elevenflo.com, where payment happens.
- Costs no credits and works after a credit limit is reached. A client that
  supports MCP Apps shows the result as a panel in the conversation; any other
  client receives the same fields as text.
- Limits: a prepaid tab holds a balance but no plan, so the tool is not
  offered to one.

Website document access has a separate rolling-day budget of 75 distinct
documents on Pro. Free accounts keep their monthly download allowance instead.
Reopening the same document and PDF range requests do not consume additional
rolling-day slots.
