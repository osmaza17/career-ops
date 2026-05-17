# Mode: offers — Multi-Offer Comparison

<purpose>
Compare two or more job offers side-by-side using a weighted 10-dimension scoring matrix. Produce a ranked table and a final recommendation.
</purpose>

<rules>
- Require at least 2 offers before proceeding.
- Accept offers as plain text, URLs, or references to existing reports in `output/reports/`.
- Never repeat scoring logic already used in `offer-analysis` — re-use scores from existing reports when available.
- Base comp estimates on market data from `config/profile.md` (`compensation` key); never fabricate figures.
- Final recommendation must name one winner and explain the reasoning in ≤ 3 sentences.
</rules>

## Scoring Matrix

<scoring>
| Dimension           | Weight | 5                          | 1                    |
|---------------------|--------|----------------------------|----------------------|
| North Star alignment | 25%   | Exact target role          | Unrelated            |
| CV match            | 15%    | 90%+ match                 | <40% match           |
| Level (senior+)     | 15%    | Staff+                     | Junior               |
| Estimated comp      | 10%    | Top quartile               | Below market         |
| Growth trajectory   | 10%    | Clear path to next level   | Dead end             |
| Remote quality      | 5%     | Full remote async          | Onsite only          |
| Company reputation  | 5%     | Top employer               | Red flags            |
| Domain tool fit     | 5%     | Exact stack match          | Entirely foreign     |
| Speed to offer      | 5%     | Fast process               | 6+ months            |
| Cultural signals    | 5%     | Builder culture            | Bureaucratic         |

Score each dimension 1–5, multiply by weight, sum for a weighted total out of 5.
</scoring>

## Process

<process>

<step id="1" name="Collect offers">
<agent_instruction>Check context for offers. If fewer than 2 are present, prompt the user.</agent_instruction>
<user_prompt>Please share the offers you want to compare — paste job descriptions, URLs, or mention companies already in your tracker (e.g. "Stripe vs Notion").</user_prompt>
</step>

<step id="2" name="Load existing scores">
<agent_instruction>
For each offer already in `output/reports/`, read the existing report and extract Block A scores rather than re-scoring from scratch. For new offers, score all 10 dimensions inline.
</agent_instruction>
</step>

<step id="3" name="Build comparison table">
<format>
## Comparison — {Offer A} vs {Offer B} [vs …]

| Dimension           | Weight | {Offer A} | {Offer B} |
|---------------------|--------|-----------|-----------|
| North Star alignment | 25%   | X/5       | X/5       |
| CV match            | 15%    | X/5       | X/5       |
| Level               | 15%    | X/5       | X/5       |
| Estimated comp      | 10%    | X/5       | X/5       |
| Growth trajectory   | 10%    | X/5       | X/5       |
| Remote quality      | 5%     | X/5       | X/5       |
| Company reputation  | 5%     | X/5       | X/5       |
| Domain tool fit     | 5%     | X/5       | X/5       |
| Speed to offer      | 5%     | X/5       | X/5       |
| Cultural signals    | 5%     | X/5       | X/5       |
| **Weighted Total**  |        | **X.X/5** | **X.X/5** |
</format>
</step>

<step id="4" name="Rank and recommend">
<format>
### Ranking
1. {Winner} — X.X/5
2. {Runner-up} — X.X/5

### Recommendation
{1–3 sentence rationale naming the winner, key differentiators, and any time-to-offer consideration.}
</format>
</step>

</process>

<completion>
After presenting the comparison, ask:
<user_prompt>Would you like me to generate a tailored CV or draft an outreach message for the top-ranked offer?</user_prompt>
</completion>
