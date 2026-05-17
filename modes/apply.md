# Mode: apply — Live Application Assistant

<purpose>
Interactive mode for filling out a live application form. Reads the active page, loads prior evaluation context, and generates personalized responses for each form question. Never auto-submits — the candidate always makes the final call.
</purpose>

<rules>
- NEVER auto-submit, click Apply/Send, or trigger any form submission. Stop and present responses for the candidate to review and copy-paste.
- If no existing report is found, offer to run auto-pipeline before proceeding.
- Read compensation targets from `config/profile.md` — never invent figures.
- If the role on screen differs from the evaluated role, notify the candidate before generating responses.
- Cover letter: if the form has a cover letter field or upload, always generate one. Max 1 page. Use the same visual design as the CV (same LaTeX template). Structure: hook using something specific from the JD or company → 2-3 sentences matching the strongest proof points to the role's core requirements → forward-looking close referencing the company name. Never use "I am writing to apply" or "I am passionate about." JD quotes → proof points, never the reverse.
</rules>

## Requirements

<section name="playwright">

- **With Playwright (preferred):** Candidate sees the browser; agent can snapshot and interact.
- **Without Playwright:** Candidate shares a screenshot, pastes questions manually, or states company + role.
</section>

## Workflow

<process>

<step id="1" name="Detect">

<agent_instruction>
Take a Playwright snapshot of the active tab (title, URL, visible content). If Playwright is unavailable, prompt the candidate.
</agent_instruction>

<user_prompt>
(Without Playwright) "Share a screenshot of the form, paste the questions as text, or tell me the company and role — I'll pull your evaluation context."
</user_prompt>
</step>

<step id="2" name="Identify and load context">

<agent_instruction>
1. Extract company name and role title from the page.
2. Search `output/reports/` for a matching report (case-insensitive).
3. If found → load full report and any existing Section G draft answers.
4. If not found → notify the candidate and offer to run auto-pipeline first.
</agent_instruction>
</step>

<step id="3" name="Detect role changes">

<agent_instruction>
Compare the role on screen against the evaluated role. If different, pause and notify.
</agent_instruction>

<user_prompt>
"The role has changed from [X] to [Y]. Should I adapt the responses to the new title, or re-evaluate the posting first?"
</user_prompt>

<agent_instruction>
- **Adapt**: Adjust responses without re-evaluating.
- **Re-evaluate**: Run full A–G pipeline, update report. Update role title in `applications.md` if applicable.
</agent_instruction>
</step>

<step id="4" name="Analyze form questions">

<agent_instruction>
Identify ALL visible questions and classify each:

| Type | Examples |
|---|---|
| Free text | Cover letter, "why this role" |
| Dropdown | How did you hear, work authorization |
| Yes/No | Relocation, visa status |
| Salary | Range or expectation |
| Upload | Resume, cover letter PDF |

- **Already answered in Section G** → adapt the existing response.
- **New question** → generate from report + `config/profile.md`.

If the form has more questions below the fold, request a scroll/screenshot and process iteratively.
</agent_instruction>
</step>

<step id="5" name="Generate responses">

<agent_instruction>
For each question:
1. Draw proof points from report Block B.
2. Use existing Section G draft as a base if available, then refine.
3. Reference something specific from the visible JD.
4. Use the "I'm choosing you" tone from the auto-pipeline framework.
5. In any "Additional info" field, include a career-ops proof point if relevant.
</agent_instruction>

<format>
```
## Responses for [Company] — [Role]

Based on: Report #NNN | Score: X.X/5 | Archetype: [type]

---

### 1. [Exact form question]
> [Response ready for copy-paste]

### 2. [Next question]
> [Response]

---

Notes:
- [Observations about role changes or mismatches]
- [Personalization suggestions for the candidate to review]
```
</format>
</step>

<step id="6" name="Stop before submit">

<validation>
Present all responses and STOP. Do not click Apply, Submit, or Send. The candidate reviews and submits manually.
</validation>

<user_prompt>
"Here are your responses — copy-paste each one into the form and review before you submit. Let me know once you've submitted and I'll update your tracker."
</user_prompt>
</step>

<step id="7" name="Post-apply (after candidate confirms submission)">

<agent_instruction>
1. Write a TSV line to `batch/tracker-additions/{num}-{slug}.tsv` with status `Applied`, then run `node merge-tracker.mjs` to update the tracker.
2. Append final form responses as an addendum to the evaluation report in `output/reports/`.
3. Suggest next step.
</agent_instruction>

<completion>
"Application logged as Applied. Want me to draft a LinkedIn outreach message to someone at [Company]? Run `/career-ops contact` to start."
</completion>
</step>

</process>

<output>
- Formatted copy-paste responses for each visible form field
- (Post-submit) Updated status via TSV → merge-tracker pipeline and Section G in the report
</output>
