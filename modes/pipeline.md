# Mode: pipeline — URL Inbox

<purpose>
Process offer URLs accumulated in `data/pipeline.md`. The user adds URLs at will; `/career-ops pipeline` processes them all — always one background agent per URL, regardless of count.
</purpose>

<rules>
- Never skip `- [ ]` items in the Pending section without marking them.
- Inaccessible URLs → mark `- [!]` with a note; continue to next item.
- Always spawn one background agent per URL (Agent tool, `run_in_background`) — even for a single pending URL.
- Pre-assign all report numbers before spawning any agent — prevents numbering conflicts.
- Run `node cv-sync-check.mjs` before processing any URL; warn the user if out of sync.
- PDF only if score >= 3.0.
- Never trust WebSearch/WebFetch alone for offer liveness — prefer Playwright.
</rules>

## Workflow

<process>

<step id="1" name="Sync check">
<agent_instruction>
Run `node cv-sync-check.mjs`. If out of sync, warn the user before continuing.
</agent_instruction>
</step>

<step id="2" name="Load pending URLs and pre-assign report numbers">
<agent_instruction>
Read `data/pipeline.md` → collect all `- [ ]` items from the Pending section.
List files in `output/reports/`, find the current max numeric prefix, and assign sequential REPORT_NUMs (max+1, max+2, …) — one per URL. Do this before spawning any agent.
</agent_instruction>
</step>

<step id="3" name="Spawn one agent per URL">
<agent_instruction>
For each pending URL, launch a background agent (Agent tool, `run_in_background: true`). Fill all placeholders before spawning.
</agent_instruction>

<format>
```
Agent(
  subagent_type="general-purpose",
  run_in_background=True,
  prompt="[content of modes/_shared.md]\n\n[content of modes/offer-analysis.md]\n\n[content of modes/pdf.md]\n\nYour assigned report number is {REPORT_NUM}. Process this job:\n\nURL: {url}\n\nIf the URL starts with 'local:jds/', read the file at that path (e.g. local:jds/acme-pm.md → read jds/acme-pm.md) instead of fetching a URL.\n\nRun the full pipeline:\n1. Extract JD (Playwright → WebFetch → local file). If inaccessible, return {url} as failed.\n2. Evaluate Blocks A–G (offer-analysis.md)\n3. Save report to output/reports/{REPORT_NUM}-{company-slug}-{date}.md\n4. Generate PDF (pdf.md) if score >= 3.0\n5. Write tracker TSV to batch/tracker-additions/{REPORT_NUM}-{company-slug}.tsv",
  description="pipeline #{REPORT_NUM}: {url}"
)
```
</format>
</step>

<step id="4" name="Wait and merge">
<agent_instruction>
After all agents complete, run:
```bash
node merge-tracker.mjs
```
Mark each URL in `data/pipeline.md` as processed or failed based on agent results.
</agent_instruction>
</step>

<step id="5" name="Update pipeline.md">
<agent_instruction>
Move the item from Pending to Processed using the format below.
</agent_instruction>

<format>
```
- [x] #NNN | {url} | {Company} | {Role} | {Score}/5 | PDF ✅/❌
```
</format>
</step>

<step id="6" name="Summary">
<completion>
Show summary table after all items are processed:

| # | Company | Role | Score | PDF | Recommended action |
|---|---------|------|-------|-----|--------------------|
</completion>
</step>

</process>

## pipeline.md Format

<format>
```markdown
## Pending
- [ ] https://jobs.example.com/posting/123
- [ ] https://boards.greenhouse.io/company/jobs/456 | Company Inc | Senior PM
- [!] https://private.url/job — Error: login required

## Processed
- [x] #143 | https://jobs.example.com/posting/789 | Acme Corp | Supply Chain Analyst | 4.2/5 | PDF ✅
- [x] #144 | https://boards.greenhouse.io/xyz/jobs/012 | BigCo | Operations Engineer | 2.1/5 | PDF ❌
```
</format>
