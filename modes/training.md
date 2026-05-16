# Mode: training — Training Evaluation

<purpose>
Evaluate a course or certification the user is considering. Produce a verdict with a concrete plan or a better alternative.
</purpose>

<rules>
- Always evaluate all 6 dimensions before issuing a verdict.
- Never recommend without accounting for opportunity cost.
- Verdicts must be one of: DO / DON'T / DO WITH TIMEBOX.
</rules>

## Dimensions

<reference id="eval-dimensions">
| Dimension             | What it evaluates                              |
|-----------------------|------------------------------------------------|
| North Star alignment  | Does it move closer to or further from the goal? |
| Recruiter signal      | What do HMs think when they see this on a CV?  |
| Time and effort       | Weeks × hours/week                             |
| Opportunity cost      | What can't they do during that time?           |
| Risks                 | Outdated content? Weak brand? Too basic?       |
| Portfolio deliverable | Does it produce a demonstrable artifact?       |
</reference>

## Verdicts

<reference id="verdicts">
| Verdict                        | Output                                                   |
|-------------------------------|----------------------------------------------------------|
| **DO**                        | 4–12 week plan with weekly deliverables and scoreboard  |
| **DON'T**                     | Better alternative with justification                   |
| **DO WITH TIMEBOX** (max N wk) | Condensed plan, essentials only                        |
</reference>

## Priority Ranking

<reference id="priority">
Training that improves credibility in quantitative operations and consulting:
1. Advanced optimisation and OR (MILP, stochastic programming, metaheuristics)
2. Discrete-event simulation (AnyLogic, Simul8, Plant Simulation, Arena)
3. Supply chain and logistics analytics (network design, S&OP, inventory models)
4. Data analysis and BI (Python/pandas, Power BI, SQL, statistical modelling)
5. Project and programme management (PMP, PRINCE2, or agile methodologies)
6. Domain certifications with recruiter signal (Lean Six Sigma Green/Black Belt, APICS CPIM/CSCP, PMI)
</reference>

## Process

<process>
<step id="1" name="Gather input">
<agent_instruction>If the user hasn't specified the course/cert, ask for it. If already provided, proceed.</agent_instruction>
</step>

<step id="2" name="Evaluate dimensions">
<agent_instruction>Score all 6 dimensions from `eval-dimensions`. Cross-reference `priority` to weight North Star alignment and recruiter signal.</agent_instruction>
</step>

<step id="3" name="Issue verdict">
<agent_instruction>Select verdict from `verdicts`. For DO or DO WITH TIMEBOX, produce a week-by-week plan with deliverables. For DON'T, name a concrete alternative.</agent_instruction>
</step>
</process>

<output>
- Verdict (DO / DON'T / DO WITH TIMEBOX)
- Dimension breakdown
- Week-by-week plan (if DO or DO WITH TIMEBOX) or recommended alternative (if DON'T)
</output>
