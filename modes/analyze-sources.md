# Mode — analyze-sources: Raw Documents → profile.md Entries

<purpose>
Transform raw academic documents (project reports, internship descriptions, student association files, competition submissions) stored in `sources/` into formatted, CV-ready entries appended directly to `config/profile.md`. Applies a recruiter/manager lens throughout: extracts scale, outcomes, named clients, and concrete deliverables — discards technical minutiae. What is written here goes directly into the `.tex` CV; there is no intermediate step.
</purpose>

<rules>
- Process **one document at a time**. Every entry goes through a review loop before writing.
- Never write to `config/profile.md` without explicit user approval.
- Never fabricate or inflate contribution to fill a template.
- Only `modes/onboard.md` and `modes/analyze-sources.md` may read from `sources/`. No other mode accesses that folder.
- Write all entries in **English**, matching the language of `config/profile.md`. Technical terms without a clean equivalent may remain in the original language in parentheses.
- Apply writing rules from `modes/writing.md` (bullet ordering, Specificity Standard, vocabulary bans).
</rules>

---

## Step 1 — Inventory, Deduplication, and Multi-file Grouping

<phase id="1" name="Inventory">

<agent_instruction>
Run all three checks silently, then report once.

**1a. List sources:** List all files in `sources/` (excluding `README.md` and `_preamble.md` — these are documentation files, not source documents to process).

**1b. Deduplication — two-pass:**
- Pass 1 (exact): Read `config/profile.md`. For each YAML array (`experience`, `student_life`, `projects`, `competitions`, `additional_training`), collect every `company:` or `title:` value. If a source file clearly maps to an existing entry, flag it `ALREADY ANALYZED`.
- Pass 2 (fuzzy): For any file with no key match, scan bullet text and date values for a likely match. If found, flag `PROBABLE MATCH` — do not proceed automatically; confirm with the user first.

**1c. Multi-file grouping:** If multiple source files clearly describe the same experience (e.g., internship brief + final report), group them as one entry. Process together and note both filenames in the review loop.
</agent_instruction>

<user_prompt>
Sources found:
1. [filename] — [type] — NEW
2. [filename] + [filename] — [type] — GROUPED (same experience, will merge)
3. [filename] — [type] — ALREADY ANALYZED (matches entry: "[title/company]" in [yaml_key])
4. [filename] — [type] — PROBABLE MATCH (matches "[title/company]" — confirm before proceeding)

Which file would you like to process first?
</user_prompt>

<agent_instruction>
- ALREADY ANALYZED → go to Improvement Mode.
- PROBABLE MATCH → ask user to confirm before treating as new or as improvement.
- NEW / GROUPED → continue with Steps 2–7.
</agent_instruction>

</phase>

---

## Step 2 — Classify and Read

<phase id="2" name="Classify and Read">

### 2a. Classification

<reference id="type-classification">
| Type | Description | Signals |
|------|-------------|---------|
| **Project** | Academic study, consulting report, engineering analysis | Academic semester dates, defined deliverable |
| **Experience** | Internship, professional placement, part-time role | Company name + role title |
| **Association** | Student club role, event mandate, representative position | Bureau/club/association + cargo |
| **Competition** | Case competition, hackathon, academic prize | Prix/classement/jurado |
| **Training** | Course, certification, extracurricular program | Institution or company, duration |
| **Other** | Describe before extracting | — |
</reference>

<reference id="type-to-yaml-key">
| Type | Target key in profile.md |
|------|--------------------------|
| Experience | `experience` |
| Association | `student_life` |
| Project | `projects` |
| Competition | `competitions` |
| Training | `additional_training` |
</reference>

<agent_instruction>
If classification is uncertain: state your best guess and why, then ask the user to confirm before proceeding.
Always verify the target array key exists in `profile.md` before appending. If `additional_training` is missing, create it after `competitions:` with an empty list, then append.
</agent_instruction>

### 2b. Document Reading Strategy

<agent_instruction>
Read efficiently — stop when you have what you need.

- **Short documents (< 5 pages):** read in full.
- **Long project reports (5–80 pages):** read in this order, stop when sufficient:
  1. Cover/title page — team, dates, supervisor, client, academic framework
  2. Executive summary — objective, method overview, main finding
  3. Conclusion/Recommendations — what was recommended, decided, or implemented
  4. Results section — metrics, outcomes
  5. Introduction — scope/context (if not covered by executive summary)
  6. Appendices — only if the executive summary references a metric that lives there
  Skip entirely: theoretical background, literature review, detailed methodology, mathematical proofs, sensitivity tables (unless the headline metric is there).
