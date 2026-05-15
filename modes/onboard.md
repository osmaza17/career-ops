# Mode — Onboard: Build config/profile.md from Scratch

## Purpose

Guide the user through creating a complete `config/profile.md` — the single source of truth for career-ops. This mode handles everything: the YAML frontmatter (identity, target roles, narrative, compensation, languages) and the full trajectory corpus (education, experience, projects, associations, competitions, training).

Once `profile.md` is complete, run `modes/ingest.md` to generate `config/cv.md`.

---

## Inputs and Output

| | Path |
|---|---|
| Template | `templates/profile.template.md` |
| Sources (optional) | `sources/` — raw documents (PDFs, text files) the user wants processed |
| Output | `config/profile.md` (created) |

**If `config/profile.md` already exists:** do not overwrite. Ask the user whether they want to (a) continue filling in missing sections, or (b) start fresh. If starting fresh, rename the existing file to `config/profile.md.bak` before proceeding.

---

## How this mode works

Two phases, in order:

1. **Phase 1 — YAML Frontmatter**: structured interview to populate all YAML keys
2. **Phase 2 — Trajectory Corpus**: populate the markdown body sections (education, experience, projects, etc.)

Nothing is written to disk until the user confirms at each step. The user has final say throughout.

---

## Phase 1 — YAML Frontmatter Interview

### Setup

Copy `templates/profile.template.md` → `config/profile.md` (or load the template into memory if the file already exists and the user chose to continue filling it).

Ask the five question groups in order. Send each group as a single message. Wait for the answer before moving to the next. Never ask all five at once.

---

### Group 1 — Identity

> "Let's build your profile — this is the source of truth the whole system reads from.
>
> First, the basics:
> - Full name (as it should appear on your CV)
> - Primary email
> - Phone number(s) — if you have separate numbers for different countries, tell me both
> - LinkedIn URL
> - University or institutional email (if you have one and want it on your CV) — skip if none
> - Current city and country"

Map answers to YAML keys:
- `candidate.full_name`, `candidate.email`
- `candidate.phone_es` / `candidate.phone_fr` / `candidate.phone` (use the single-key form if only one number)
- `candidate.linkedin`
- `candidate.institutional_email` (omit the line entirely if they say none)
- `location.city`, `location.country`, `location.timezone` (infer timezone from country/city)

---

### Group 2 — Target Roles

> "What roles are you targeting?
> - Your top 2–3 target job titles (e.g. 'Operations Research Analyst', 'Strategy Consultant')
> - For each title: is it a dream role (primary), a strong fit (secondary), or a stretch (adjacent)?
> - What level are you targeting? (e.g. Junior, Mid, Senior)"

Map answers to YAML keys:
- `target_roles.primary` — the job titles
- `target_roles.archetypes` — one entry per title with `name`, `level`, and `fit` (`primary` / `secondary` / `adjacent`)

---

### Group 3 — Narrative

> "Now the most important part — your story:
> - In one line, how would you describe yourself professionally? (e.g. 'Industrial engineer from X — specializing in Y')
> - What makes you stand out from other candidates at your level? Give me 2–3 specific differentiators.
> - What are your top 1–3 achievements so far — the ones you'd lead with in any interview? For each, give me the outcome in one sentence with a number if possible."

Map answers to YAML keys:
- `narrative.headline`
- `narrative.superpowers` — one item per differentiator
- `narrative.proof_points` — one entry per achievement: `name` (short label) + `hero_metric` (one sentence, metric-led)

**If the user is vague** (e.g., "I'm good at problem-solving"): ask one follow-up — "Can you give me a concrete example — a project, a result, something a reference could verify?" Never fabricate or paraphrase into something more impressive than what they said.

---

### Group 4 — Compensation

> "Compensation:
> - What's your target salary range? (include currency and gross/net)
> - What's your walk-away minimum? (private — only used by the negotiation mode, never shown to companies)
> - Relocation and remote: are you open to relocating? Any location or remote work restrictions?"

Map answers to YAML keys:
- `compensation.target_range`, `compensation.currency`
- `compensation.minimum`
- `compensation.location_flexibility`

