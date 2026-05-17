# Mode: auto-pipeline — Full Automatic Pipeline

<purpose>
When the user pastes a JD (text or URL) without an explicit sub-command, run the full pipeline in sequence: extract → evaluate → save report → generate PDF → draft answers (if score ≥ 4.5) → update tracker.
</purpose>

<rules>
- NEVER write directly to `data/applications.md` — always use the TSV → merge pipeline.
- If any step fails, continue with remaining steps and note the failure in the TSV notes column.
- Draft answers are only generated when score ≥ 4.5.
- Always match output language to the JD language (EN default).
</rules>

---

## Step 0 — Extract JD

<step id="0" name="Extract JD">
<agent_instruction>
If input is a URL, extract JD content using this priority order:
1. **Playwright (preferred):** `browser_navigate` + `browser_snapshot` — handles SPAs (Lever, Ashby, Greenhouse, Workday).
2. **WebFetch (fallback):** For static pages (ZipRecruiter, WeLoveProduct, company career pages).
3. **WebSearch (last resort):** Search role title + company on secondary portals that index the JD as static HTML.

If input is already JD text, use it directly — no fetch needed.
</agent_instruction>
<validation>
If none of the above methods yield content:
</validation>
<user_prompt>I couldn't extract the JD automatically. Could you paste the text or share a screenshot?</user_prompt>
</step>

---

## Step 1 — Evaluate (Blocks A–G)

<step id="1" name="Evaluate">
<agent_instruction>
Run the full evaluation per `modes/offer-analysis.md` (Blocks A–F + Block G Posting Legitimacy). Do not repeat that logic here.
</agent_instruction>
</step>

---

## Step 2 — Save Report

<step id="2" name="Save Report">
<agent_instruction>
Save the full evaluation to `reports/{###}-{company-slug}-{YYYY-MM-DD}.md` using the format defined in `modes/offer-analysis.md`. Include Block G and add `**Legitimacy:** {tier}` to the report header.
</agent_instruction>
<output>`reports/{###}-{company-slug}-{YYYY-MM-DD}.md`</output>
</step>

---

## Step 3 — Generate PDF

<step id="3" name="Generate PDF">
<agent_instruction>
Execute `modes/pdf.md`. It defaults to the LaTeX pipeline; switches to Canva if `cv.canva_resume_design_id` is set in `config/profile.md`.
</agent_instruction>
<output>`output/` PDF file</output>
</step>

---

## Step 4 — Draft Application Answers (score ≥ 4.5 only)

<step id="4" name="Draft Application Answers">
<validation>Only run this step if the final score is ≥ 4.5.</validation>
<agent_instruction>
1. Use Playwright to navigate to the application form and snapshot the questions. If unavailable, use the generic questions below.
2. Generate answers following the tone rules below.
3. Append answers to the report as section `## H) Draft Application Answers`.
</agent_instruction>

### Generic Questions (fallback if form can't be extracted)

- Why are you interested in this role?
- Why do you want to work at [Company]?
- Tell us about a relevant project or achievement.
- What makes you a good fit for this position?
- How did you hear about this role?

### Answer Tone Rules

**Position: "I'm choosing you."** — The candidate has options and is choosing this company for specific reasons.

- **Confident, not arrogant:** Reference real experience — "I've spent the past year building optimisation models with real industrial clients — your role is where I want to apply that next."
- **Specific and concrete:** Always anchor to something real from the JD and something real from the candidate's profile.
- **Direct, no fluff:** 2–4 sentences per answer. No "I'm passionate about…" or "I would love the opportunity to…"
- **Proof over claim:** Instead of "I'm great at X", say "I built X that does Y."

**Framework by question type:**
- **Why this role?** → "Your [specific thing] maps directly to [specific thing I built]."
- **Why this company?** → Concrete product/mission reference — "I've been using [product] for [time/purpose]."
- **Relevant experience?** → One quantified proof point.
- **Good fit?** → "I sit at the intersection of [A] and [B], which is exactly where this role lives."
- **How did you hear?** → Honest — "Found through [portal/scan], evaluated against my criteria, and it scored highest."
</step>

---

## Step 5 — Update Tracker

<step id="5" name="Update Tracker">
<agent_instruction>
1. Write one TSV line to `batch/tracker-additions/{num}-{company-slug}.tsv`:
```
{num}\t{YYYY-MM-DD}\t{company}\t{role}\tEvaluated\t{X.X}/5\t{pdf_emoji}\t[{num}](reports/{num}-{slug}-{date}.md)\t{one-line note}
```
Set PDF emoji to ✅ if Step 3 succeeded, ❌ otherwise.

2. Run:
```bash
node merge-tracker.mjs
```
</agent_instruction>
<output>`data/applications.md` (via merge script)</output>
</step>