- **Internship documents:** prioritize mission letter (scope) → final report/attestation (outcomes) → supervisor evaluation (strongest verifiable signal). If brief and final report are grouped, synthesize both.
- **Poorly formatted/corrupted PDFs:** stop, describe what's readable, ask user to paste: (a) cover page info, (b) executive summary or objectives, (c) results or conclusions. Do not attempt extraction from corrupted text.

Source documents may be in French, English, or Spanish. Read in the source language; write the `profile.md` entry in the language of `config/profile.md`. Do not mix languages within an entry.
</agent_instruction>

</phase>

---

## Step 3 — The Recruiter Lens (CRITICAL)

<rules>
This filter applies to all extraction and drafting steps.

**Extract and amplify:**
- Named clients and institutions (carry credibility)
- Scale signals — people managed, budget, volume, geographic scope, team size; prefer numbers over adjectives
- Outcomes — what was decided, implemented, or changed; a recommendation accepted by the client outweighs a correct model
- Your specific contribution — what you did, not what the team did
- Transferable signals — structured problem-solving, autonomous project management, stakeholder communication, quantitative analysis
- Verifiable facts — grades, rankings, prizes, tools adopted permanently

**Suppress and discard:**
- Mathematical formulations, model specs, algorithmic complexity
- Solver settings, software configuration, hyperparameters
- Academic methodology for its own sake ("we used a MILP model")
- Team process descriptions that don't show your role
- Problem geometry that describes setup, not value
- Theoretical background, literature review, technical jargon with no managerial translation

**Test for every extracted fact:** "Would a hiring manager who does not know this field find this useful or impressive?" If no — discard.
</rules>

---

## Step 4 — Extraction per Document Type

<phase id="4" name="Extraction">

<reference id="extraction-fields">

**4a. Professional Experience (internship/stage)**
| Field | What to look for |
|-------|-----------------|
| Company | Exact name as written |
| Sector | Industry, type of firm |
| Dates | Start and end month/year |
| Role/title | Exact title from the document |
| Country | Country only |
| Main mission | What the internship was for — 1–2 sentences |
| Personal deliverables | Report, tool, model, recommendation, design |
| Results | Cost impacted, time saved, volume processed, decisions influenced |
| Tools with signal | Only tools with recruiter meaning — professional software |
| Competences | Autonomous management, client interaction, technical execution |

**4b. Academic Project / Consulting Study**
| Field | What to look for |
|-------|-----------------|
| Project type | Étude, diagnostic, optimisation, go-to-market, dimensionnement, simulation |
| Client | Named company/institution, or "ejercicio académico" if none |
| Academic framework | Course name, program, semester, institution |
| Dates | Semester or month range |
| Team | Size and composition |
| Your role | What you personally owned vs. what the team shared |
| Problem | What question was being answered — 1–2 sentences |
| Approach | Only if it demonstrates a transferable skill |
| Results | Recommendation adopted, grade, prize, client feedback, metric achieved |
| Differentiator | What makes this harder to replicate than average |

**4c. Student Association Activity**
| Field | What to look for |
|-------|-----------------|
| Organization | Full name |
| Role/cargo | Exact title |
| Dates | Full mandate duration |
| Scope | Members managed, events organized, budget, reach |
| Concrete actions | What you actually did — not the association's purpose |
| Notable achievement | One verifiable fact you'd mention in 30 seconds |

**4d. Competition / Hackathon**
| Field | What to look for |
|-------|-----------------|
| Result | Ranking or prize — always leads |
| Organizer | Who ran it — credibility signal |
| Date | Month and year |
| Problem | What was being solved |
| Team | Size |
| Your contribution | What you specifically did |
| Overlap | Does a Projects entry already cover this same work? |

**4e. Training / Extracurricular Program**
| Field | What to look for |
|-------|-----------------|
| Program name | Exact name |
| Organizer | Institution or company |
| Dates | Duration |
| Access | Was selection competitive? How many participants? |
| Content with signal | Only sessions/modules with recruiter value |
| Differentiator | Access, speakers, format that make this non-standard |
</reference>

### Minor contribution handling

<validation>
If the personal contribution appears limited (e.g., one team member among fifteen, no defined deliverable):
</validation>

<user_prompt>
⚠️ Thin contribution detected in [filename].

Based on the document, your specific role is unclear or limited.
Options:
1) Confirm — was your contribution larger than described? (documents often under-represent individual roles)
2) Fold — use this as supporting context in an existing entry covering similar work
3) Archive — note it for future reference; do not add to profile.md

Which?
</user_prompt>

</phase>

---

## Step 5 — Probing

<phase id="5" name="Probing">

<agent_instruction>
After extraction, identify gaps and classify each as blocking (cannot draft without it) or enhancing (entry can be drafted, but this strengthens it). Ask all questions in one message, labeled clearly. Never ask more than 3 questions total per document. Ask blocking questions first; if none are blocking, ask up to 2 enhancing. Wait for answers before drafting.
</agent_instruction>

