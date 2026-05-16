# Mode — Ingest: profile.md → cv.md

## Purpose

Transform `config/profile.md` (YAML frontmatter + trajectory corpus) into `config/cv.md` (polished, LaTeX-ready markdown). Every element produced here is consumed directly by `modes/latex.md` to generate `.tex` CVs — bullets must be job-ready without rewriting at generation time.

---

## Inputs and output

| | Path |
|---|---|
| Source | `config/profile.md` |
| Output | `config/cv.md` (overwrite) |

**Language:** cv.md is written in the **language of the job offer or description**. If no JD is provided, default to English. Spanish and English follow the verb-first bullet structure. French follows the deverbal noun structure — see Bullet Rules below.

---

## Writing Conventions

All writing style rules (bullet ordering, bold, parentheses, colon, middle-dot, triple-dash, Specificity Standard, Headline, Hobbies, trim signals, vocabulary) are centralized in `modes/writing.md`. Read and apply that file before generating any output.

---

## Step 1 — Parse profile.md

Read the full file. Separate three types of content:

1. **YAML frontmatter** (between the `---` delimiters at the top) — structured data. Read these keys directly:
   - Identity and contact: `candidate.*` and `location.*` → used for the cv.md header
   - Languages: `languages` array → used for the Languages section in cv.md
   - Target roles and narrative: `target_roles.*`, `narrative.*` → context for framing
   - Section 0 and Section 2 in the markdown body are placeholders that redirect here — do not look for content there.

2. **Trajectory content** — factual data in the markdown body: education (Section 1), skills (Section 3), experience (Section 4), student life (Section 5), projects (Section 6), competitions (Section 7), training (Section 8). Section 9 is excluded by default — see selection rules.

3. **`[META-INSTRUCCIÓN]` blocks** — read and internalize as behavioral directives. Never reproduce them in cv.md. They inform what to prioritize, what to pair together, and how to frame entries.

---

## Step 2 — Selection rules

Before writing any section, apply these filters:

| Section in profile.md body | Default behaviour |
|---|---|
| Section 0–6 | Include (apply per-entry rules below) |
| Section 7 — Competitions and Hackathons | Include only if the result is externally verifiable (ranking, jury, prize) and not already fully covered by a Projects entry |
| Section 8 — Additional Training | Include only if it adds a differentiating signal not covered by the main education, and space allows |
| Section 9 — Miscellaneous (archive) | Exclude by default. Include only if the META-INSTRUCCIÓN permits it or the user explicitly requests it |

**Paired entries:** Respect any pairing directives defined in `[META-INSTRUCCIÓN]` blocks within `config/profile.md`. Never infer or hardcode pairings — read them from the file.

---

## Step 3 — Write cv.md

### File structure

```
# [Full Name]

**Headline:** [Professional title] specialized in [specialization]
**Email:** ... | **Phone (ES):** ... | **Phone (FR):** ...
**LinkedIn:** ... | **Location:** ...

---

## Summary

[3-sentence paragraph]

---

## Education

[entries]

---

## Experience

[entries]

---

## Student Life

[entries]

---

## Projects

[entries]

---

## Competitions & Hackathons

[entries — optional]

---

## Skills

[rows]

---

## Languages

[rows]

---

## Hobbies

[rows — optional]
```

---

### Headline

→ LaTeX: `\cvheadline{...}` — apply the fixed Headline structure from `modes/writing.md §9`.

---

### Summary

Three sentences following the Summary structure in `modes/writing.md §15`. Named clients and verified results from `narrative.proof_points` in the YAML frontmatter are mandatory in S2.

---

### Date format

Apply date format from `modes/writing.md §16`. Separator: `--` (double hyphen). Never `-` or `—`.

---

### Education entries

```
### [Title]

**Subtitle:** [Institution]
**Date:** [mon. YYYY] -- [mon. YYYY]
**Country:** [Country]

- [Bullet: specialization or programme focus]
- [Bullet: quantitative achievement — "Average X/10 --- top Y% of N students". Use `---` to link score and ranking. Never "GPA".]
- [Bullet: unique milestone — optional]
```

Fields:
- **Title** = exact degree name only. ≤ 45 characters (right-column hard limit — `modes/latex.md §3d`).
- **Subtitle** = institution name only.
- **Country** = country only, no city.

→ LaTeX: `\cventryformation{Title}{Subtitle}{Date}{Country}`

Rules:
- Reverse chronological order.
- Maximum 3 bullets per entry.
- Priority order: specialization → quantitative achievement → unique milestone.
- Bullets in Education do NOT follow the deverbal noun pattern — they are factual statements.

---

### Experience entries

```
### [Company]

**Post:** [Post in X domain]
**Country:** [Country]
**Date:** [mon. YYYY] -- [mon. YYYY]

- [Bullet]
- [Bullet — max 3]
```