---

### Group 5 — Languages, Visa, and Awards

> "Two final things:
> - What languages do you speak? For each: name, level, and certificate if you have one (e.g. 'English — C1 Advanced, Cambridge certificate')
> - Any prizes or ranked competitive results? (e.g. '1st prize at X competition, 80 teams') — skip if none"

Map answers to YAML keys:
- `languages` — one entry per language: `language` + `level` (include certificate in the level string: e.g. `"C1 Advanced (Cambridge certificate)"`)
- `awards_and_competitions` — for each prize: `name`, `category`, `date` (YYYY-MM), `result`, `context`. If none, leave the block empty.

Also set `location.visa_status` — infer from nationality/location if mentioned in Group 1. If not clear, ask:
> "One more: what's your visa status? (e.g. 'EU citizen — no sponsorship needed within EU' / 'Requires visa sponsorship')"

---

### YAML Review

After all five groups, assemble the complete YAML frontmatter and present it in full:

```
Here's your profile YAML — review everything before I save it:

---
[full YAML block]
---

Tell me what to change, or say "looks good" to save it.
```

Iterate until the user confirms. Only write to `config/profile.md` on explicit approval — replace the YAML frontmatter block (between the `---` delimiters) in the template. The markdown body sections remain as empty placeholders until Phase 2.

---

## Phase 2 — Trajectory Corpus

After the YAML is saved, automatically inventory `sources/` before doing anything else. Do not ask the user which method they prefer — check first, then report.

---

### Step 1 — Automatic Sources Inventory

List all files in `sources/` (excluding `README.md` if present).

**If the folder is empty or doesn't exist**, skip to Step 3 (Conversation Collection) and open with:
> "YAML saved. The `sources/` folder is empty — let's fill in your trajectory through a short interview. You can also drop documents into `sources/` at any point and I'll process them."

**If files are found**, read each file (applying the reading strategy below) and silently extract a classification and a one-line summary. Then report the full inventory before proposing anything:

```
YAML saved. I found [N] documents in sources/ — here's what I can build from them:

1. [filename]
   Type: [project report / internship doc / association file / competition / training / other]
   Would produce: [one sentence — e.g. "an Experience entry for Air Liquide (internship, Jun–Aug 2024)"]

2. [filename]
   Type: [...]
   Would produce: [one sentence]

[... for each file]

I'll process them one by one and show you a draft entry for each before writing anything.
Shall I start with [filename 1], or do you want a different order?
```

**Reading strategy for the inventory scan** (efficient — stop when classification and summary are clear):
- Short doc (< 5 pages): read in full
- Long doc: cover/title page → executive summary → conclusion → results section. Stop when you have enough to classify and summarize.

---

### Step 2 — Process Each Sources File

For each file in the confirmed order, apply the full extraction and review logic from `modes/analyze-sources.md`:

- **Classify** the document (project / experience / association / competition / training)
- **Read efficiently** — cover → summary → conclusions → results (skip methodology, proofs, appendices unless a key metric lives there)
- **Apply the recruiter lens** — extract outcomes, scale, named clients, deliverables; suppress methodology, solver settings, parameter names, implementation minutiae
- **Ask probing questions** if needed — blocking questions first (those that cannot be drafted without the answer), max 3 per document, wait for answers before drafting
- **Draft the entry** using the appropriate template from `modes/analyze-sources.md`
- **Propose the draft** for validation (see Review Loop below) — nothing is written until the user approves
- **YAML update** — after each approved entry, offer to add a proof point or award to the YAML frontmatter if the entry contains a strong verifiable outcome

---

### Step 3 — Conversation Collection (empty sections)

After processing all sources files (or immediately if `sources/` was empty), check which trajectory sections are still empty or contain only placeholder text. For each empty section, ask structured questions in order.

Only ask about sections not yet filled. If all sections are filled by sources, skip this step entirely.

#### Section 1 — Education

> "Tell me about your education — most recent first.
> For each degree: institution, exact degree name, start and end dates, country, and any notable result (GPA, ranking, award, or distinction)."

