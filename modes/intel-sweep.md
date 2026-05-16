# Mode: intel-sweep — Parallel Company Intelligence Sweep

<purpose>
Research N companies simultaneously, one agent per company. Each agent executes the `deep` research axes (runs WebSearch and synthesizes findings — does not just emit a prompt). The conductor aggregates results into a ranked shortlist.
</purpose>

<rules>
- Workers use WebSearch only — no Playwright, no TSV, no tracker updates.
- Research docs are saved to `reports/` with the `intel-` prefix (e.g. `intel-acme-2026-05-17.md`).
- `fit_score` is a directional company-to-archetype signal, not an apply/don't-apply decision.
- If a company appears in `config/portals.yml`, the worker may also check its known job listings for context.
</rules>

---

## Conductor Steps

<process>

<step id="1" name="Collect companies">

<agent_instruction>
Accept company list from the user message (inline) or from the `companies` array in `config/portals.yml`.
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

READ THIS FILE: modes/deep.md  (axis structure and sector-specific questions — use as a RESEARCH GUIDE, not as output to generate. Execute the research yourself using WebSearch.)

TASK:
1. Detect {company}'s sector and company type (listed/private/PE-backed/startup/institution).
2. Run WebSearch for each axis from modes/deep.md (business model, strategic direction, recent moves,
   culture, challenges, the role, candidate angle). Use the sector-specific questions block that
   matches the detected sector. Do NOT output the prompt template — answer from search results.
3. Write the full research doc to:
     reports/intel-{company-slug}-{YYYY-MM-DD}.md
4. Return a JSON summary:
   {
     "company": "{company}",
     "sector": "{detected sector}",
     "company_type": "{listed|private|pe-backed|startup|institution}",
     "fit_score": {1-5},
     "fit_rationale": "{one sentence}",
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

Full research docs saved to reports/intel-{slug}-{date}.md for each company.
</output>

<completion>
Want me to run `parallel-eval` on the recommended companies? Paste the job URLs and I'll evaluate them all at once.
</completion>

</step>

</process>