Fields:
- **Company** = company name as written (LaTeX will uppercase it automatically). E.g., "Bucarest 54", "Air Liquide".
- **Post** = role and domain. E.g., "Stage en Génie Civil", "Consultant Opérations, Supply Chain".
- **Country** = country only, no city.

→ LaTeX: `\cventry{Company}{Post}{Country}{Date}` — displays as: line 1 = **COMPANY · Post** (bold, the `·` here is structural inside the LaTeX command); line 2 = dates (left, muted) and Country (right, muted).

Follow all Bullet Rules. Write as many bullets as there are genuinely strong things to say — minimum 1, maximum 3. One strong bullet beats two weak ones.

---

### Student Life entries

```
### [Title]

**Date:** [mon. YYYY] -- [mon. YYYY]

- [Bullet]
- [Bullet — max 3]
```

Fields:
- **Title** = role/position. Use a comma to separate role and organization — never `·`. E.g., "Secretary General, BDI", "Treasurer, Club Español".
- No **Subtitle** — `\cventryassociatif` has no subtitle argument; omit this field entirely.
- No **Country** field.

→ LaTeX: `\cventryassociatif{Title}{Date}`

Follow all Bullet Rules. Write as many bullets as there are genuinely strong things to say — minimum 1, maximum 3. Pair BDI Secretary General and Spanish Club Treasurer as adjacent entries.

---

### Projects entries

```
### [Title]

**Subtitle:** [Relevant clarification about the project]
**Date:** [mon. YYYY] (or [mon. YYYY] -- [mon. YYYY] if range)

- [Bullet]
- [Bullet — max 3]
```

Fields:
- **Title** = apply the project title pattern from `modes/writing.md §19.1`. Title ≤ 45 characters (right-column hard limit — `modes/latex.md §3d`).
- **Subtitle** = apply the subtitle pattern from `modes/writing.md §19.2`. Do NOT list tools; no country of execution.

→ LaTeX: `\cventryprojet{Title}{Subtitle}{Date}` (Title ≤ 45 chars — right-column limit).

Follow all Bullet Rules.

**Number of bullets:** write as many as there are genuinely strong things to say — minimum 1, maximum 3. One strong bullet is better than two weak ones. If only one thing is truly impressive, write one bullet and move on.

**Ordering:** decreasing order of interest to the interviewer — apply the project entry ordering from `modes/writing.md §1.3`. Never order by date.

---

### Competitions & Hackathons entries

```
### [Title]

**Subtitle:** [Organizing body]
**Date:** [mon. YYYY]

- [Bullet]
- [Bullet — max 2]
```

Fields:
- **Title** = "[position], [contest name]". E.g., "1st Prize, Programme Akademia".
- **Subtitle** = organizer or foundation. E.g., "Fondation Bankinter, Hackathon national, 80 teams".
- No **Country** field.

→ LaTeX: `\cventrycontest{Title}{Subtitle}{Date}`

Include only if:
- (a) The result reinforces a claim in Profile or Projects, or
- (b) The award is verifiable (ranking, jury, prize) and not already present as a Projects entry.

Maximum 2 bullets. Do not duplicate content from a Projects entry covering the same work.

---

### Skills

```
## Skills

- **Programming:** Python, SQL, MATLAB
- **Optimisation:** Gurobi, Simul8, MILP, MCDA
- **Analytics & BI:** Power BI, Excel, AutoCAD, CYPE
- **Tools:** LaTeX, Office Suite
- **AI & Automation:** Claude Code, n8n, LLM
```

Apply Skills section rules from `modes/writing.md §20`.

---

### Languages

```
## Languages

- **Spanish:** Native
- **English:** Cambridge C1 Advanced
- **French:** DELF B2 (daily academic and professional use at CentraleSupélec)
- **German:** A2
```

Include certificate name when it exists.

---

### Hobbies

```
## Hobbies

[one line — optional]
```

Apply Hobbies inclusion criteria and format from `modes/writing.md §10`.

→ LaTeX: `\section*{Centres d'intérêt}` (optional section — see `modes/latex.md §7.10`).

---

## Bullet Rules

Apply all rules from `modes/writing.md` (R0–R9, trim signals, self-check, colon convention, Specificity Standard, deverbal nouns for French, vocabulary bans). Education bullets are factual statements — R0–R9 do not apply there.

---

## After writing cv.md

Report to the user:
- Sections written and entry count per section.
- Any trajectory content excluded and why (e.g., Section 9 — Miscellaneous entries held back per META-INSTRUCCIÓN).
- Any pairing or framing constraint applied from a META-INSTRUCCIÓN.
- Next step: "Run `/career-ops pdf` with a job description to generate a tailored LaTeX CV."
