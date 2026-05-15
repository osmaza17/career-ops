# Mode — Ingest: portfolio.md → cv.md

## Purpose

Transform `config/portfolio.md` (raw professional corpus) into `config/cv.md` (polished, LaTeX-ready markdown). Every element produced here is consumed directly by `modes/latex.md` to generate `.tex` CVs — bullets must be job-ready without rewriting at generation time.

---

## Inputs and output

| | Path |
|---|---|
| Source | `config/portfolio.md` |
| Output | `config/cv.md` (overwrite) |

**Language:** cv.md is written in the **language of the job offer or description**. If no JD is provided, default to English. Spanish and English follow the verb-first bullet structure. French follows the deverbal noun structure — see Bullet Rules below.

---

## Step 1 — Parse portfolio.md

Read the full file. Separate two types of content:

1. **Trajectory content** — factual data: education, experience, projects, skills, languages, contact.
2. **`[META-INSTRUCCIÓN]` blocks** — read and internalize as behavioral directives. Never reproduce them in cv.md. They inform what to prioritize, what to pair together, and how to frame entries.

---

## Step 2 — Selection rules

Before writing any section, apply these filters:

| Section in portfolio.md | Default behaviour |
|---|---|
| SECCIÓN 0–6 | Include (apply per-entry rules below) |
| SECCIÓN 7 (Formación Complementaria) | Include only if it adds a differentiating signal not covered by the main education, and space allows |
| SECCIÓN 8 (Miscelánea) | Exclude by default. Include only if the META-INSTRUCCIÓN permits it or the user explicitly requests it |

**Paired entries:** Respect META-INSTRUCCIÓN pairings. BDI Secretary General and Spanish Club Treasurer must always appear together as adjacent entries.

---

## Step 3 — Write cv.md

### File structure

```
# [Full Name]

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
```

---

### Summary

Exactly 3 sentences:

1. Who you are + institution + graduation year (or expected).
2. 1–2 hard differentiators — use real figures, rankings, or unique facts from portfolio.md.
3. Type of role sought + area or contract type.

Rules: no first-person singular · no "passionate about" or equivalents · no em-dashes · do not list projects or experiences.

---

### Date format

All dates follow: `[mon. YYYY] -- [mon. YYYY]`

| Language | Month abbreviations |
|---|---|
| French (default) | `janv.` `févr.` `mars` `avr.` `mai` `juin` `juil.` `août` `sept.` `oct.` `nov.` `déc.` |
| English | `Jan.` `Feb.` `Mar.` `Apr.` `May` `June` `July` `Aug.` `Sept.` `Oct.` `Nov.` `Dec.` |

- Separator: `--` (double hyphen = en-dash in LaTeX). Never `-` or `—`.
- Single-month entry: `avr. 2026` (no separator).
- Ongoing: `oct. 2025 -- en cours` (FR) / `Oct. 2025 -- present` (EN).

---

### Education entries

