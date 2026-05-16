# Mode — analyze-sources: Raw Documents → profile.md Entries

## Purpose

Transform raw academic documents (project reports, internship descriptions, student association files, competition submissions) stored in `sources/` into formatted, CV-ready entries appended directly to `config/profile.md`. `profile.md` is the single source of truth — what you write here is what goes into the `.tex` CV. There is no intermediate step.

This mode applies a **recruiter/manager lens** throughout: it extracts what a non-specialist evaluating a profile would find meaningful — scale, outcomes, named clients, decisions, concrete deliverables — and discards technical minutiae that adds no signal to someone who hasn't read the source document.

---

## Access to `sources/`

`modes/onboard.md`, `modes/analyze-sources.md`, and `modes/parallel-sources.md` are the only three modes that read from `sources/`. `onboard` handles initial profile setup; `analyze-sources` processes single documents interactively; `parallel-sources` handles batch processing of multiple new files in parallel. No other mode, agent, or script accesses that folder.

---

## How this mode works

This mode always processes **one document at a time**. Every entry goes through an iterative review loop before anything is written to `config/profile.md`. The user has final say at every step — the agent never writes to the file without explicit approval.

---

## Step 1 — Inventory, Deduplication, and Multi-file Grouping

Before anything else, run all three checks silently, then report once.

### 1a. List source files

List all files in `sources/` (excluding `README.md`).

### 1b. Deduplication check — two-pass

**Pass 1 — Key match (exact):**
Read `config/profile.md`. For each YAML array (`experience`, `student_life`, `projects`, `competitions`, `additional_training`), collect every `company:` or `title:` value. If a source file clearly corresponds to an existing entry (matching company name, project title, or organization name), that experience is already covered — flag it as `ALREADY ANALYZED`.

**Pass 2 — Content match (fuzzy fallback):**
For any source file with no key match, scan bullet text and date values across all arrays for a likely match to the source document. If found, flag as `PROBABLE MATCH` — do not proceed automatically; confirm with the user first.

### 1c. Multi-file grouping

If multiple source files clearly describe the same experience (e.g., `2024-06_internship_airfrance-brief.pdf` and `2024-12_internship_airfrance-final-report.pdf`), group them as one entry. Process together: the brief provides context, the final report provides outcomes and metrics. Note both filenames in the review loop so the user knows which sources were used.

### 1d. Report

```
Sources found:
1. [filename] — [type] — NEW
2. [filename] + [filename] — [type] — GROUPED (same experience, will merge)
3. [filename] — [type] — ALREADY ANALYZED (matches entry: "[title/company]" in [yaml_key])
4. [filename] — [type] — PROBABLE MATCH (matches "[title/company]" — confirm before proceeding)

Which file would you like to process first?
```

**For already-analyzed files:** follow §Improvement Mode.
**For probable matches:** ask the user to confirm before treating as new or as improvement.
**For new and grouped files:** continue with Steps 2–7.

---

## Step 2 — Classify Each Document

For each source (or group), determine the type:

| Type | Description |
|------|-------------|
| **Project** | Academic study, consulting report, engineering analysis, technical project with defined scope and deliverable |
| **Experience** | Internship (stage), professional placement, part-time role |
| **Association** | Student club or association role, event mandate, representative position |
| **Competition** | Case competition, hackathon, academic prize |
| **Training** | Course, certification, extracurricular program |
| **Other** | Describe what it is before extracting |

Classification signals: academic semester dates → project; company name + role title → experience; bureau/club/association + cargo → association; prix/classement/jurado → competition.

**If classification is uncertain:** state your best guess and why, then ask the user to confirm before proceeding.

**Target YAML array by type:**

| Type | Target key in profile.md |
|------|--------------------------|
| Experience | `experience` |
| Association | `student_life` |
| Project | `projects` |
| Competition | `competitions` |
| Training | `additional_training` |

Always verify the array key exists in `profile.md` before appending. If it's missing (common for `additional_training`), create it after the `competitions:` block with an empty list, then append.

---

## Step 2.5 — Document Reading Strategy

Different document types and lengths require different reading approaches. Do not read everything — read efficiently and stop when you have what you need.

