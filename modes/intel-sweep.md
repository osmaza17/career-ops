# Mode: intel-sweep — Parallel Company Intelligence Sweep

<purpose>
Research N companies simultaneously, one agent per company. Each agent runs WebSearch across 7 research axes and synthesizes findings. The conductor aggregates results into a ranked shortlist.
</purpose>

<rules>
- Workers use WebSearch only — no Playwright, no TSV, no tracker updates.
- Research docs are saved to `output/reports/` with the `intel-` prefix (e.g. `intel-acme-2026-05-17.md`).
- `fit_score` is a directional company-to-archetype signal, not an apply/don't-apply decision.
- If a company appears in `config/portals.yml`, the worker may also check its known job listings for context.
</rules>

---

## Conductor Steps

<process>

<step id="1" name="Collect companies">

<agent_instruction>
Accept company list from the user message (inline) or from the `tracked_companies` array in `config/portals.yml`.
</agent_instruction>

<user_prompt>
Will research {N} companies in parallel:
1. {Company A}
2. {Company B}
...

What role are you targeting at these companies? (Used to personalize the candidate-fit angle for each.)

Confirm the list, or add/remove companies before we start.
</user_prompt>

<agent_instruction>Wait for confirmation and role answer before proceeding.</agent_instruction>

</step>

<step id="2" name="Read candidate context">

<agent_instruction>
Read `config/profile.md` (YAML frontmatter: `target_roles`, `narrative.proof_points`, `narrative.superpowers`, `strategy.adaptive_framing`). Extract the top 2–3 proof points and primary target archetype. Pass these to all workers so they can personalize Axis 7 (Candidate angle).
</agent_instruction>

</step>

<step id="3" name="Spawn research agents">

<agent_instruction>
Spawn one background agent per company in parallel (WebSearch only — safe to fully parallelize, no count limit). Fill all placeholders before spawning.
</agent_instruction>

<format>
Worker prompt:

```
You are a company research agent. Research {company} for a candidate targeting {role} roles.

CANDIDATE CONTEXT:
  Target role: {role}
  Primary archetype: {archetype}
  Top proof points:
    - {proof_point_1}
    - {proof_point_2}

RESEARCH AXES (answer from search results — do not output a prompt template):
  1. Business model: What does the company do, revenue model, size, ownership, geographies?
  2. Strategic direction: Stated priorities next 2–3 years, growth vs. consolidation, major bets?
  3. Recent moves (last 6–12 months): Leadership hires, acquisitions, funding, restructuring?
  4. Culture and environment: Glassdoor/LinkedIn reviews, remote/hybrid policy, career trajectory?
  5. Challenges and pressures: Main business pressures, competitors, pain points, sector growth rate?
  6. The role and team: Day-to-day work, why hiring now, success metrics in 6–12 months?
  7. Candidate angle: What unique value does this candidate bring given the company's current priorities?

  For Axis 1 and Axis 4, adapt questions to the detected sector (consulting, industrial/manufacturing,
  logistics, finance, energy, engineering services, tech, or other) — focus on sector-specific
  business model levers, staffing norms, and operational context.

TASK:
1. Detect {company}'s sector and company type (listed/private/PE-backed/startup/institution).
2. Run WebSearch for each axis above. Use the sector-specific lens for Axis 1 and 4.
   Do NOT output a prompt template — answer from search results.
3. Write the full research doc to:
     output/reports/intel-{company-slug}-{YYYY-MM-DD}.md
4. Return a JSON summary:
   {
     "company": "{company}",
     "sector": "{detected sector}",
     "company_type": "{listed|private|pe-backed|startup|institution}",
     "fit_score": {1-5},
     "fit_rationale": "{one sentence}",
     "top_signal": "{strongest reason to apply}",
     "red_flag": "{biggest concern, or null}",
     "report": "output/reports/intel-{company-slug}-{date}.md"
   }

fit_score rubric:
  5 = company type, growth stage, and role scope align perfectly with candidate archetype
  4 = strong fit, minor misalignment on one axis
  3 = decent fit, worth investigating further
  2 = weak fit, significant mismatch on culture or role scope
  1 = poor fit, apply only for specific reasons

If the company has insufficient public data: fit_score: null, fit_rationale: "Insufficient public data".
```
</format>

</step>

<step id="4" name="Collect results">

<agent_instruction>Wait for all workers to complete, then parse their JSON summaries.</agent_instruction>

</step>

<step id="5" name="Present ranked shortlist">

<agent_instruction>Sort companies by `fit_score` descending.</agent_instruction>

<output>
## Company Intelligence Sweep — {date}
Role targeted: {role}

| Rank | Company | Sector | Fit | Top Signal | Red Flag | Report |
|------|---------|--------|-----|------------|----------|--------|
| 1 | {company} | {sector} | {score}/5 | {signal} | {flag or —} | [intel]({path}) |

**Recommended targets (fit ≥ 4):** {list}
**Research before deciding (fit 3):** {list}
**Low priority (fit ≤ 2):** {list}

Full research docs saved to output/reports/intel-{slug}-{date}.md for each company.
</output>

<completion>
Paste any of the recommended job URLs to run a full evaluation.
</completion>

</step>

</process>