<reference id="probe-questions">
**Project — blocking:** "What was YOUR specific contribution vs. what the whole team did?" *(blocking if "we" appears throughout with no individual attribution)*
**Project — enhancing:** "Did the client implement or act on any recommendations?" / "Was there a grade or external evaluation?"
**Experience — blocking:** "What was your most important deliverable — the thing your manager would remember you for?" *(blocking if deliverables are absent)*
**Experience — enhancing:** "Any metric: volume processed, time saved, cost impacted?" / "Was any of your work adopted after you left?"
**Association — blocking:** "What was the scale: members managed, budget, events, participants?" *(blocking if no scale data)*
**Association — enhancing:** "What's the one achievement from this mandate you'd lead with in an interview?"
**Competition — blocking:** "What was your specific role in the team?" *(always ask — competition documents rarely attribute individual contribution)*
**Competition — enhancing:** "Is this the same work as an existing Projects entry? Should both reference each other?"
</reference>

</phase>

---

## Step 6 — Draft YAML Entry

<phase id="6" name="Draft">

<reference id="yaml-templates">

**Experience → `experience` array**
```yaml
- company: "[Company name]"
  post: "[Role in X domain — e.g., 'Operations Research Intern, Supply Chain']"
  country: "[Country only, no city]"
  date: "[mon. YYYY] -- [mon. YYYY]"
  bullets:
    - "[Bullet 1 — strongest outcome or deliverable, verb-first]"
    - "[Bullet 2]"
    - "[Bullet 3 — max 3 total]"
```
Rules: `post` = role and domain. Reverse chronological within array. Max 3 bullets, min 1 strong bullet.
→ LaTeX: `\cventry{Company}{Post}{Country}{Date}`

---

**Project → `projects` array**
```yaml
- title: "[Title ≤ 45 chars]"
  subtitle: "[Client, framework, or differentiator — no tools, no country]"
  date: "[mon. YYYY]"
  bullets:
    - "[Bullet 1 — most impressive outcome or contribution, verb-first]"
    - "[Bullet 2]"
    - "[Bullet 3 — max 3 total]"
```
Rules: `title` ≤ 45 chars (see `modes/writing.md §19.1`). `subtitle` per `modes/writing.md §19.2`. Order by decreasing interest to interviewer — not by date.
→ LaTeX: `\cventryprojet{Title}{Subtitle}{Date}`

---

**Association → `student_life` array**
```yaml
- title: "[Role, Organization]"
  date: "[mon. YYYY] -- [mon. YYYY]"
  bullets:
    - "[Bullet 1 — scale or concrete achievement]"
    - "[Bullet 2]"
    - "[Bullet 3 — max 3 total]"
```
Rules: `title` = role and organization separated by comma (e.g., "Secretary General, BDI"). No subtitle, no country, no `·`.
→ LaTeX: `\cventryassociatif{Title}{Date}`

---

**Competition → `competitions` array**
```yaml
- title: "[Position, Contest Name]"
  subtitle: "[Organizer — context: foundation, company, institution. Include team count.]"
  date: "[mon. YYYY]"
  bullets:
    - "[Bullet 1 — result leads always]"
    - "[Bullet 2 — max 2 total]"
```
Rules: Include only if result is externally verifiable (ranking, jury, prize). Do not duplicate content from a Projects entry covering the same work.
→ LaTeX: `\cventrycontest{Title}{Subtitle}{Date}`

---

**Training → `additional_training` array** (reverse chronological)
```yaml
- title: "[Program Name] — [Organizer]"
  date: "[mon. YYYY]"
  bullets:
    - "[Bullet 1 — access/selection signal or most differentiating content]"
    - "[Bullet 2 — max 2 total]"
```
Rules: Include only if it adds a differentiating signal not covered by main education. Lead with the access signal if present. If `additional_training:` key is missing, create it after `competitions:`.

</reference>

</phase>

---

## Step 7 — Review Loop and Append

<phase id="7" name="Review and Append">

<review_loop>

**Round 1 — Present draft:**

<user_prompt>
Here's the draft entry for [filename] → [yaml_key]:

```yaml
[Full YAML entry object]
```

Tell me what to change, or say "add it" to write it to profile.md.
</user_prompt>

**Each subsequent round — revise and re-present:**

<agent_instruction>
Apply requested changes. Always show the complete entry — never just the diff.
</agent_instruction>

<user_prompt>
Revised:

```yaml
[Full revised entry]
```

Anything else to change, or ready to add?
</user_prompt>

**Approval phrases:** "add it", "looks good", "yes", "go ahead", "write it", or any clear affirmative.

</review_loop>