### Short documents (< 5 pages): read in full.

### Long project reports (5–80 pages): read in this order, stop when sufficient

1. **Cover / title page** — team members, dates, supervisor, client name, academic framework
2. **Executive summary / résumé exécutif** — objective, method overview, main finding or recommendation
3. **Conclusion / Recommendations section** — what was recommended, decided, or implemented
4. **Results section** — metrics, data, outcomes
5. **Introduction** — project scope and context (if not covered by executive summary)
6. **Appendices** — only if the executive summary references a specific metric that lives there

**Skip entirely:** theoretical background, literature review, detailed methodology, solver configuration, mathematical proofs, sensitivity tables (unless the headline metric lives there).

### Internship documents

Prioritize in order: mission letter (defines scope), end-of-internship report or attestation (captures outcomes), supervisor evaluation (if present — may contain the strongest verifiable signal). If the mission letter and final report are grouped, synthesize both.

### Multi-language documents

Source documents may be in French, English, or Spanish. **Read in the source language. Write the profile.md entry in the language of `config/profile.md`.** Do not mix languages within an entry. Technical terms without a clean equivalent in the target language can remain in the original language in parentheses.

### Poorly formatted or corrupted PDFs

If the text is garbled, tables are unreadable, or figures dominate the content: stop, describe what's readable, and ask the user to paste: (a) cover page info, (b) executive summary or objectives, (c) results or conclusions. Do not attempt extraction from corrupted text.

---

## Step 3 — The Recruiter Lens (CRITICAL)

This filter applies to **all extraction and drafting steps**. It is the most important constraint in this mode.

### Extract and amplify

- **Named clients and institutions** — real companies, public bodies, named professors or sponsors. These carry credibility that "a real company" does not.
- **Scale signals** — number of people managed, budget handled, volume processed, geographic scope, team size. Prefer concrete numbers over adjectives.
- **Outcomes** — what was decided, implemented, or changed. A recommendation accepted by the client outweighs a technically correct model. A prize outweighs an elegant methodology.
- **Your specific contribution** — what you did, not what the team or the methodology did.
- **Transferable signals** — structured problem-solving, autonomous project management, stakeholder communication, quantitative analysis, cross-cultural context. These read across sectors.
- **Verifiable facts** — grades, rankings, prizes, acceptance of recommendations, tools adopted permanently. Non-verifiable superlatives add nothing.

### Suppress and discard

- Mathematical formulations, model specifications, algorithmic complexity
- Implementation details: solver settings, software configuration, hyperparameters, warmup periods, data structure choices
- Academic methodology for its own sake — "we used a MILP model" belongs in the report, not the CV
- Team process descriptions that don't show your role (e.g., "the team followed a waterfall approach")
- Problem geometry that describes the setup, not the value (e.g., "a 1,200 m² facility" is fine as context; "the network had 5 nodes at distances of 50–200 km" is not)
- Theoretical background and literature review content
- Technical jargon with no managerial translation

**Test for every extracted fact:** "Would a hiring manager who does not know this field find this useful or impressive?" If no — discard.

---

## Step 4 — Extraction per Document Type

### 4a. Professional Experience (internship / stage)

| Field | What to look for |
|-------|-----------------|
| Company | Exact name as written |
| Sector | Industry, type of firm |
| Dates | Start and end month/year |
| Role/title | Exact title from the document |
| Country | Country only |
| Main mission | What the internship was for — 1–2 sentences |
| Personal deliverables | What you produced: report, tool, model, recommendation, design |
| Results | Any metric: cost impacted, time saved, volume processed, decisions influenced |
| Tools with signal | Only tools that carry recruiter meaning — professional software, not generic office tools unless used in a notable way |
| Competences | Cross-functional signals: autonomous management, client interaction, technical execution |

### 4b. Academic Project / Consulting Study

