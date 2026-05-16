# Mode: parallel-eval — Parallel Evaluation of Multiple Job URLs

## Purpose

Evaluate N job URLs simultaneously using one headless worker per URL. Delivers N full A-G reports in the time it takes to do one. Same quality as running `offer-analysis` N times sequentially.

## When to use

User pastes 2+ URLs at once, or has pending URLs in `data/pipeline.md` to clear in bulk.

---

## Conductor Steps

### Step 1 — Collect URLs

Accept URLs from:
- User message (pasted inline)
- `data/pipeline.md` (entries with status `pending`)

**Guard:** If only 1 URL is found and the user did not explicitly invoke `parallel-eval`, delegate to `auto-pipeline` instead — do not prompt for parallel evaluation.

List them for confirmation:

```
Found N URLs to evaluate in parallel:
1. {url1}
2. {url2}
...

Proceed? (Remove any you don't want to run now.)
```

Wait for confirmation before spawning.

### Step 2 — Assign report numbers

Read `reports/` to find the current max report number. Assign sequential numbers: max+1, max+2, … max+N. Reserve all numbers before spawning — workers must not overlap.

### Step 3 — Spawn one worker per URL

For each URL, spawn a headless background worker. Use the CLI headless command from the **Headless / Batch Mode** table in `AGENTS.md`.

Worker prompt (fill in placeholders before spawning):

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
  config/profile.md       candidate identity, proof points, targets
  config/profile.md       CV sections and candidate data (match against JD requirements)
  config/profile.md       user archetypes (target_roles.archetypes, strategy.*) and negotiation context (strategy.negotiation_scripts)

VERIFICATION NOTE: You are running in headless parallel mode. Use WebFetch
to read the JD. Mark the report header with:
  **Verification:** unconfirmed (parallel mode)
Do NOT attempt Playwright — it cannot run in parallel.

TASK:
1. WebFetch the URL. If inaccessible, return {"status": "failed", "reason": "URL inaccessible"}.
2. Run the full A-G evaluation from modes/offer-analysis.md.
3. Write the report to:
     reports/{num}-{slug}-{date}.md
   where {slug} = company name lowercase hyphenated.
4. Write the tracker TSV to:
     batch/tracker-additions/{num}-{slug}.tsv
   Follow the 9-column TSV format from AGENTS.md exactly.
5. Return a single JSON line to stdout:
   {"num":"{num}","company":"{company}","role":"{role}","score":{X.X},"legitimacy":"{tier}","url":"{url}","status":"ok"}

Do NOT run merge-tracker.mjs — the conductor handles that after all workers complete.
```

Spawn all workers in parallel (background). Do not wait for one before spawning the next.

### Step 4 — Wait and collect results

Wait for all workers to complete. Parse their JSON result lines.

For failed workers (status: "failed"), record the reason.

### Step 5 — Merge tracker

```bash
node merge-tracker.mjs
```

### Step 6 — Summary table

```
## Parallel Evaluation — {date} — {N} jobs

| # | Company | Role | Score | Legitimacy | Report |
|---|---------|------|-------|------------|--------|
| {num} | {company} | {role} | {score}/5 | {tier} | [{num}](reports/{path}) |
| ... | | | | | |

Tracker merged. {n_ok} completed, {n_failed} failed.
```

Flag any score ≥ 4.0 with **→ Recommended**. Flag any "Suspicious" legitimacy with **⚠ Verify first**.

Offer to retry failed URLs if any.

---

## Limits and constraints

- **Maximum 5 workers in parallel.** Beyond that, rate limits and memory become unreliable. For larger batches, split into groups of 5 and run sequentially.
- **Playwright is disabled for workers.** All JD reading uses WebFetch. Legitimacy verification is marked "unconfirmed" — the user can open any report and verify manually.
- **Report numbers are reserved upfront.** Workers must not compute their own numbers — race conditions will produce duplicates.
- **Worker failure is isolated.** One failure does not affect other workers. The conductor continues and reports failures in the summary.
