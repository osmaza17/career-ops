# Mode: patterns — Rejection Pattern Detector

<purpose>
Analyze all tracked applications to find patterns in outcomes and surface actionable insights. Identifies what is working (archetypes, remote policies, score ranges) and what is wasting time (geo-restricted roles, stack mismatches, low-score applications).
</purpose>

<rules>
- Edit `config/profile.md` under `strategy.*` for any profile changes. NEVER edit `modes/_shared.md`.
- For portal filter changes, edit `config/portals.yml`.
- Do not run analysis if the data threshold is not met (see Step 1).
</rules>

## Inputs

- `data/applications.md` — application tracker
- `output/reports/` — individual evaluation reports
- `config/profile.md` — user profile and `strategy.*` keys
- `config/portals.yml` — portal config (for filter update recommendations)

---

## Process

<process>

### Step 1 — Check Data Threshold

<step id="1" name="Check Data Threshold">
<agent_instruction>
Count entries in `data/applications.md` with status beyond "Evaluated" (i.e., Applied, Responded, Interview, Offer, Rejected, Discarded, SKIP). Minimum required: 5.
</agent_instruction>

If threshold is not met:
<user_prompt>
"Not enough data yet — {N}/5 applications have progressed beyond evaluation. Keep applying and come back when you have more outcomes to analyze."
</user_prompt>

Exit gracefully.
</step>

### Step 2 — Run Analysis Script

<step id="2" name="Run Analysis Script">
<agent_instruction>
Execute:

```bash
node analyze-patterns.mjs
```

Parse the JSON output:

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
</agent_instruction>
</step>

### Step 3 — Generate Report

<step id="3" name="Generate Report">
<agent_instruction>
Write the report to `output/reports/pattern-analysis-{YYYY-MM-DD}.md`.
</agent_instruction>

<format>
```markdown
# Pattern Analysis — {YYYY-MM-DD}

**Applications analyzed:** {total}
**Date range:** {from} to {to}
**Outcomes:** {positive} positive, {negative} negative, {self_filtered} self-filtered, {pending} pending

---

## Conversion Funnel

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

Table with each archetype: total applications, positive outcomes, conversion rate.
Highlight best-performing and worst-performing archetype.

## Top Blockers

Frequency table of recurring hard blockers (geo-restriction, sector-mismatch, seniority gap, onsite requirement, language requirement, required certification or diploma).
Note the percentage of all applications affected by each.

## Remote Policy Patterns

Table showing conversion rate by remote policy bucket (global, regional, geo-restricted, hybrid/onsite).

## Skills and Domain Gaps

Most common missing skills, tools, or domain knowledge in negative/self-filtered outcomes with frequency.

## Recommended Score Threshold

Data-driven minimum score and reasoning.

## Recommendations

1. **[IMPACT]** Action to take
   Reasoning behind the recommendation.
```
</format>
</step>

### Step 4 — Present Summary

<step id="4" name="Present Summary">
<user_prompt>
"**Pattern Analysis Complete** ({total} applications, {date range})

Key findings:
- {finding 1}
- {finding 2}
- {finding 3}

Full report: `output/reports/pattern-analysis-{YYYY-MM-DD}.md`"
</user_prompt>

<agent_instruction>
Show one-line stat summary, top 3 most impactful findings, and a link to the full report.
</agent_instruction>
</step>

### Step 5 — Offer to Apply Recommendations

<step id="5" name="Offer to Apply Recommendations">
<user_prompt>
"Want me to apply any of these recommendations? I can:
- Update `config/portals.yml` to filter out geo-restricted roles
- Set a score threshold in `config/profile.md` for PDF generation
- Adjust archetype targeting based on what's converting

Just say which ones, or 'all' to apply everything."
</user_prompt>

<agent_instruction>
If the user agrees, apply changes:
- Portal filter changes → edit `config/portals.yml`
- Profile/archetype changes → edit `config/profile.md` under `strategy.*`
- Score threshold → add `strategy.score_threshold: X.X` to `config/profile.md`. The `offer-analysis` mode reads this key and warns the user when offers fall below it.
</agent_instruction>
</step>

</process>

---

## Reference: Outcome Classification

| Status | Outcome |
|--------|---------|
| Interview, Offer, Responded, Applied | Positive (invested effort or got traction) |
| Rejected, Discarded | Negative (company said no or offer closed) |
| SKIP | Self-filtered (user decided not to apply) |
| Evaluated | Pending (no action taken yet) |
