# Mode — analyze-sources: Raw Documents → profile.md Entries

## Purpose

Transform raw academic documents (project reports, internship descriptions, student association files, competition submissions) stored in `sources/` into structured entries ready to append to `config/profile.md`. These entries feed the `ingest` pipeline that generates `cv.md`.

This mode applies a **recruiter/manager lens** throughout: it extracts what a non-specialist evaluating a profile would find meaningful — scale, outcomes, named clients, decisions, concrete deliverables — and discards technical minutiae that adds no signal to someone who hasn't read the source document.

---

## Exclusive Access

**This is the only mode that reads from `sources/`.** No other mode, agent, or script accesses that folder.

---

## How this mode works

This mode always processes **one document at a time**. Every entry goes through an iterative review loop before anything is written to `config/profile.md`. The user has final say at every step — the agent never writes to the file without explicit approval.

---

## Step 1 — Inventory, Deduplication, and Multi-file Grouping

Before anything else, run all three checks silently, then report once.

### 1a. List source files

List all files in `sources/` (excluding `README.md`).

### 1b. Deduplication check — two-pass

**Pass 1 — Fuente match (exact):**
Read `config/profile.md` and collect every `**Fuente:**` field value. If a source filename appears there, that experience is already analyzed.

**Pass 2 — Content match (fallback for legacy entries without a Fuente field):**
For any source file with no Fuente match, scan profile.md entry titles (`### …`) and company/organization names for a likely match to the source document. If found, flag as `PROBABLE MATCH (legacy entry, no Fuente field)` — do not proceed automatically; confirm with the user first.

### 1c. Multi-file grouping

If multiple source files clearly describe the same experience (e.g., `2024-06_internship_airfrance-brief.pdf` and `2024-12_internship_airfrance-final-report.pdf`), group them as one entry. Process together: the brief provides context, the final report provides outcomes and metrics. The resulting entry has one `**Fuente:**` listing both filenames.

### 1d. Report

```
Sources found:
1. [filename] — [type] — NEW
2. [filename] + [filename] — [type] — GROUPED (same experience, will merge)
3. [filename] — [type] — ALREADY ANALYZED (Fuente match → entry: "[title]", SECCIÓN X)
4. [filename] — [type] — PROBABLE MATCH (legacy entry "[title]" — confirm before proceeding)

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

**Target section by type:**

| Type | Default SECCIÓN |
|------|----------------|
| Experience | SECCIÓN 4 — Experiencia Profesional |
| Association | SECCIÓN 5 — Vida Asociativa |
| Project | SECCIÓN 6 — Proyectos e Innovación |
| Competition | SECCIÓN 7 — Concursos y Hackathones |
| Training | SECCIÓN 8 — Formación Complementaria |

Always verify the section exists in the current `profile.md` before inserting. If a section is missing, create it using the standard header format.

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

Source documents may be in French, English, or Spanish. **Read in the source language. Write the profile.md entry in Spanish**, matching the language of `config/profile.md`. Do not mix languages within an entry. Technical terms without a clean Spanish equivalent can remain in the original language in parentheses.

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
C) Archive — add to SECCIÓN 9 as a narrative reserve, not a primary entry

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

## Step 6 — Draft Entries

Write entries in **Spanish**, matching the language of `config/profile.md`. Use the templates below.

All entries end with a `[META-INSTRUCCIÓN]` block — see §META-INSTRUCCIÓN Writing Guide.

### Entry template: Professional Experience → SECCIÓN 4

```markdown
### [Company]

**Fuente:** [filename(s) from sources/]
**Tipo:** Experiencia profesional — [stage / prácticas / rol a tiempo parcial]
**Período:** [mes AAAA] – [mes AAAA]
**Puesto:** [Título exacto]
**País:** [País]

**Contexto:**
[1–2 frases: sector de la empresa, tamaño/tipo si es relevante, propósito de la misión]

**Contribución personal:**
- [Lo que hice concretamente — 3–5 puntos factuales, no el propósito del cargo]

**Entregables:**
- [Qué produje: informe, herramienta, modelo, recomendación, diseño]

**Resultados:**
- [Cualquier métrica o señal cuantificable: ahorro, volumen, impacto, feedback recibido]
- [Si no hay métrica: señal de calidad — herramienta adoptada permanentemente, recomendación implementada, solicitud de seguimiento]

**Competencias con señal para RRHH:**
[Solo competencias transferibles: gestión autónoma, interacción con cliente, entrega técnica, análisis cuantitativo]

> **[META-INSTRUCCIÓN]** [Ver §META-INSTRUCCIÓN Writing Guide]
```

---

### Entry template: Academic Project → SECCIÓN 6

```markdown
### [Tipo de estudio] · [Cliente o contexto]

**Fuente:** [filename(s) from sources/]
**Tipo:** [Proyecto académico / Estudio de consultoría / Estudio técnico]
**Marco:** [Asignatura o programa — institución]
**Período:** [mes AAAA] – [mes AAAA]
**Cliente:** [Nombre real, o "ejercicio académico sin cliente externo"]
**Equipo:** [N estudiantes — composición breve si es relevante]

