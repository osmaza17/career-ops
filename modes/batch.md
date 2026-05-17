# Mode: batch — Mass Processing of Jobs

<purpose>
Process multiple job postings in parallel using a conductor–worker architecture. Two sub-modes: **Mode A: Conductor --chrome** (navigates portals live via browser) and **Mode B: Standalone** (script-driven from a pre-collected URL list).
</purpose>

<rules>
- Always run `node merge-tracker.mjs` as the final step of any batch run.
- Workers operate in headless mode — use the correct CLI command from the **Headless / Batch Mode** table in `AGENTS.md`.
- Never edit `applications.md` directly to add new entries — write TSV lines to `batch/tracker-additions/` and let `merge-tracker.mjs` handle the merge.
- Each worker is self-contained with its own 200K token context; conductor only orchestrates.
- Evaluation logic follows `modes/offer-analysis.md` — do not duplicate it here.
</rules>

## Architecture

```text
Conductor (headed browser mode)
  │
  ├─ Job 1: reads JD from DOM + URL → headless worker → report .md + PDF + tracker-line
  ├─ Job 2: click next, read JD + URL → headless worker → report .md + PDF + tracker-line
  └─ End: merge tracker-additions → applications.md + summary
```

## Files

```text
batch/
  batch-input.tsv               # URLs (from conductor or manual)
  batch-state.tsv               # Progress (auto-generated, gitignored)
  batch-runner.sh               # Standalone orchestrator script
  batch-prompt.md               # Prompt template for workers
  logs/                         # One log per job (gitignored)
  tracker-additions/            # Tracker lines (gitignored)
```

## Mode A: Conductor --chrome

<process>

<step id="1" name="Read state">
<agent_instruction>Read `batch/batch-state.tsv` to identify already-processed jobs and skip them.</agent_instruction>
</step>

<step id="2" name="Navigate and extract">
<agent_instruction>Open Chrome → navigate to search URL → read results DOM → extract URL list → append new URLs to `batch-input.tsv`.</agent_instruction>
</step>

<step id="3" name="Process each pending URL">
<agent_instruction>
For each pending URL:
a. Chrome: click job → read JD text from DOM
b. Save JD to `/tmp/batch-jd-{id}.txt`
c. Calculate next sequential REPORT_NUM
d. Execute headless worker via Bash (use CLI command from `AGENTS.md`):
   ```bash
   <headless-cmd> "Process this job. URL: {url}. JD: /tmp/batch-jd-{id}.txt. Report: {num}. ID: {id}"
   ```
e. Update `batch-state.tsv` (status + score + report_num)
f. Log to `logs/{report_num}-{id}.log`
g. Chrome: go back → next job
</agent_instruction>
</step>

<step id="4" name="Pagination">
<agent_instruction>When results are exhausted, click "Next" and repeat Step 3.</agent_instruction>
</step>

<step id="5" name="Finalize">
<agent_instruction>Run `node merge-tracker.mjs` to merge all tracker additions into `applications.md`.</agent_instruction>
<user_prompt>Batch complete. {N} jobs processed. Summary: [table of company / score / report link]</user_prompt>
</step>

</process>

## Mode B: Standalone script

```bash
batch/batch-runner.sh [OPTIONS]
```

Options:
- `--dry-run` — list pending jobs without executing
- `--retry-failed` — retry only failed jobs
- `--start-from N` — start from ID N
- `--parallel N` — N workers in parallel
- `--max-retries N` — attempts per job (default: 2)
- `--min-score N` — skip PDF generation for jobs scoring below N (default: 0 = generate all PDFs)

<agent_instruction>`merge-tracker.mjs` runs automatically after `batch-runner.sh` completes. If the script fails mid-run, the user can run `node merge-tracker.mjs` manually to merge any completed tracker additions.</agent_instruction>

## batch-state.tsv Format

<format>
```text
id	url	status	started_at	completed_at	report_num	score	error	retries
1	https://...	completed	2026-...	2026-...	002	4.2	-	0
2	https://...	failed	2026-...	2026-...	-	-	Error msg	1
3	https://...	processing	2026-...	-	-	-	-	0
```

Status values written by `batch-runner.sh`: `processing` (worker running), `completed`, `failed`, `skipped` (below min-score). Jobs not yet started have no row — they are not pre-populated as `pending`.
</format>

## Workers (headless mode)

<agent_instruction>Each worker receives `batch-prompt.md` as system prompt. It is self-contained — use the headless CLI command from `AGENTS.md`.</agent_instruction>

<output>
Each worker produces:
1. `.md` report → `output/reports/`
2. PDF → `output/CVs/`
3. Tracker TSV line → `batch/tracker-additions/{id}.tsv`
4. Result JSON via stdout
</output>

## Resumability

<agent_instruction>
- On crash/restart: re-read `batch-state.tsv` → skip completed jobs automatically.
- Lock file (`batch-runner.pid`) prevents double execution.
- Worker failures are isolated — job #47 failing does not affect others.
</agent_instruction>

## Error Handling

| Error | Recovery |
|-------|----------|
| URL inaccessible | Worker fails → mark `failed`, continue |
| JD behind login | Conductor reads DOM; if fails → mark `failed` |
| Portal layout change | Conductor reasons about HTML and adapts |
| Worker crashes | Mark `failed`, continue; retry with `--retry-failed` |
| Conductor crashes | Re-run → reads state → skips completed |
| PDF fails | `.md` report saved; PDF remains pending |
