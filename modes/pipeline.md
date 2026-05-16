# Mode: pipeline — URL Inbox

<purpose>
Process offer URLs accumulated in `data/pipeline.md`. The user adds URLs at will; `/career-ops pipeline` processes them all in batch.
</purpose>

<rules>
- Never skip `- [ ]` items in the Pending section without marking them.
- Inaccessible URLs → mark `- [!]` with a note; continue to next item.
- Use parallel agents (Agent tool, `run_in_background`) when 3+ URLs are pending.
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

<step id="2" name="Load pending URLs">
<agent_instruction>
Read `data/pipeline.md` → collect all `- [ ]` items from the Pending section.
If 3+ items are pending, launch one agent per URL in parallel (Agent tool, `run_in_background`).
</agent_instruction>
</step>

<step id="3" name="Assign report number">
<agent_instruction>
For each URL: list files in `reports/`, extract the numeric prefix (e.g. `142-…` → 142), set REPORT_NUM = max + 1.
</agent_instruction>
</step>

<step id="4" name="Extract JD">
<agent_instruction>
Attempt extraction in order:
1. Playwright: `browser_navigate` + `browser_snapshot` (preferred; works with SPAs).
2. WebFetch: fallback for static pages or when Playwright is unavailable.
3. WebSearch: last resort — search secondary portals that index the JD.

Special cases:
- **LinkedIn**: may require login → mark `[!]`, ask user to paste the text.
- **PDF URL**: read directly with the Read tool.
- **`local:` prefix**: read the local file (e.g. `local:jds/file.md` → read `jds/file.md`).

If URL is not accessible → mark `- [!]` with a brief note; skip to next item.
</agent_instruction>
</step>

<step id="5" name="Evaluate and produce outputs">
<agent_instruction>
Run the full auto-pipeline per `modes/offer-analysis.md`: Blocks A–F → Report .md → PDF (if score ≥ 3.0) → Tracker TSV.
</agent_instruction>
</step>

<step id="6" name="Update pipeline.md">
<agent_instruction>
Move the item from Pending to Processed using the format below.
</agent_instruction>

<format>
```
- [x] #NNN | {url} | {Company} | {Role} | {Score}/5 | PDF ✅/❌
```
</format>
</step>

<step id="7" name="Summary">
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
