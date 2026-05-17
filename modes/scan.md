# Mode: scan — Portal Scanner (Offer Discovery)

<purpose>
Scan configured job portals, filter by title relevance, and add new offers to `data/pipeline.md` for later evaluation.
</purpose>

<rules>
- NEVER run Playwright in parallel — only sequential `browser_navigate` calls.
- NEVER mark an offer expired without navigating to it first (Level 3 URLs only).
- NEVER add a duplicate URL to `pipeline.md` (dedup against history + applications + pipeline).
- ALWAYS save a newly discovered `careers_url` to `config/portals.yml` immediately.
- ALWAYS prefer a company's corporate careers page over a direct ATS URL.
- Do NOT abort the scan if a single URL fails — mark `skipped_expired` and continue.
- `scan.mjs` (zero-token) only covers Greenhouse, Ashby, and Lever APIs. For all other companies, the agent runs this mode manually.
</rules>

> **Note (v1.5+):** `scan.mjs` / `npm run scan` is zero-token and queries Greenhouse, Ashby, and Lever public APIs directly. The levels below describe the **agent** flow. For companies without a supported API, the agent must complete Level 1 (Playwright) or Level 3 (WebSearch) manually.

## Recommended Execution

<agent_instruction>
Run as a subagent to avoid consuming main context:

```
Agent(
    subagent_type="general-purpose",
    prompt="[contents of this file + specific data]",
    run_in_background=True
)
```
</agent_instruction>

## Configuration

<agent_instruction>
Read `config/portals.yml` for:
- `search_queries` — WebSearch queries with `site:` filters (broad discovery)
- `tracked_companies` — companies with `careers_url` for direct navigation
- `title_filter` — positive/negative/seniority_boost keywords
- `location_filter` — optional allow/block location keywords
</agent_instruction>

## Discovery Strategy (3 Levels)

### Level 1 — Direct Playwright (PRIMARY)

<agent_instruction>
For each `tracked_companies` entry with `enabled: true` and a defined `careers_url`:
- `browser_navigate` → `browser_snapshot` → extract all `{title, url, company}` listings.
- If the page has department filters, navigate relevant sections.
- If the page paginates, follow all pages.
- On 404/redirect: fall back to `scan_query` and flag the URL for update.

Preferred over other levels because it sees real-time content and works with SPAs (Ashby, Lever, Workday).
</agent_instruction>

### Level 2 — ATS APIs / Feeds (SUPPLEMENTARY)

<agent_instruction>
For each `tracked_companies` entry with `enabled: true` and a defined `api:`:
- WebFetch the API/feed URL; infer `api_provider` from domain if not set.
- Normalize each result to `{title, url, company}`; merge/dedup with Level 1 candidates.

**Parsers by provider:**

| Provider | Endpoint pattern | Fields |
|---|---|---|
| Greenhouse | `https://boards-api.greenhouse.io/v1/boards/{company}/jobs` | `jobs[].title`, `jobs[].absolute_url` |
| Ashby | GET `https://api.ashbyhq.com/posting-api/job-board/{slug}?includeCompensation=true` | `jobs[].title`, `jobs[].jobUrl` |
| Lever | `https://api.lever.co/v0/postings/{company}?mode=json` | `[].text`, `[].hostedUrl` |
</agent_instruction>

### Level 3 — WebSearch (BROAD DISCOVERY)

<agent_instruction>
For each `search_queries` entry with `enabled: true`:
- Run WebSearch with the defined `query`.
- Extract `{title, url, company}` from each result using: `(.+?)(?:\s*[@|—–-]\s*|\s+at\s+)(.+?)$`
  - Examples: `"Supply Chain Analyst @ Acme"`, `"OR Engineer at Air Liquide"`, `"Consultant - Ops @ BCG"`
- Merge/dedup with Level 1+2 candidates.
- **Level 3 results require liveness verification (Step 7.5) — Google results may be weeks stale.**

Levels are additive: all three run, results are merged and deduplicated.
</agent_instruction>

## Workflow

<process>

<step id="1" name="Read inputs">
<agent_instruction>
- Read `config/portals.yml` (config)
- Read `data/scan-history.tsv` (seen URLs)
- Read `data/applications.md` + `data/pipeline.md` (dedup sources)
</agent_instruction>
</step>

<step id="2" name="Level 1 — Playwright scan">
<agent_instruction>
Run in parallel batches of 3–5 companies. For each `tracked_companies` entry with `enabled: true` and `careers_url` defined, navigate and extract listings. Accumulate in candidates list.
</agent_instruction>
</step>

<step id="3" name="Level 2 — ATS APIs">
<agent_instruction>
Run in parallel. For each `tracked_companies` entry with `enabled: true` and `api:` defined, fetch and parse per provider table above. Accumulate and dedup with Level 1.
</agent_instruction>
</step>

<step id="4" name="Level 3 — WebSearch queries">
<agent_instruction>
Run in parallel if possible. For each `search_queries` entry with `enabled: true`, search and extract listings. Accumulate and dedup with Level 1+2.
</agent_instruction>
</step>