| Field | What to look for |
|-------|-----------------|
| Project type | Étude, diagnostic, optimisation, go-to-market, dimensionnement, simulation... |
| Client | Named company or institution, or "ejercicio académico" if none |
| Academic framework | Course name, program, semester, institution |
| Dates | Semester or month range |
| Team | Size and composition |
| Your role | What you personally owned vs. what the team shared |
| Problem | What question was being answered — 1–2 sentences |
| Approach | Only if it demonstrates a transferable skill (e.g., "led stakeholder interviews", "built the financial model") — not methodology for its own sake |
| Results | Recommendation adopted, grade, prize, client feedback, metric achieved |
| Differentiator | What makes this project harder to replicate than average |

### 4c. Student Association Activity

| Field | What to look for |
|-------|-----------------|
| Organization | Full name |
| Role/cargo | Exact title |
| Dates | Full mandate duration |
| Scope | Members managed, events organized, budget, reach |
| Concrete actions | What you actually did — not the association's purpose |
| Notable achievement | The one verifiable fact you'd mention in 30 seconds |

### 4d. Competition / Hackathon

| Field | What to look for |
|-------|-----------------|
| Result | Ranking or prize — always leads |
| Organizer | Who ran it — credibility signal |
| Date | Month and year |
| Problem | What was being solved |
| Team | Size |
| Your contribution | What you specifically did |
| Overlap | Does a Projects entry already cover this same work? |

### 4e. Training / Extracurricular Program

| Field | What to look for |
|-------|-----------------|
| Program name | Exact name |
| Organizer | Institution or company |
| Dates | Duration |
| Access | Was selection competitive? How many participants? |
| Content with signal | Only sessions/modules with recruiter value — sector exposure, executive interaction, strategic framing |
| Differentiator | What makes this program non-standard (access, speakers, format) |

### Minor contribution handling

If after reading the document the personal contribution appears limited (e.g., "one team member among fifteen, processed data for two weeks with no defined deliverable"): do not force a standalone entry. Present:

```
⚠️ Thin contribution detected in [filename].

Based on the document, your specific role is unclear or limited.
Options:
A) Confirm — was your contribution larger than described? (documents often under-represent individual roles)
B) Fold — use this as supporting context in another existing entry that covers similar work
C) Archive — note it in the session for future reference; do not add to profile.md

Which?
```

Never fabricate or inflate contribution to fill a template.

---

## Step 5 — Probing

After extraction, identify gaps and classify each as **blocking** or **enhancing**.

- **Blocking** — cannot draft a useful entry without this. Ask before drafting.
- **Enhancing** — entry can be drafted, but this would strengthen it. The user can skip.

Ask all probing questions in one message, labeled clearly. Never ask more than 3 questions total per document.

### Question bank by type

**Project — blocking:**
- "What was YOUR specific contribution vs. what the whole team did?" *(blocking if "we" appears throughout with no individual attribution)*

**Project — enhancing:**
- "Did the client implement or act on any recommendations? What happened?"
- "Was there a grade or external evaluation? What was it?"

**Experience — blocking:**
- "What was your most important deliverable — the thing your manager would remember you for?" *(blocking if deliverables are absent from the document)*

**Experience — enhancing:**
- "Any metric you can share: volume processed, time saved, cost impacted?"
- "Was any of your work adopted or continued after you left?"

**Association — blocking:**
- "What was the scale: members managed, budget, events, participants?" *(blocking if the document has no scale data)*

**Association — enhancing:**
- "What's the one achievement from this mandate you'd lead with in an interview?"

**Competition — blocking:**
- "What was your specific role in the team?" *(always ask — competition documents rarely attribute individual contribution)*

**Competition — enhancing:**
- "Is this the same work as an existing Projects entry in profile.md? Should both reference each other?"

Ask blocking questions first. If none are blocking, ask up to 2 enhancing questions. Wait for the user's answers before drafting.

---

## Step 6 — Draft YAML Entry

Write entries in **English** always. Apply all writing rules from `modes/writing.md` — bullet ordering, Specificity Standard, vocabulary bans.

Entries are YAML objects appended to the appropriate array in `config/profile.md`. What you draft here is what goes directly into the `.tex` output. One strong bullet beats two weak ones. Never pad.

### Entry template: Professional Experience → `experience` array

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

→ LaTeX command: `\cventry{Company}{Post}{Country}{Date}`