<validation>
On approval: run the Quality Check (see below) before writing. If it passes, append the YAML object to the correct array in `config/profile.md`. Never touch any other array or key.
If the user says "hold" or "not now" — stop without writing anything.
</validation>

<completion>
Added to profile.md → [yaml_key] array.

Entry added. Run `/career-ops pdf` with a job description to generate an updated CV.
</completion>

<agent_instruction>
After writing the entry, check whether a Profile Metadata Update is warranted (see below).
</agent_instruction>

</phase>

---

## Improvement Mode (Already-Analyzed Sources)

Triggered when a source file matches an existing entry by title, company name, or content match (Step 1b).

<process>

<step id="A" name="Locate existing entry">
<agent_instruction>Find the full entry in `profile.md` (title, fields, bullets). Extract it verbatim.</agent_instruction>
</step>

<step id="B" name="Re-read source">
<agent_instruction>Apply the reading strategy from Step 2b. Look specifically for: facts/metrics absent from the existing entry; framing no longer at its strongest; recruiter-lens violations (technical minutiae, buried lead).</agent_instruction>
</step>

<step id="C" name="Decide if improvement is warranted">
<agent_instruction>
Only propose an improvement if there is a genuine reason:
- A metric or outcome in the source absent from the existing entry
- A recruiter-lens violation in the current text
- The title or role description doesn't match the source

If no genuine improvement exists, say so explicitly.
</agent_instruction>
<user_prompt>
The existing entry already captures everything useful in this document. No changes proposed.
</user_prompt>
</step>

<step id="D" name="Present and review">
<review_loop>
<user_prompt>
## Already analyzed: [filename]

**Current entry in profile.md:**
```yaml
[Full current entry, verbatim]
```

**Proposed improved version:**
```yaml
[Full proposed entry]
```

**What changed and why:**
- [Specific change]: [reason]

Tell me what to adjust, or say "replace it" to overwrite the current entry.
</user_prompt>
<agent_instruction>On each revision round, show the full proposed entry — never just the diff.</agent_instruction>
</review_loop>
</step>

<step id="E" name="Apply on approval">
<validation>
Only on explicit approval ("replace it", "looks good", "go ahead"): locate the matching YAML object by its `title:` or `company:` key, replace only that object. Never touch other objects in the same array or any other key.
If user says "keep the current version" or "no changes" — do nothing.
</validation>
</step>

</process>

---

## Profile Metadata Updates

<agent_instruction>
After any entry is confirmed and appended, check whether the identity/narrative section should also be extended. Only offer when the new entry contains a strong result or competition outcome not yet captured there.
</agent_instruction>

<reference id="metadata-update-rules">
| Entry type | YAML key to update | Condition |
|------------|-------------------|-----------|
| Any entry with a strong result or differentiator | `narrative.superpowers` | If not already captured there |
| Competition with external prize | `awards_and_competitions` | If award not already listed |
| New experience that changes the narrative | `narrative.headline` or `narrative.exit_story` | Only if user explicitly requests it |
</reference>

<format>
**New superpower entry:**
```yaml
- "[What makes this notable — a differentiator, result, or achievement the user would want to talk about]"
```

**New awards_and_competitions entry:**
```yaml
- name: "[Prize name — Organization]"
  category: "hackathon / research_competition / case_competition / other"
  date: "YYYY-MM"
  result: "[Ranking or prize description]"
  context: "[1 sentence: format, number of teams, your role]"
```
</format>

<review_loop>
<user_prompt>
Entry added to profile.md.

This result is strong enough to add to your profile summary.
Proposed addition to [narrative.superpowers / awards_and_competitions]:

[formatted YAML block]

Tell me what to adjust, or say "add it" to update.
</user_prompt>
<agent_instruction>Show the proposed YAML, accept changes, only write on explicit approval. Modify only the target key — never touch CV section arrays (`education`, `experience`, `projects`, etc.).</agent_instruction>
</review_loop>

---

## Quality Check Before Appending

<validation>
Verify all of the following before writing to `profile.md`:

1. Every fact passes the recruiter lens — no technical minutiae, no methodology for its own sake
2. Personal contribution is explicitly separated from team work
3. At least one concrete, verifiable signal per bullet (metric, name, ranking, deliverable); if none, use a qualitative signal (adoption, follow-up, feedback)
4. Language and writing style match the rest of `profile.md` CV sections (verb-first bullets, no first person, no em-dashes)
5. Target array key exists in `profile.md`; if `additional_training` is missing, it was created before appending
6. Entry format matches the YAML template for its type (correct keys, max bullets respected, title ≤ 45 chars for `projects` and `competitions`)
7. `additional_training` entries are in reverse chronological order within the array

Any failure → fix before appending.
</validation>