<step id="5" name="Filter by title and location">
<agent_instruction>
Apply `title_filter` from `config/portals.yml`:
- At least 1 `positive` keyword must appear in title (case-insensitive).
- 0 `negative` keywords may appear.
- `seniority_boost` keywords give priority but are not required.

Apply `location_filter` (if block present in config):
- Empty location on an offer → passes.
- Any `block` keyword present → reject (takes precedence).
- Non-empty `allow` → at least one keyword must match.
- All matches are case-insensitive substring.
- Persist location as 7th column in `scan-history.tsv`.

Record filtered-out offers in `scan-history.tsv` with status `skipped_title`.
</agent_instruction>
</step>

<step id="6" name="Deduplicate">
<agent_instruction>
Check candidates against:
1. `scan-history.tsv` — exact URL already seen
2. `applications.md` — normalized company + role already evaluated
3. `pipeline.md` — exact URL already pending or processed

Record duplicates in `scan-history.tsv` with status `skipped_dup`.
</agent_instruction>
</step>

<step id="7" name="Liveness verification (Level 3 only)">
<agent_instruction>
For each new Level 3 URL (sequential — NEVER parallel Playwright):
1. `browser_navigate` → `browser_snapshot`
2. Classify:
   - **Active**: job title + role description + Apply/Submit control visible in main content.
   - **Expired** (any signal wins): URL contains `?error=true`; page contains "job no longer available" / "no longer open" / "position has been filled" / "this job has expired" / "page not found"; only navbar/footer visible (content < ~300 chars).
3. Expired → record `skipped_expired` in `scan-history.tsv`; discard.
4. On `browser_navigate` error (timeout, 403, etc.) → mark `skipped_expired` and continue.

Level 1 and Level 2 results are real-time; skip this step for them.
</agent_instruction>
</step>

<step id="8" name="Add to pipeline">
<agent_instruction>
For each new verified offer passing all filters:
- Append to `data/pipeline.md` Pending section: `- [ ] {url} | {company} | {title}`
- Record in `data/scan-history.tsv`: `{url}\t{date}\t{query_name}\t{title}\t{company}\t{location}\tadded`

For private/non-public URLs:
- Save JD to `jds/{company}-{role-slug}.md`
- Add to pipeline as: `- [ ] local:jds/{company}-{role-slug}.md | {company} | {title}`
</agent_instruction>
</step>

</process>

## Scan History Format

`data/scan-history.tsv` tracks all seen URLs:

```
url	first_seen	portal	title	company	status	location
https://...	2026-02-10	Ashby	Supply Chain Analyst	Acme Logistics	added	Paris
https://...	2026-02-10	Greenhouse	Junior Dev	BigCo	skipped_title	
https://...	2026-02-10	Ashby	OR Engineer	OldCo	skipped_dup	
https://...	2026-02-10	WebSearch	Consultant Ops	ClosedCo	skipped_expired	
```

## careers_url Management

<rules>
- ALWAYS prefer a corporate careers page over a direct ATS URL — using ATS URLs directly can cause false 410 errors.
- ALWAYS save a newly found `careers_url` to `config/portals.yml` immediately.
</rules>

| Correct (corporate) | Incorrect as first option (direct ATS) |
|---|---|
| `https://careers.mastercard.com` | `https://mastercard.wd1.myworkdayjobs.com` |
| `https://openai.com/careers` | `https://job-boards.greenhouse.io/openai` |
| `https://stripe.com/jobs` | `https://jobs.lever.co/stripe` |

**Known URL patterns by platform:**

| Platform | Corporate/hosted URL | API/feed |
|---|---|---|
| Ashby | `https://jobs.ashbyhq.com/{slug}` | `https://api.ashbyhq.com/posting-api/job-board/{slug}?includeCompensation=true` |
| Greenhouse | `https://job-boards.greenhouse.io/{slug}` | `https://boards-api.greenhouse.io/v1/boards/{company}/jobs` |
| Lever | `https://jobs.lever.co/{slug}` | `https://api.lever.co/v0/postings/{company}?mode=json` |

<agent_instruction>
If `careers_url` is missing for a company:
1. Try the known pattern for their platform.
2. If that fails, run WebSearch: `"{company}" careers jobs`, navigate with Playwright to confirm.
3. Save the found URL in `config/portals.yml`.

If `careers_url` returns 404/redirect: note in output summary, try `scan_query` as fallback, flag for manual update.
</agent_instruction>

## portals.yml Maintenance

<agent_instruction>
- Save `careers_url` whenever a new company is added.
- Disable noisy queries with `enabled: false`.
- Adjust filter keywords as target roles evolve.
- Periodically verify `careers_url` — companies change ATS platforms.
</agent_instruction>

## Output

<output>
```
Portal Scan — {YYYY-MM-DD}
━━━━━━━━━━━━━━━━━━━━━━━━━━
Queries run: N
Offers found: N total
Filtered by title: N relevant
Duplicates: N (already evaluated or in pipeline)
Expired discarded: N (dead links, Level 3)
New added to pipeline.md: N

  + {company} | {title} | {query_name}
  ...
```
</output>

<completion>
<user_prompt>→ Run /career-ops pipeline to evaluate the new offers.</user_prompt>
</completion>
