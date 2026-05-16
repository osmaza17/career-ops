# Mode: project — Portfolio Project Evaluation

<purpose>
Evaluate a proposed or existing portfolio project across 6 dimensions, return a scored verdict, and produce a build plan or pivot recommendation.
</purpose>

<rules>
- Never recommend BUILD for a project scoring below 3.0/5 without flagging the risk.
- Always include a STAR story angle — the project must be interview-tellable.
- Do not recommend 3+ month MVPs unless the signal value is exceptional.
</rules>

<scoring>

## Scoring Grid (6 dimensions, 1–5 each)

| Dimension | Weight | 5 = … | 1 = … |
|---|---|---|---|
| Signal for target roles | 25% | Directly demonstrates a JD skill | Unrelated |
| Uniqueness | 20% | Nobody has done this | Everyone has it |
| Demo-ability | 20% | Live demo, dashboard, or clear visual output in 2 min | Documentation only, nothing to show |
| Metrics potential | 15% | Clear operational metrics (throughput, cost savings, cycle time, utilisation %, service level) | No measurable outcome possible |
| Time to MVP | 10% | 1 week | 3+ months |
| STAR story potential | 10% | Rich story with trade-offs | Implementation only |

</scoring>

<process>

## Evaluation Flow

<step id="1" name="Score">
<agent_instruction>Apply the scoring grid. Compute weighted total (1–5). Read target roles from config/profile.md to calibrate "Signal for target roles".</agent_instruction>
</step>

<step id="2" name="Verdict">
<agent_instruction>
Assign one of three verdicts:
- **BUILD** → score ≥ 3.0; produce an 80/20 plan with weekly milestones
- **SKIP** → score < 3.0; explain why and suggest a stronger alternative
- **PIVOT TO [alternative]** → core idea is salvageable but a variant would score higher; describe the pivot
</agent_instruction>
</step>

<step id="3" name="80/20 Plan (BUILD only)">

## 80/20 Plan

- Week 1 → MVP with core metric
- Week 2 → polish + Interview Pack

</step>

<step id="4" name="Interview Pack (BUILD only)">

## Interview Pack Requirements

For each approved project:
1. **One-pager**: problem + approach + results + key decisions
2. **Demo**: live tool, dashboard, model output, or recorded 2-min walkthrough
3. **Postmortem**: what worked, what didn't, what would be done differently

</step>

</process>

<output>
Score table + weighted total, verdict, and (if BUILD) the 80/20 plan and Interview Pack checklist.
</output>