Rules:
- **post** = role and domain. E.g., "Stage Génie Civil", "Consultant Opérations, Supply Chain".
- Reverse chronological within the array.
- Maximum 3 bullets. Minimum 1 strong bullet.

---

### Entry template: Academic Project → `projects` array

```yaml
- title: "[Title ≤ 45 chars]"
  subtitle: "[Relevant clarification — client, framework, or differentiator. No tools. No country.]"
  date: "[mon. YYYY]"
  bullets:
    - "[Bullet 1 — most impressive outcome or contribution, verb-first]"
    - "[Bullet 2]"
    - "[Bullet 3 — max 3 total]"
```

→ LaTeX command: `\cventryprojet{Title}{Subtitle}{Date}` (title ≤ 45 chars — right-column limit)

Rules:
- **title** ≤ 45 characters. Apply title pattern from `modes/writing.md §19.1`.
- **subtitle** = apply pattern from `modes/writing.md §19.2`. No tools. No country.
- Position within array: decreasing interest to the interviewer — never by date.

---

### Entry template: Student Association → `student_life` array

```yaml
- title: "[Role, Organization]"
  date: "[mon. YYYY] -- [mon. YYYY]"
  bullets:
    - "[Bullet 1 — scale or concrete achievement]"
    - "[Bullet 2]"
    - "[Bullet 3 — max 3 total]"
```

→ LaTeX command: `\cventryassociatif{Title}{Date}`

Rules:
- **title** = role and organization separated by a comma. E.g., "Secretary General, BDI". No `·`.
- No subtitle, no country.

---

### Entry template: Competition / Hackathon → `competitions` array

```yaml
- title: "[Position, Contest Name]"
  subtitle: "[Organizer — context: foundation, company, institution. Include team count.]"
  date: "[mon. YYYY]"
  bullets:
    - "[Bullet 1 — result leads always]"
    - "[Bullet 2 — max 2 total]"
```

→ LaTeX command: `\cventrycontest{Title}{Subtitle}{Date}`

Rules:
- Include only if the result is externally verifiable (ranking, jury, prize).
- Do not duplicate content from a Projects entry covering the same work.

---

### Entry template: Training / Program → `additional_training` array

Entries in this array are ordered **reverse chronologically** (most recent first).

```yaml
- title: "[Program Name] — [Organizer]"
  date: "[mon. YYYY]"
  bullets:
    - "[Bullet 1 — access/selection signal or most differentiating content]"
    - "[Bullet 2 — max 2 total]"
```

Rules:
- Include only if it adds a differentiating signal not covered by main education.
- Lead with the access signal (competitive selection, executive interaction) if present.
- If `additional_training:` key does not exist in profile.md, create it after `competitions:`.

---

## Step 7 — Iterative Review and Append

After drafting the entry, present it in full and enter the review loop. **Nothing is written to `config/profile.md` until the user gives explicit approval.**

### Review loop

**Round 1 — Present the draft:**
```
Here's the draft entry for [filename] → [yaml_key]:

```yaml
[Full YAML entry object]
```

Tell me what to change, or say "add it" to write it to profile.md.
```

**Each subsequent round — Revise and re-present:**
Apply the user's requested changes. Show the full revised entry again — never show only the diff, always show the complete entry so the user can evaluate it as a whole.

```
Revised:

```yaml
[Full revised entry]
```

Anything else to change, or ready to add?
```

Repeat until the user gives explicit approval. Approval phrases: "add it", "looks good", "yes", "go ahead", "write it", or any clear affirmative that signals the review is done.

**On approval — write to profile.md:**
Run the Quality Check (§Quality Check Before Appending) on the approved version. If it passes, append the YAML object to the correct array in `config/profile.md`. Never touch any other array or key.

```
Added to profile.md → [yaml_key] array.

[If metadata update is warranted, proceed to §Profile Metadata Updates]
```

After writing the entry (and any YAML update), tell the user:
> "Entry added. Run `/career-ops pdf` with a job description to generate an updated CV."

### Rules
- Never write to `profile.md` during the review loop — only after explicit approval.
- Always show the complete entry on each revision round, not just what changed.
- If the user says "hold" or "not now", stop without writing anything.