**Objetivo:**
[1–2 frases: qué problema se resolvía, para quién, qué decisión debía tomar el cliente]

**Mi contribución específica:**
- [Lo que yo hice, diferenciado de lo que hizo el equipo — específico, no "participé en"]

**Resultados e impacto:**
- [Recomendación adoptada, calificación, premio, métrica alcanzada — lo que sea verificable]
- [Si no hay métrica: calidad de la recomendación, feedback del cliente, diferencial del enfoque]

**Señal diferenciadora:**
- [Lo que hace este proyecto más riguroso o difícil de replicar que el ejercicio estándar]

> **[META-INSTRUCCIÓN]** [Ver §META-INSTRUCCIÓN Writing Guide]
```

---

### Entry template: Student Association → SECCIÓN 5

```markdown
### [Organización] — [Rol/Cargo]

**Fuente:** [filename(s) from sources/]
**Tipo:** Vida asociativa — [cargo de dirección / miembro / responsable de área]
**Período:** [mes AAAA] – [mes AAAA]
**Cargo exacto:** [Título preciso]

**Escala:**
- [Número de miembros gestionados, presupuesto, eventos organizados, participantes alcanzados]

**Lo que hice concretamente:**
- [Tareas y responsabilidades reales — no el propósito de la asociación]
- [Segunda tarea si existe]
- [Tercera tarea si existe]

**Logro más destacable:**
- [Un hecho concreto, verificable, que mencionarías en 30 segundos en una entrevista]

> **[META-INSTRUCCIÓN]** [Ver §META-INSTRUCCIÓN Writing Guide]
```

---

### Entry template: Competition / Hackathon → SECCIÓN 7

```markdown
### [Resultado] · [Nombre del concurso]

**Fuente:** [filename(s) from sources/]
**Tipo:** Concurso / Hackathon / Competición académica
**Organizador:** [Entidad — añade contexto de credibilidad: fundación, empresa, institución]
**Fecha:** [mes AAAA]
**Equipo:** [N participantes, si aplica]

**El resultado:**
- [Ranking o premio exacto — lidera siempre]

**El problema:**
- [1–2 frases: qué se pedía y cuál fue el enfoque del equipo]

**Mi contribución:**
- [Qué aportaste tú específicamente]

**Overlap:**
- [¿Existe ya un entry de Proyectos para este trabajo? → Indicar si es el mismo ángulo o diferente]

> **[META-INSTRUCCIÓN]** [Ver §META-INSTRUCCIÓN Writing Guide]
```

---

### Entry template: Training / Program → SECCIÓN 8

Entries in SECCIÓN 8 are ordered **reverse chronologically** (most recent first). Append the new entry at the top of the section if it is the most recent, or insert it at the correct chronological position otherwise.

```markdown
### [Nombre del programa] — [Organizador]

**Fuente:** [filename(s) from sources/]
**Tipo:** Formación complementaria — [programa ejecutivo / certificación / programa competitivo]
**Período:** [mes AAAA] [– mes AAAA si aplica]

**Contexto:**
[1–2 frases: quién organiza, a quién va dirigido, cómo se accede — especialmente si el acceso fue selectivo]

**Contenidos con señal:**
- [Solo módulos o sesiones con valor recruiter: exposición sectorial, interacción con ejecutivos, marco estratégico]

**Señal diferenciadora:**
- [Lo que distingue este programa: acceso competitivo, perfil de participantes, interlocutores de nivel]

> **[META-INSTRUCCIÓN]** [Ver §META-INSTRUCCIÓN Writing Guide]
```

---

## META-INSTRUCCIÓN Writing Guide

Every entry ends with a `[META-INSTRUCCIÓN]` block. Write it as a behavioral directive for the `ingest` mode — not as a description of the content. It is invisible in the final CV.

Include:
1. **What to lead with** — which fact or bullet should open the entry in the CV
2. **Which roles will value it most** — so `ingest` can weight it differently per JD
3. **Any pairing** — if this entry must appear adjacent to another
4. **Anything to suppress** — details that should never appear in cv.md even if present in profile.md

Example:
```
> **[META-INSTRUCCIÓN]** Liderar con la recomendación aceptada por el cliente, no con la metodología. Valor especial ante roles de consultoría, supply chain y operaciones. Si el CV incluye el proyecto de Air Liquide, colocar este entry inmediatamente después — refuerzan la misma narrativa cuantitativa.
```

---

## Step 7 — Iterative Review and Append

After drafting the entry, present it in full and enter the review loop. **Nothing is written to `config/profile.md` until the user gives explicit approval.**

### Review loop

**Round 1 — Present the draft:**
```
Here's the draft entry for [filename] → SECCIÓN X:

---
[Full entry, formatted exactly as it would appear in profile.md]
---

Tell me what to change, or say "add it" to write it to profile.md.
```

**Each subsequent round — Revise and re-present:**
Apply the user's requested changes. Show the full revised entry again — never show only the diff, always show the complete entry so the user can evaluate it as a whole.

```
Revised:

