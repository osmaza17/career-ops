# Mode: patterns -- Rejection Pattern Detector

## Purpose

Analyze all tracked applications to find patterns in outcomes and surface actionable insights. Identifies what's working (archetypes, remote policies, score ranges) and what's wasting time (geo-restricted roles, stack mismatches, low-score applications).

## Inputs

- `data/applications.md` — Application tracker
- `reports/` — Individual evaluation reports
- `config/profile.md` — User profile (for recommendation context)
- `config/profile.md` (`strategy.*` keys) — User archetypes and framing
- `config/portals.yml` — Portal config (for filter update recommendations)

## Minimum Threshold

Before running analysis, check: does `data/applications.md` have at least 5 entries with status beyond "Evaluated" (i.e., Applied, Responded, Interview, Offer, Rejected, Discarded, SKIP)?

If not, tell the user:
> "Not enough data yet -- {N}/5 applications have progressed beyond evaluation. Keep applying and come back when you have more outcomes to analyze."

Exit gracefully.

## Step 1 — Run Analysis Script

Execute:

```bash
node analyze-patterns.mjs
```

Parse the JSON output. It contains:

| Key | Contents |
|-----|----------|
| `metadata` | Total entries, date range, analysis date, counts by outcome |
| `funnel` | Count per status stage (evaluated, applied, interview, offer, etc.) |
| `scoreComparison` | Avg/min/max score per outcome group (positive, negative, self_filtered, pending) |
| `archetypeBreakdown` | Per-archetype: total, positive, negative, self_filtered, conversion rate |
| `blockerAnalysis` | Most frequent hard blockers: geo-restriction, skills-mismatch, seniority gap, onsite requirement, sector-mismatch |
| `remotePolicy` | Per-policy bucket: total, positive, negative, conversion rate |
| `companySizeBreakdown` | Per-size bucket: startup, scaleup, enterprise |
| `scoreThreshold` | Recommended minimum score + reasoning |
| `techStackGaps` | Most frequent skills or tool gaps in negative outcomes |
| `recommendations` | Top 5 actionable items with reasoning and impact level |

If the script returns `error`, display the error message and exit.

## Step 2 — Generate Report

Write the report to `reports/pattern-analysis-{YYYY-MM-DD}.md`.

### Report Structure

```markdown
# Pattern Analysis -- {YYYY-MM-DD}

**Applications analyzed:** {total}
**Date range:** {from} to {to}
**Outcomes:** {positive} positive, {negative} negative, {self_filtered} self-filtered, {pending} pending

---

## Conversion Funnel

Show each status with count and percentage of total. Use a simple table:

| Stage | Count | % |
|-------|-------|---|
| Evaluated | X | X% |
| Applied | X | X% |
| ... | | |

## Score vs Outcome

| Outcome | Avg Score | Min | Max | Count |
|---------|-----------|-----|-----|-------|
| Positive | X.X/5 | X.X | X.X | X |
| Negative | ... | | | |
| Self-filtered | ... | | | |
| Pending | ... | | | |

## Archetype Performance

Table with each archetype, total applications, positive outcomes, conversion rate.
Highlight the best-performing archetype and the worst.

## Top Blockers

Frequency table of recurring hard blockers (geo-restriction, sector-mismatch, seniority gap, onsite requirement, language requirement, required certification or diploma).
Note the percentage of all applications affected by each.

## Remote Policy Patterns

Table showing conversion rate by remote policy bucket (global, regional, geo-restricted, hybrid/onsite).

## Skills and Domain Gaps

List of most common missing skills, tools, or domain knowledge in negative/self-filtered outcomes with frequency.

## Recommended Score Threshold

State the data-driven minimum score and reasoning.

## Recommendations

Number the top recommendations (from the script output). For each:
1. **[IMPACT]** Action to take
   Reasoning behind the recommendation.
```

## Step 3 — Present Summary

Show the user a condensed version with:
1. One-line stat summary (X applications, Y% applied, Z% positive outcome)
2. Top 3 findings (most impactful patterns)
3. Link to full report

Example:
> **Pattern Analysis Complete** (24 applications, Apr 7-8)
>
> Key findings:
> - Geo-restricted roles are 0% conversion (7 of 24) -- stop evaluating US/Canada-only postings
> - Regional/global remote roles convert at 57-67% -- these are your sweet spot
> - No positive outcomes below 4.2/5 -- consider this your score floor
>
> Full report: `reports/pattern-analysis-2026-04-08.md`

## Step 4 — Offer to Apply Recommendations

Ask the user if they want to act on any recommendations:

> "Want me to apply any of these recommendations? I can:
> - Update `config/portals.yml` to filter out geo-restricted roles
> - Set a score threshold in `config/profile.md` for PDF generation
> - Adjust archetype targeting based on what's converting
>
> Just say which ones, or 'all' to apply everything."

If the user agrees:
- For portal filter changes: edit `config/portals.yml`
- For profile/archetype changes: edit `config/profile.md` under `strategy.*` (NEVER `_shared.md`)
- For score threshold: add to `config/profile.md` under `strategy.score_threshold` (e.g. `score_threshold: 4.2`). The `offer-analysis` mode reads this key — if present, it warns the user before evaluating offers that fall below the threshold.

## Outcome Classification

For reference, outcomes are classified as:

| Status | Outcome |
|--------|---------|
| Interview, Offer, Responded, Applied | **Positive** (invested effort or got traction) |
| Rejected, Discarded | **Negative** (company said no or offer closed) |
| SKIP | **Self-filtered** (user decided not to apply) |
| Evaluated | **Pending** (no action taken yet) |