```
### [Title]

**Subtitle:** [Institution]
**Date:** [mon. YYYY] -- [mon. YYYY]
**Country:** [Country]

- [Bullet: specialization or programme focus]
- [Bullet: quantitative achievement — GPA, ranking, award]
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
- **Post** = role and domain. E.g., "Stage en Génie Civil", "Consultant Opérations · Supply Chain".
- **Country** = country only, no city.

→ LaTeX: `\cventry{Company}{Post}{Country}{Date}` — displays as: line 1 = **COMPANY · Post** (bold); line 2 = Country (left, muted) and Date (right, muted).

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
- **Title** = role/position. If the organization adds signal, embed it in the title: "Secrétaire Général BDI", "Trésorier Club Espagnol".
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
- **Title** = what type of project it is — describes the TYPE (étude, modélisation, diagnostic, go-to-market, product launch...). If there is a named client, append after `·`, never at the start.
  - ✗ `Air Liquide · H₂ Supply Chain Optimisation`
  - ✓ `H₂ Supply Chain Optimisation · Air Liquide`
- **Subtitle** = relevant clarification: academic framework, program, team composition, or context. Use `·` to combine elements.
- **Never include the country of execution** in either Title or Subtitle. Country is irrelevant context in academic/consulting project entries — the school name or client name already carries the necessary signal.

→ LaTeX: `\cventryprojet{Title}{Subtitle}{Date}` (Title ≤ 45 chars — right-column limit).

Follow all Bullet Rules.

**Number of bullets:** write as many as there are genuinely strong things to say — minimum 1, maximum 3. One strong bullet is better than two weak ones. If only one thing is truly impressive, write one bullet and move on.

**Ordering:** decreasing order of what will interest the interviewer most. Default ranking:

1. **Competitive prizes** — ranked against other candidates, judged externally. Most credible signal.
2. **Real clients, especially well-known ones** — CAC 40, Fortune 500, named institutions. Shows real-world delivery.
3. **Projects with strong quantified outcomes** — regardless of client prestige, if the numbers are compelling.
4. **Technically sophisticated projects** — novel methodology, complex modelling, original design.
5. **Purely academic projects** — no external client, no prize. Go last.

When two entries tie on tier, the one with the harder metric or the more recognisable name goes first. Never order by date.

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
- **Title** = "[position] · [contest name]". E.g., "1er Prix · Programme Akademia".
- **Subtitle** = organizer or foundation. E.g., "Fondation Bankinter · Hackathon national, 80 équipes".
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

Rules: omit soft skills entirely · sort items within each row by relevance to the most common target role · maximum 5 rows.

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

## Bullet Rules

> Apply to all bullets in Experience, Student Life and Projects. Education bullets are factual statements — these rules do not apply there.

### Ordering

**R0 — Most impressive fact first.**

Within each entry, order bullets so the single most impactful line comes first. A recruiter who reads only the first bullet of each entry must leave with the best possible impression.

What counts as "most impressive": a hard metric, a named client or institution, a competition result, a unique outcome. If two bullets have a metric, the larger or more surprising one goes first.

✗ Method → result order:
```
Designed a proprietary dispatch heuristic (Simul8).
Won a zero-stockout fleet recommendation accepted by Air Liquide.
```
✓ Result → method order:
```
Won a **zero-stockout** fleet recommendation accepted by Air Liquide.
Designed a proprietary dispatch heuristic with a multi-component cost function (Simul8).
```

### Structure

Pattern: `[Past-tense verb] [object] [with/on/for/across] [specification] (tools, scale — max 4 elements).`

No subject — the verb stands alone, implying first person without writing "I".

| Rule | Requirement |
|---|---|
| R1 | Single sentence. ≤ 12 words preferred; 18 words hard limit. Ends with a period. |
| R2 | Opens with a past-tense action verb. Never a noun phrase, infinitive, gerund, or "Responsible for". |
| R3 | Tools, scales, KPIs always in parentheses at the end, separated by commas. Never in the prose. |
| R4 | One action per bullet. If two actions exist → split into two bullets. |
| R5 | Bold only on: (a) hard verifiable metric (figure, %, €, ranking), (b) domain methodology explicitly named in a JD. Max 2 bold elements per bullet. Never bold the verb. |
| R6 | Real figures only. If no metric exists, omit the number — a bullet without a figure is fine. If an entire entry has no quantifiable metric, compensate with specificity: name the client, state the scale, or give a concrete qualitative outcome. Never invent or approximate. |
| R7 | If the same idea fits in fewer words, use fewer words. Do not pad. |
| R8 | **Manager filter.** Every bullet must be useful to a non-specialist hiring manager. If the fact only makes sense to someone already deep in the implementation, cut the technical detail and keep only the outcome, scale, or competence signal. Ask: "Would a recruiter who doesn't know the domain find this useful?" If not, rewrite. |
| R9 | **Purpose clarity.** The first bullet of any project or experience entry must make clear (a) what was being done and (b) for whom or to what end. Do not lead with methodology when the project's purpose is still unknown to the reader. |

**Trim signals — rewrite if any of these appear:**
- Any em-dash (`—`): remove — replace with `·` if separating related elements, or rephrase entirely
- "through/via" followed by another long phrase → cut after the verb + object
- Two parenthetical groups in one bullet → keep only the more informative one
- Statistical/implementation configuration in prose (number of replicas, confidence intervals, warmup periods, parameter names) → cut from prose; name the method in parentheses, not its setup: ✗ `(Simul8, 4 replicas, 95% CI)` → ✓ `(Simul8)`
- Technical jargon with no managerial value (internal parameter names, solver settings, data structure details) → cut entirely unless they signal a rare, named competence
- Problem input dimensions that describe the problem's geometry, not the value delivered (distances, geographic extents, exact raw volumes) → cut. ✗ `for 5 stations across 50–200 km` → ✓ `for 5 delivery stations`. ✗ `carbon balance of 146,400 t CO₂` → state the conclusion only

### Verb examples (English)

Designed · Developed · Modelled · Optimised · Deployed · Analysed · Audited · Managed · Coordinated · Conducted · Formulated · Built · Led · Negotiated · Presented · Reduced · Quantified · Won · Maintained · Monitored

### Verb examples (French — only used when cv.md is NOT in French)

Conçu · Développé · Modélisé · Optimisé · Déployé · Analysé · Audité · Géré · Coordonné · Conduit · Formulé · Construit · Dirigé · Négocié · Présenté · Réduit · Quantifié · Obtenu · Maintenu

### French bullet structure — deverbal nouns

In French, opening a bullet with a past participle without a subject (`Conçu…`, `Recommandé…`) is grammatically incomplete. When cv.md is in French, replace the verb with its **deverbal noun**:

Pattern: `[Deverbal noun] de/d'/du/des [object] [complement] (tools, scale).`

