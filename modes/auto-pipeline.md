# Mode: auto-pipeline — Full Automatic Pipeline

<purpose>
When the user pastes one or more JDs (text or URLs) without an explicit sub-command, spawn one background agent per URL/JD. Each agent independently runs the full pipeline: extract → evaluate → report → PDF → tracker. The conductor assigns report numbers, launches agents, waits for completion, then merges the tracker and shows a summary.
</purpose>

<rules>
- NEVER run the pipeline inline — always spawn one agent per URL/JD, even for a single input.
- NEVER write directly to `data/applications.md` — always use the TSV → merge pipeline.
- Always match output language to the JD language (EN default).
- PDF only if score ≥ 3.0.
- Pre-assign all report numbers before spawning any agent — prevents numbering conflicts.
</rules>

---

## Conductor Steps

<process>

<step id="0" name="Pre-flight">
<agent_instruction>
First evaluation of the session: run `node cv-sync-check.mjs`. Notify user if any warnings are returned. If score falls below `strategy.score_threshold` in `config/profile.md` (default 3.5), surface a warning after evaluation — do not block the pipeline, but make the recommendation explicit.
</agent_instruction>
</step>

<step id="1" name="Collect inputs">
<agent_instruction>
Detect all URLs and/or JD text blocks in the user's message. Each distinct URL or pasted JD block = one job. Note the count.
</agent_instruction>
</step>

<step id="2" name="Pre-assign report numbers">
<agent_instruction>
List files in `output/reports/`, find the current max numeric prefix, and assign sequential REPORT_NUMs (max+1, max+2, …) — one per job. Do this before spawning any agent.
</agent_instruction>
</step>

<step id="3" name="Spawn one agent per job">
<agent_instruction>
For each job, launch a background agent (Agent tool, `run_in_background: true`). Fill all placeholders before spawning — never spawn with an unresolved placeholder.
</agent_instruction>

<format>
```
Agent(
  subagent_type="general-purpose",
  run_in_background=True,
  prompt="[content of modes/_shared.md]\n\n[content of modes/offer-analysis.md]\n\n[content of modes/pdf.md]\n\nYour assigned report number is {REPORT_NUM}. Process this job:\n\nURL: {url_or_none}\n\n{jd_text_if_pasted}\n\nRun the full pipeline:\n1. Extract JD (Playwright → WebFetch → WebSearch)\n2. Evaluate Blocks A–G (offer-analysis.md)\n3. Save report to output/reports/{REPORT_NUM}-{company-slug}-{date}.md\n4. Generate PDF (pdf.md) if score >= 3.0\n5. Write tracker TSV to batch/tracker-additions/{REPORT_NUM}-{company-slug}.tsv",
  description="auto-pipeline #{REPORT_NUM}: {url_or_company}"
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
</agent_instruction>
</step>

<step id="5" name="Summary">
<completion>
Show one row per processed job:

| # | Company | Role | Score | PDF | Report |
|---|---------|------|-------|-----|--------|
</completion>
</step>

</process>
