# Mode: pdf — CV PDF Generation

<purpose>
Generate a tailored PDF CV for a target role. Defaults to LaTeX; offers Canva when a design ID is configured.
</purpose>

<rules>
- NEVER invent metrics, keywords, or experience not present in `config/profile.md`
- Canva text replacements MUST stay within ±15% of the original character count (fixed-size text boxes)
- NEVER commit a Canva transaction without explicit user approval
- Download the export URL immediately — pre-signed S3 links expire in ~2 hours
- After generation, update the tracker: flip PDF column from ❌ to ✅ if the offer is already registered
</rules>

---

## LaTeX Flow (default)

<agent_instruction>
Execute the full LaTeX pipeline defined in `modes/latex.md`. Use this path unless the user explicitly requests Canva or `cv.canva_resume_design_id` is set in `config/profile.md`.
</agent_instruction>

---

## Canva Flow (optional)

<agent_instruction>
Check `config/profile.md` for `cv.canva_resume_design_id`. If absent, skip to LaTeX. If present, ask:
</agent_instruction>

<user_prompt>
Which CV format would you like?
1. LaTeX/PDF — fast, ATS-optimized (default)
2. Canva CV — visual, design-preserving
</user_prompt>

<process>

<step id="1" name="Duplicate base design">

- `export-design` the base design (`cv.canva_resume_design_id`) as PDF → get download URL
- `import-design-from-url` with that URL → creates editable duplicate
- Record the new `design_id`

<validation>
If `import-design-from-url` fails → fall back to LaTeX pipeline and notify user.
</validation>
</step>

<step id="2" name="Map design structure">

- `get-design-content` on the duplicate → returns all richtext elements
- Map elements to CV sections by content matching:
  - Candidate name → header
  - "Summary" / "Professional Summary" → summary
  - Company names from `experience[].company` → experience sections
  - Degree/school names → education
  - Skill keywords → skills
- Record character count for each element (needed for budget enforcement in Step 3)

<validation>
If mapping fails → show user what was found and ask for manual guidance.
</validation>
</step>

<step id="3" name="Generate tailored content">

<agent_instruction>
Apply the same tailoring logic as the LaTeX flow:
- Rewrite Professional Summary with JD keywords + exit narrative
- Reorder experience bullets by JD relevance
- Select top competencies matching JD requirements
- Inject keywords naturally

Enforce character budget: each replacement MUST be within ±15% of the original element's character count (recorded in Step 2). Condense if over budget.
</agent_instruction>
</step>

<step id="4" name="Apply edits and verify layout">

a. `start-editing-transaction` on the duplicate
b. `perform-editing-operations` — `find_and_replace_text` for each section (summary, experience bullets, skills, projects)
c. **Reflow layout** after replacements:
   - Read updated element positions/dimensions from the operation response
   - For each experience section top-to-bottom: `end_y = top + height`
   - Move next section's elements to `end_y + original_gap` (typically ~30px) using `position_element`
d. **Verify layout** — `get-design-thumbnail` (page_index=1); check for overlapping text, uneven spacing, cut-off text; fix with `position_element` / `resize_element` / `format_text` until clean

<validation>
Show the user the final preview thumbnail.
</validation>

<user_prompt>
Here's the preview. Does this look good? I'll save it once you confirm.
</user_prompt>

e. `commit-editing-transaction` — ONLY after user approval

<validation>
If `find_and_replace_text` finds no matches → try broader substring matching. Always provide the Canva design URL so the user can edit manually if auto-edit fails.
</validation>
</step>

<step id="5" name="Export and download PDF">

a. `export-design` the duplicate as PDF (format: `a4` or `letter` based on JD location)
b. Download immediately:

```bash
curl -sL -o "output/cv-{candidate}-{company}-canva-{YYYY-MM-DD}.pdf" "{download_url}"
```

c. Verify:

```bash
file output/cv-{candidate}-{company}-canva-{YYYY-MM-DD}.pdf
```

Must show "PDF document". If XML/HTML → URL expired; re-export and retry.

</step>

</process>

<output>
- `output/cv-{candidate}-{company}-canva-{YYYY-MM-DD}.pdf`
- Canva design URL (for manual tweaking)
- File size confirmation
</output>

<completion>
PDF saved to `output/cv-{candidate}-{company}-canva-{YYYY-MM-DD}.pdf` ({size}). Canva design: {url} — open it anytime for manual adjustments.
</completion>