---

## Improvement Mode for Already-Analyzed Sources

Triggered when a source file matches an existing entry in `profile.md` by title, company name, or content match (see Step 1b).

### Step A — Locate the existing entry

Find the full entry in `profile.md` (title, fields, bullets). Extract it verbatim.

### Step B — Re-read the source document

Apply the reading strategy from Step 2.5. Look specifically for:
- Facts, metrics, or details present in the source but missing from the existing entry
- Framing that is no longer the strongest angle
- Recruiter-lens violations in the existing entry (technical minutiae that crept in, buried lead)

### Step C — Decide if improvement is warranted

Only propose an improvement if there is a genuine reason:
- A metric or outcome in the source that is absent from the existing entry
- A recruiter-lens violation in the current text
- The title or role description doesn't match the source

Do NOT propose purely cosmetic rewrites or paraphrasing with no new information. If no genuine improvement exists, say so explicitly: "The existing entry already captures everything useful in this document. No changes proposed."

### Step D — Present and enter the review loop

Show the current entry and the proposed improvement side by side, then enter the same iterative loop as Step 7:

```
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
```

On each revision round, show the full proposed entry again. Never show only the diff.

### Step E — Apply on explicit approval

Only when the user gives explicit approval ("replace it", "looks good", "go ahead"):
- Locate the matching YAML object in the correct array by its `title:` or `company:` key value.
- Replace only that object. Never touch other objects in the same array or any other key.

If the user says "keep the current version" or "no changes" — do nothing.

---

## Profile Metadata Updates

After any entry is confirmed and appended to `config/profile.md`, check whether the identity/narrative section at the top should also be extended. This is optional — only offer when the new entry contains a genuinely strong proof point or competition result not yet captured there.

### When to offer a YAML update

| Entry type | YAML key to update | Condition |
|------------|-------------------|-----------|
| Any entry with a strong quantified outcome | `narrative.proof_points` | If the outcome is not already summarized there |
| Competition with external prize | `awards_and_competitions` | If the award is not already listed |
| New experience that changes the narrative | `narrative.headline` or `narrative.exit_story` | Only if the user explicitly requests it |

### How to update the metadata

1. Read the full `config/profile.md`.
2. Modify only the target key — do not touch any other key or array.
3. Write the updated file back. The CV section arrays (`education`, `experience`, `projects`, etc.) must remain untouched.

### Format for a new proof_point entry

```yaml
- name: "[Short project name]"
  hero_metric: "[One sentence: the most impressive verifiable outcome]"
```

### Format for a new awards_and_competitions entry

```yaml
- name: "[Prize name — Organization]"
  category: "hackathon / research_competition / case_competition / other"
  date: "YYYY-MM"
  result: "[Ranking or prize description]"
  context: "[1 sentence: format, number of teams, your role]"
```

### Offer format

After an entry is written to `profile.md`, if a metadata update is warranted:

```
Entry added to profile.md.

This result is strong enough to add to your profile summary.
Proposed addition to [narrative.proof_points / awards_and_competitions]:

[formatted YAML block]

Tell me what to adjust, or say "add it" to update.
```

Same iterative rule applies: show the proposed YAML, accept changes, only write on explicit approval.

---


## Quality Check Before Appending

For each entry, verify before writing to `profile.md`:

1. Every fact passes the recruiter lens — no technical minutiae, no methodology for its own sake
2. Personal contribution is explicitly separated from team work
3. At least one concrete, verifiable signal (metric, name, ranking, deliverable) per bullet — if none exists, use a qualitative signal (adoption, follow-up, feedback)
4. Language and writing style match the rest of the profile.md CV sections (verb-first bullets, no first person, no em-dashes); technical terms may remain in their original language in parentheses
5. Target array key (`experience`, `projects`, etc.) exists in the current profile.md; if `additional_training` is missing, it was created before appending
6. Entry format matches the YAML template for its type (correct keys, max bullets respected, title ≤ 45 chars for `projects` and `competitions`)
7. `additional_training` entries are in reverse chronological order within the array

Any failure → fix before appending.