Structure each entry as:
```markdown
### [Exact degree name]

**Institution:** [Name]
**Period:** [mon. YYYY] – [mon. YYYY]
**Country:** [Country]

**Notable:** [GPA / ranking / award — omit line if nothing notable]
```

#### Section 3 — Technical Skills

> "List your technical tools and skills — grouped by category if possible. Omit soft skills.
> Example categories: Programming · Optimisation · Analytics & BI · Design · Project Management · AI & Automation"

Structure as a grouped list.

#### Section 4 — Professional Experience

> "Tell me about your professional experience — internships, jobs, placements — most recent first.
> For each: company, role/title, dates, country, and what you actually did (deliverables, outcomes, metrics if you have them)."

Use the Professional Experience entry template from `modes/analyze-sources.md`. Ask one probing question if deliverables are vague:
> "What's the one thing your manager would remember you for — the most concrete deliverable or outcome from this role?"

#### Section 5 — Student Life and Associations

> "Any student clubs, associations, or elected roles?
> For each: organization name, your exact role title, dates, and scale (members managed, events organized, budget if relevant)."

Use the Association entry template from `modes/analyze-sources.md`.

#### Section 6 — Projects

> "Academic projects, consulting studies, or personal technical projects — most impactful first.
> For each: project type, client or context, dates, team size, and what you produced, recommended, or built."

Use the Academic Project entry template from `modes/analyze-sources.md`. Apply project ordering: competitive prizes → real clients → strong metrics → technical sophistication → purely academic.

#### Section 7 — Competitions and Hackathons

> "Any competitions or hackathons with an external ranking or prize?
> For each: result (prize/ranking), organizer, date, and what you built or solved."

Use the Competition entry template from `modes/analyze-sources.md`. Only include externally verifiable results.

#### Section 8 — Additional Training

> "Any courses, certificates, or extracurricular programs worth including?
> For each: program name, organizer, dates, and what made it notable (selective access, executive interaction, strong network)."

Structure in reverse chronological order (most recent first).

---

### Review Loop

After drafting each entry (from sources or from conversation), present it in full before writing anything:

```
Draft entry for [Section X — type]:

---
[Full entry, formatted exactly as it would appear in profile.md]
---

Tell me what to change, or say "add it" to write it to profile.md.
```

On each revision round, show the complete entry again — never just the diff. Only write to `config/profile.md` on explicit approval ("add it", "looks good", "yes", "go ahead", or any clear affirmative).

**If the user drops new files into `sources/` mid-session** (during conversation collection), pause the current section, switch back to Step 2 for those files, then resume where you left off.

---

## Rules

- Never write to `config/profile.md` without explicit user approval at each step.
- Never fabricate metrics, invent outcomes, or inflate contributions. If no metric exists, omit the number — an entry without a figure is fine.
- If the user says "skip" for any section, add a comment in that section: `*(left empty — add content later with analyze-sources or direct input)*`
- If the user drops files into `sources/` mid-session (during Path B), switch to Path A for those files without losing progress on sections already filled.
- Section 9 (Miscellaneous / archive) is never asked about during onboarding — it is populated over time by `analyze-sources`.
- All entries written to the markdown body must include a `[META-INSTRUCCIÓN]` block — see the META-INSTRUCCIÓN Writing Guide in `modes/analyze-sources.md`.

---

## Completion

Once all sections are filled (or the user has explicitly skipped sections):

> "Your profile is complete. Here's what was built:
>
> **YAML:** identity · [N] target roles · narrative · compensation · [N] languages[· N awards]
> **Trajectory:**
> - Section 1 — Education: [N entries / empty]
> - Section 3 — Skills: [filled / empty]
> - Section 4 — Experience: [N entries / empty]
> - Section 5 — Associations: [N entries / empty]
> - Section 6 — Projects: [N entries / empty]
> - Section 7 — Competitions: [N entries / empty]
> - Section 8 — Training: [N entries / empty]
>
> Want me to generate your CV now from this profile?"

Run `modes/ingest.md` only if the user confirms. If they decline, do nothing — they can trigger it later with `/career-ops ingest`.