---
[Full revised entry]
---

Anything else to change, or ready to add?
```

Repeat until the user gives explicit approval. Approval phrases: "add it", "looks good", "yes", "go ahead", "write it", or any clear affirmative that signals the review is done.

**On approval — write to profile.md:**
Run the Quality Check (§Quality Check Before Appending) on the approved version. If it passes, append the entry to the correct SECCIÓN in `config/profile.md`. Never touch any other content in the file.

```
Added to profile.md → SECCIÓN X.

[If YAML update is warranted, proceed to §YAML Frontmatter Updates]

Next step: run `/career-ops ingest` to regenerate cv.md.
```

### Rules
- Never write to `profile.md` during the review loop — only after explicit approval.
- Always show the complete entry on each revision round, not just what changed.
- If the user says "hold" or "not now", stop without writing anything.

---

## Improvement Mode for Already-Analyzed Sources

Triggered when a source file matches an existing `**Fuente:**` field in `profile.md` (or is confirmed as a probable match for a legacy entry).

### Step A — Locate the existing entry

Find the full entry in `profile.md` (title, all fields, bullets, META-INSTRUCCIÓN). Extract it verbatim.

### Step B — Re-read the source document

Apply the reading strategy from Step 2.5. Look specifically for:
- Facts, metrics, or details present in the source but missing from the existing entry
- Framing that is no longer the strongest angle
- Recruiter-lens violations in the existing entry (technical minutiae that crept in, buried lead)
- META-INSTRUCCIÓN that could be more actionable

### Step C — Decide if improvement is warranted

Only propose an improvement if there is a genuine reason:
- A metric or outcome in the source that is absent from the existing entry
- A recruiter-lens violation in the current text
- A META-INSTRUCCIÓN that is missing or weak
- The title or role description doesn't match the source

Do NOT propose purely cosmetic rewrites or paraphrasing with no new information. If no genuine improvement exists, say so explicitly: "The existing entry already captures everything useful in this document. No changes proposed."

### Step D — Present and enter the review loop

Show the current entry and the proposed improvement side by side, then enter the same iterative loop as Step 7:

```
## Already analyzed: [filename]

**Current entry in profile.md:**
---
[Full current entry, verbatim]
---

**Proposed improved version:**
---
[Full proposed entry]
---

**What changed and why:**
- [Specific change]: [reason]

Tell me what to adjust, or say "replace it" to overwrite the current entry.
```

On each revision round, show the full proposed entry again. Never show only the diff.

### Step E — Apply on explicit approval

Only when the user gives explicit approval ("replace it", "looks good", "go ahead"):
- Replace only the entry block in `profile.md` (from its `### [Title]` heading to the next `---` separator). Never touch anything outside that block.

If the user says "keep the current version" or "no changes" — do nothing.

---

## YAML Frontmatter Updates

After any entry is confirmed and appended to `config/profile.md`, check whether the YAML frontmatter should also be extended. This is optional — only offer when the new entry contains a genuinely strong proof point or competition result not yet in the YAML.

### When to offer a YAML update

| Entry type | YAML key to update | Condition |
|------------|-------------------|-----------|
| Any entry with a strong quantified outcome | `narrative.proof_points` | If the outcome is not already summarized there |
| Competition with external prize | `awards_and_competitions` | If the award is not already listed |
| New experience that changes the narrative | `narrative.headline` or `narrative.exit_story` | Only if the user explicitly requests it |

### How to update the YAML frontmatter

1. Read the full YAML frontmatter block from `config/profile.md` (between the `---` delimiters).
2. Modify only the target key — do not touch any other key.
3. Write the updated frontmatter back to the file, replacing only the block between the `---` delimiters. The markdown body must remain untouched.

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

After an entry is written to `profile.md`, if a YAML update is warranted:

```
Entry added to profile.md.

This result is strong enough to add to your YAML profile summary.
Proposed addition to [narrative.proof_points / awards_and_competitions]:

[formatted YAML block]

Tell me what to adjust, or say "add it" to update the YAML.
```

Same iterative rule applies: show the proposed YAML, accept changes, only write on explicit approval.

---


## Quality Check Before Appending

For each entry, verify before writing to `profile.md`:

1. Every fact passes the recruiter lens — no technical minutiae, no methodology for its own sake
2. Personal contribution is explicitly separated from team work
3. At least one concrete, verifiable signal (metric, name, ranking, deliverable) per entry — if none exists, "Resultados" section notes a qualitative signal (adoption, follow-up, feedback) instead of being empty
4. META-INSTRUCCIÓN is a directive, not a description — it tells `ingest` what to do, not what the entry contains
5. Language is Spanish throughout, consistent with profile.md style; technical terms may remain in their original language in parentheses
6. Target SECCIÓN exists in the current profile.md
7. `**Fuente:**` field is present with the exact filename(s) from `sources/`
8. SECCIÓN 8 entries are in reverse chronological order within the section

Any failure → fix before appending.
