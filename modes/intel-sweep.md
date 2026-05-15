# Mode: intel-sweep — Parallel Company Intelligence Sweep

## Purpose

Research N companies simultaneously, one agent per company. Each agent executes the `deep` research axes (not just generates the prompt — actually runs WebSearch and synthesizes findings). The conductor aggregates results into a ranked shortlist.

## When to use

User wants to research a batch of companies before deciding where to apply, or wants to build a prioritized target list from `config/portals.yml`.

---

## Conductor Steps

### Step 1 — Collect companies

Accept company list from:
- User message (inline list of company names)
- `config/portals.yml` — extract the `companies` array if present

Show the list for confirmation and ask once:

```
Will research N companies in parallel:
1. {Company A}
2. {Company B}
...

What role are you targeting at these companies? (Used to personalize the candidate-fit angle for each.)

Confirm the list, or add/remove companies before we start.
```

Wait for confirmation and role answer.

### Step 2 — Read candidate context

Read `config/profile.md` (YAML frontmatter: `target_roles`, `narrative.proof_points`, `narrative.superpowers`) and `config/strategy.md` (archetype framing). Extract the top 2–3 proof points and the primary target archetype. Pass these to all workers so they can personalize Axis 7 (Candidate angle) of the deep research.

### Step 3 — Spawn one research agent per company

For each company, spawn a background agent. Use WebSearch — no Playwright needed, safe to fully parallelize.

Worker prompt (fill in placeholders before spawning):

```
You are a company research agent. Research {company} for a candidate targeting {role} roles.

CANDIDATE CONTEXT:
  Target role: {role}
  Primary archetype: {archetype}
  Top proof points:
    - {proof_point_1}
    - {proof_point_2}

READ THIS FILE: modes/deep.md  (axis structure and sector-specific questions — use as a RESEARCH GUIDE, not as output to generate. You will execute the research yourself using WebSearch, not emit the prompt template.)

TASK:
1. Detect {company}'s sector and company type (listed/private/PE-backed/startup/institution).
2. Execute deep research — run WebSearch for each axis from modes/deep.md (business model,
   strategic direction, recent moves, culture, challenges, the role, candidate angle).
   Use the sector-specific questions block that matches the detected sector.
   Do NOT output the prompt template — answer the questions yourself from search results.
3. Write the full research doc to:
     reports/intel-{company-slug}-{YYYY-MM-DD}.md
   where {company-slug} = company name lowercase hyphenated.
4. Return a JSON summary:
   {
     "company": "{company}",
     "sector": "{detected sector}",
     "company_type": "{listed|private|pe-backed|startup|institution}",
     "fit_score": {1-5},
     "fit_rationale": "{one sentence: why this score}",
     "top_signal": "{strongest reason to apply}",
     "red_flag": "{biggest concern, or null}",
     "report": "reports/intel-{company-slug}-{date}.md"
   }

fit_score rubric:
  5 = company type, growth stage, and role scope align perfectly with candidate archetype
  4 = strong fit, minor misalignment on one axis
  3 = decent fit, worth investigating further
  2 = weak fit, significant mismatch on culture or role scope
  1 = poor fit, apply only for specific reasons

If the company is not findable via WebSearch (very private, no public data):
  Return fit_score: null and note "Insufficient public data" in fit_rationale.
```

Spawn all workers in parallel (background). No limit on count here — WebSearch workers are lightweight.

### Step 4 — Wait and collect results

Wait for all workers to complete. Parse their JSON summaries.

### Step 5 — Present ranked shortlist

Sort companies by `fit_score` descending. Present:

```
## Company Intelligence Sweep — {date}
Role targeted: {role}

| Rank | Company | Sector | Fit | Top Signal | Red Flag | Report |
|------|---------|--------|-----|------------|----------|--------|
| 1 | {company} | {sector} | {score}/5 | {signal} | {flag or —} | [intel]({path}) |
| ... | | | | | | |

**Recommended targets (fit ≥ 4):** {list}
**Research before deciding (fit 3):** {list}
**Low priority (fit ≤ 2):** {list}

Full research docs saved to reports/intel-{slug}-{date}.md for each company.
```

Offer: "Want me to run `parallel-eval` on the recommended companies? Paste the job URLs and I'll evaluate them all at once."

---

## Notes

- `fit_score` is the agent's directional assessment of company-to-archetype fit, not an application score. Use it to prioritize research time, not to make final apply/don't-apply decisions.
- Workers use WebSearch only. No Playwright, no TSV, no tracker updates — intel sweep is research only.
- Research docs are saved to `reports/` with the `intel-` prefix to distinguish them from evaluation reports.
- If a company is in `config/portals.yml`, the worker can also check that portal's known job listings for context.