| Past participle | Deverbal noun |
|---|---|
| Conçu / Développé | Conception de / Développement de |
| Recommandé | Recommandation de |
| Réduit | Réduction de |
| Identifié | Identification de |
| Proposé | Proposition de |
| Modélisé | Modélisation de |
| Formulé | Formulation de |
| Diagnostiqué | Diagnostic de |
| Géré | Gestion de |
| Piloté | Pilotage de |
| Coordonné | Coordination de |
| Obtenu | Obtention de |
| Déterminé | Détermination de |
| Élaboré | Élaboration de |
| Comparé | Comparaison de |

✗ `Conçu un algorithme de dispatch propriétaire (Simul8).`
✓ `Conception d'un algorithme de dispatch propriétaire (Simul8).`

All other rules (R1–R9, trim signals, self-check) apply equally.

### Banned

- Noun phrase opening: `Design of a tool…`, `Conception d'un outil…`
- Infinitive: `Develop a tool…`, `Développer un outil…`
- Gerund: `Developing…`, `Développant…`
- Explicit subject: `I developed…`, `J'ai développé…`
- Implicit first person: `In charge of…`, `Responsible for…`, `En charge de…`
- Connectors: `as well as` · `while` · `in order to` · `allowing` · `ainsi que` · `tout en` · `afin de` · `permettant de` · `qui a permis de`

### Globally banned vocabulary

passionate about · results-driven · team player · leveraged · spearheaded · synergy · go-getter · in order to · so as to

### Banned verbs (grandiose)

orchestrated · championed · catalyzed · pioneered · evangelized · galvanized · drove (as in "drove impact") · secured commitment

### Banned adjectives

strategic · transversal · complex · high-impact · ambitious · challenging · innovative · dynamic · rigorous · autonomous · significant · notable · key · critical · solid

### Examples

**Wrong form (noun phrase):**
✗ `Development of a Python tool enabling automation of logistics data processing.`
✓ `Developed a logistics data automation tool (Python, pandas).`

**Wrong form (too long — em-dash list):**
✗ `Ran the annual IKEA logistics operation for incoming international students — survey, consolidated order, on-campus delivery.`
✓ `Ran annual IKEA furniture logistics for incoming international students.`

**Wrong form (too long — "through" padding):**
✗ `Identified **5 critical operational bottlenecks** in a 1,242 m² facility through on-site observation and structured interviews.`
✓ `Identified **5 operational bottlenecks** in a 1,242 m² facility (on-site visits, interviews).`

**Wrong form (two actions):**
✗ `Concluded against Bokashi as a national solution and redirected recommendation toward integrated methanisation.`
✓ `Recommended methanisation over Bokashi after ruling it out as a national-scale solution.`

**Wrong form (grandiose verb):**
✗ `Orchestrated annual IKEA logistics operation for incoming international students.`
✓ `Ran annual IKEA furniture logistics for incoming international students.`

### Bullet self-check

Before writing the next bullet, verify the current one passes all nine:

Per bullet:
1. Opens with a past-tense verb (no subject, no noun phrase)?
2. ≤ 12 words? If not, can it be cut without losing meaning?
3. No em-dash, no "through/via" padding, no double parenthetical?
4. Tools and scales in parentheses, not prose?
5. Bold only on a hard metric or named methodology?
6. Free of banned adjectives, connectors, and grandiose verbs?
7. Exactly one action?
8. Would a non-specialist hiring manager find this useful? No implementation minutiae. (R8)
9. If this is the first bullet: is the purpose and beneficiary of the project/task immediately clear? (R9)

Per entry (after writing all bullets):
10. Is the most impressive bullet first?

Any failure → rewrite before moving on.

---

## After writing cv.md

Report to the user:
- Sections written and entry count per section.
- Any trajectory content excluded and why (e.g., SECCIÓN 8 entries held back per META-INSTRUCCIÓN).
- Any pairing or framing constraint applied from a META-INSTRUCCIÓN.
- Next step: "Run `/career-ops pdf` with a job description to generate a tailored LaTeX CV."
