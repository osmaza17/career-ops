# Mode: parallel-eval — Parallel Evaluation of Multiple Job URLs

<purpose>
Evaluate N job URLs simultaneously using one headless worker per URL. Delivers N full A-G reports in the time it takes to do one. Same quality as running `offer-analysis` N times sequentially.
</purpose>

<rules>
- Maximum 5 workers in parallel. For larger batches, split into groups of 5 and run sequentially.
- Playwright is disabled for workers — all JD reading uses WebFetch; mark reports `**Verification:** unconfirmed (parallel mode)`.
- Report numbers are reserved upfront by the conductor — workers must not compute their own (race condition).
- Worker failure is isolated; one failure does not stop others.
- Workers must NOT run `merge-tracker.mjs` — the conductor does it after all workers complete.
</rules>

---

## Conductor Steps

### Step 1 — Collect URLs

<agent_instruction>
Accept URLs from the user message (pasted inline) or from `data/pipeline.md` (entries with status `pending`).

Guard: if only 1 URL is found and the user did not explicitly invoke `parallel-eval`, delegate to `auto-pipeline` — do not prompt for parallel evaluation.
</agent_instruction>

<user_prompt>
Found {N} URLs to evaluate in parallel:
1. {url1}
2. {url2}
...

Proceed? (Remove any you don't want to run now.)
</user_prompt>

<agent_instruction>Wait for confirmation before spawning.</agent_instruction>

### Step 2 — Assign report numbers

<agent_instruction>
Read `reports/` to find the current max report number. Assign sequential numbers max+1 … max+N and reserve them all before spawning any worker.
</agent_instruction>

### Step 3 — Spawn one worker per URL

<agent_instruction>
For each URL, spawn a headless background worker using the CLI headless command from the **Headless / Batch Mode** table in `AGENTS.md`. Spawn all workers in parallel — do not wait for one before spawning the next.

Worker prompt (fill placeholders before spawning):
</agent_instruction>

```
You are a career evaluation agent. Fully evaluate this job posting.

INPUTS:
  URL: {url}
  Report number: {num}  (3-digit zero-padded, e.g. "007")
  Date: {YYYY-MM-DD}
  Working directory: {absolute path to career-ops root}

READ THESE FILES FIRST (required — do not skip):
  modes/_shared.md        scoring rules, archetype definitions, global rules
  modes/offer-analysis.md evaluation template blocks A-G
  config/profile.md       candidate identity, CV sections, proof points, archetypes, negotiation context

VERIFICATION NOTE: Running in headless parallel mode. Use WebFetch to read the JD.
Mark the report header with:
  **Verification:** unconfirmed (parallel mode)
Do NOT attempt Playwright — it cannot run in parallel.

TASK:
1. WebFetch the URL. If inaccessible, return {"status":"failed","reason":"URL inaccessible"}.
2. Run the full A-G evaluation from modes/offer-analysis.md.
3. Write the report to: reports/{num}-{slug}-{date}.md
   where {slug} = company name lowercase hyphenated.
4. Write the tracker TSV to: batch/tracker-additions/{num}-{slug}.tsv
   Follow the 9-column TSV format from AGENTS.md exactly.
5. Return a single JSON line to stdout:
   {"num":"{num}","company":"{company}","role":"{role}","score":{X.X},"legitimacy":"{tier}","url":"{url}","status":"ok"}
```

### Step 4 — Wait and collect results

<agent_instruction>
Wait for all workers to complete. Parse their JSON result lines. Record the reason for any worker with `"status":"failed"`.
</agent_instruction>

### Step 5 — Merge tracker

<agent_instruction>Run `node merge-tracker.mjs`.</agent_instruction>

### Step 6 — Summary

<completion>
## Parallel Evaluation — {date} — {N} jobs

| # | Company | Role | Score | Legitimacy | Report |
|---|---------|------|-------|------------|--------|
| {num} | {company} | {role} | {score}/5 | {tier} | [{num}](reports/{path}) |

Tracker merged. {n_ok} completed, {n_failed} failed.
</completion>

<agent_instruction>
Flag any score ≥ 4.0 with **→ Recommended**. Flag any "Suspicious" legitimacy with **⚠ Verify first**. Offer to retry failed URLs if any.
</agent_instruction>

<output>
- `reports/{num}-{slug}-{date}.md` — one full A-G report per URL
- `batch/tracker-additions/{num}-{slug}.tsv` — one TSV per URL
- Tracker merged into `data/applications.md` via `merge-tracker.mjs`
</output>
