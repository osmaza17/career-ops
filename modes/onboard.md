# Mode — Onboard: Build config/profile.md from Scratch

## Purpose

Guide the user through creating a complete `config/profile.md` — the single source of truth for career-ops. This file is a pure YAML document. It feeds every mode: identity, CV sections, strategy, narrative, and compensation all live here.

Once `profile.md` is complete, run `/career-ops pdf` with a job description to generate a tailored `.tex` CV directly.

---

## Inputs and Output

| | Path |
|---|---|
| Sources (optional) | `sources/` — raw documents the user wants processed |
| Output | `config/profile.md` (created) |

**If `config/profile.md` already exists:** do not overwrite. Ask the user whether they want to (a) continue filling in missing sections, or (b) start fresh. If starting fresh, rename the existing file to `config/profile.md.bak` before proceeding.

---

## How this mode works

Two phases, in order:

1. **Phase 1 — YAML Interview**: structured interview to populate all identity, strategy, and metadata keys
2. **Phase 2 — CV YAML Sections**: build the `summary`, `education`, `experience`, `student_life`, `projects`, `competitions`, `skills`, and `awards_and_competitions` arrays

Nothing is written to disk until the user confirms at each step. The user has final say throughout.

---

## Phase 1 — YAML Interview

Ask the seven question groups in order. Send each group as a single message. Wait for the answer before moving to the next. Never ask all groups at once.

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
- `candidate.institutional_email` (omit the key entirely if they say none)
- `location.city`, `location.country`, `location.timezone` (infer timezone from country/city)

---

### Group 2 — Target Roles

> "What roles are you targeting?
>
> - Your primary job title (the one you'd write in a LinkedIn headline)
> - Then 2–4 role archetypes — these are the distinct role flavors you're open to. For each, tell me:
>   - Role name
>   - Level (e.g. Junior, Alternant, Mid, Senior)
>   - Fit: primary (dream), secondary (strong fit), or stretch (adjacent)
>   - What domains or skills define this role? (2–4 words — e.g. 'Problem structuring, quantitative analysis, client delivery')
>   - In one sentence starting with 'Someone who…', what does the buyer (the hiring manager) want? (e.g. 'Someone who translates ambiguous mandates into structured deliverables')"

Map answers to YAML keys:
- `target_roles.primary` — list of job titles
- `target_roles.archetypes` — one entry per archetype: `name`, `level`, `fit`, `thematic_axes`, `value_proposition`

If the user gives a list of roles without the archetype details, ask for `thematic_axes` and `value_proposition` per role before moving on — these two fields are used by the evaluation and CV modes and cannot be left empty.

---

### Group 3 — Narrative

> "Now your story — the part that makes you sound like a person, not a list of jobs.
>
> 1. **Headline** — one line: how would you describe yourself professionally? (e.g. 'Industrial engineer from X — specializing in Y and Z')
> 2. **Short exit story** — 3–4 sentences, third-person, written as if a recruiter is describing you. What's your arc, your differentiator, and what you're looking for?
> 3. **Long version** — same story, first person, 4–6 sentences. This is for LinkedIn summaries and cover letter openers.
> 4. **Usage notes** — very briefly: when do you use the short version vs. the long one? (e.g. 'Short: PDFs and recruiter intros. Long: LinkedIn About and cold emails.')
> 5. **Superpowers** — 2–3 concrete differentiators. Not soft skills — things a reference could verify. (e.g. 'First student from UPV to do this exchange', 'MILP optimization adopted by 15+ associations')
> 6. **Proof points** — 2–3 named achievements, each with a metric. Give me a short label and one sentence with a number. (e.g. 'Staff Planning App — Reduced manual planning from 1–10h to seconds, adopted by 15+ associations')"

Map answers to YAML keys:
- `narrative.headline`
- `narrative.exit_story` (short, third-person)
- `narrative.exit_story_long` (first-person, multi-sentence block — use YAML `|` block scalar)
- `narrative.exit_story_usage` — keys: `in_pdf`, `in_star`, `in_draft_answers` (or equivalent labels the user provides)
- `narrative.superpowers` — one item per differentiator
- `narrative.proof_points` — one entry per achievement: `name` (short label) + `hero_metric` (one sentence, metric-led)

**If the user is vague** (e.g., "I'm good at problem-solving"): ask one follow-up — "Can you give me a concrete example — a project, a result, something a reference could verify?" Never fabricate or paraphrase into something more impressive than what they said.

---

### Group 4 — Compensation

> "Compensation:
> - What's your target salary range? (include currency and gross/net — e.g. '45,000–55,000 EUR gross/year' or '1,600–2,100 EUR gross/month')
> - What's your walk-away minimum? (private — only used by the negotiation mode, never shown to companies)
> - Location and remote: where are you based, and what are your constraints? (e.g. Île-de-France only during studies, open after graduation, hybrid preferred)
> - How do you like to answer comp questions in interviews?"

Map answers to YAML keys:
- `compensation.target_range`, `compensation.currency`, `compensation.minimum`
- `compensation.location_flexibility`
- `compensation.market_context` — if the user is in the French market, pre-fill with a note about SYNTEC, Grande École norms, and convention collective relevance; ask the user to confirm or adjust
- `compensation.approach` — the user's preferred framing when asked about salary expectations

---

### Group 5 — Languages, Visa, and Awards

> "Two final basics:
> - What languages do you speak? For each: name, level, and certificate if you have one (e.g. 'English — C1 Advanced, Cambridge certificate')
> - Any prizes or ranked competitive results? (e.g. '1st prize at X competition, 80 teams') — skip if none"

Map answers to YAML keys:
- `languages` — one entry per language: `language` + `level` (include certificate in the level string: e.g. `"C1 Advanced (Cambridge certificate)"`)
- `awards_and_competitions` — for each prize: `name`, `category`, `date` (YYYY-MM), `result`, `context`. If none, set to an empty list `[]`.

Also set `location.visa_status` — infer from nationality/location if mentioned in Group 1. If not clear, ask:
> "One more: what's your visa status? (e.g. 'EU citizen — no sponsorship needed within EU' / 'Requires visa sponsorship')"

---

### Group 6 — Strategy

> "Now the strategy layer — this is how the system frames you differently for different role types. Keep it short — bullets are fine.
>
> 1. **Adaptive framing** — for each archetype we listed, what do you emphasize? Name 1–2 projects or proof points per role type, and say where in your profile they live (e.g. 'Projects section', 'Experience').
> 2. **Cross-cutting advantage** — in 2–3 sentences, what's the combination of qualities that makes you hard to replicate at your level?
> 3. **AI positioning** — how do you use AI tools in your work? One honest sentence.
> 4. **Negotiation scripts** — how do you answer 'What are your salary expectations?' and 'Can you do better on comp?' Draft two short scripts.
> 5. **Deal-breakers** — 2–3 things that would make you walk away from a role.
> 6. **Fit signals** — what energizes you at work? What drains you? (2–3 items each)"

Map answers to YAML keys:
- `strategy.adaptive_framing` — list of objects: `role_type`, `emphasize`, `proof_sources`
- `strategy.cross_cutting_advantage` — multi-line string (YAML `|` block scalar)
- `strategy.ai_positioning` — multi-line string
- `strategy.negotiation_scripts` — object with keys the user defines (e.g. `salary_expectations`, `counter_offer`, `grande_ecole_angle`)
- `strategy.deal_breakers` — list of strings
- `strategy.fit_signals` — object with `energizes` (list) and `drains` (list)

Keep this group lightweight. If the user says "skip" for any strategy sub-key, leave it as an empty placeholder — they can fill it later via `update-profile`.

Also populate `strategy.portfolio`:
- `portfolio.public_url` — ask if they have a public portfolio URL; set to `null` if not
- `portfolio.notes` — one sentence on portfolio status

---

### Group 7 — Location Policy

> "Two quick things about how location appears in applications:
>
> 1. When you fill in a 'Location' field on an application form, what do you write? Give me 2–3 bullets — one for on-site roles during studies, one for remote/hybrid, one for post-graduation.
> 2. How should the system score roles by location? For example:
>    - Remote or hybrid in your city → score normally
>    - Fully on-site outside your city during studies → penalize how much?
>    - Geographically incompatible (would require relocation mid-studies) → score how?"

Map answers to YAML keys:
- `location.in_forms` — list of 2–3 bullet strings
- `location.scoring_rules` — object with keys like `remote_or_hybrid`, `onsite_outside_city_during_studies`, `geographically_incompatible` — values are the scoring policy (e.g. `"Score normally"`, `"Score 3.0 (constraint, not dealbreaker)"`, `"Score 1.0"`)

---

### YAML Review

After all seven groups, assemble the complete YAML document and present it in full:

```
Here's your complete profile YAML — review everything before I save it:

---
[full YAML block — all keys, no CV sections yet]
---

Tell me what to change, or say "looks good" to save it.
```

Iterate until the user confirms. Only write to `config/profile.md` on explicit approval. At this stage, write the YAML content only — the CV sections (`summary`, `education`, `experience`, etc.) will be added in Phase 2.

Also set `cv.output_format: "latex"` in the written file.

---

## Phase 2 — CV YAML Sections

After the Phase 1 YAML is saved, automatically inventory `sources/` before doing anything else. Do not ask the user which method they prefer — check first, then report.

---

### Step 1 — Automatic Sources Inventory

List all files in `sources/` (excluding `README.md` if present).

**If the folder is empty or doesn't exist**, skip to Step 3 (Conversation Collection) and open with:
> "YAML saved. The `sources/` folder is empty — let's fill in your CV sections through a short interview. You can also drop documents into `sources/` at any point and I'll process them."

**If files are found**, read each file (applying the reading strategy below) and silently extract a classification and a one-line summary. Then report the full inventory before proposing anything:

```
YAML saved. I found [N] documents in sources/ — here's what I can build from them:

1. [filename]
   Type: [project report / internship doc / association file / competition / training / other]
   Would produce: [one sentence — e.g. "a projects entry for Air Liquide (hydrogen logistics simulation, Apr 2026)"]

2. [filename]
   Type: [...]
   Would produce: [one sentence]

[... for each file]

I'll process them one by one and show you a draft YAML entry for each before writing anything.
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
- **Draft a YAML entry** using the appropriate template from `modes/analyze-sources.md`
- **Propose the draft** for validation (see Review Loop below) — nothing is written until the user approves
- **YAML metadata update** — after each approved entry, offer to add a proof point or award to the YAML keys if the entry contains a strong verifiable outcome

---

### Step 3 — Conversation Collection (empty sections)

After processing all source files (or immediately if `sources/` was empty), check which CV YAML sections are still missing. For each missing section, ask structured questions in order.

Only ask about sections not yet filled. If all sections were covered by source documents, skip this step.

---

#### Section — Summary

> "Write a 3-sentence professional summary — this goes at the top of your CV:
> - Sentence 1: who you are + your specialization
> - Sentence 2: your strongest proof points (the 2 most impressive things you've done)
> - Sentence 3: what you're looking for"

Format as:
```yaml
summary: |
  [Three-sentence summary, no first-person pronouns, written as if introducing you to a hiring manager.]
```

---

#### Section — Education

> "Tell me about your education — most recent first.
> For each degree: exact degree title, institution name, start and end dates (e.g. 'Sept. 2025 -- Apr. 2027'), country, and 2–3 bullets (key specialization, academic ranking or distinction, any notable exchange or award).
> Give me one degree at a time."

Format each entry as:
```yaml
- title: "[Exact degree name — ≤ 45 chars ideally]"
  subtitle: "[Institution name]"
  date: "[mon. YYYY] -- [mon. YYYY]"
  country: "[Country]"
  bullets:
    - "[Bullet 1 — specialization or most differentiating feature]"
    - "[Bullet 2 — ranking, GPA, distinction, or exchange]"
    - "[Bullet 3 — optional]"
```

---

#### Section — Experience

> "Tell me about your professional experience — internships, jobs, placements — most recent first.
> For each: company, exact role/title, dates, country, and 2–3 bullets (deliverables, outcomes, metrics if you have them)."

**If the user has documents in `sources/` covering this experience:** redirect — "You have a document in sources/ that likely covers this. Want me to process that instead? It'll give us better bullets." Then switch to Step 2 for that file.

Format each entry as:
```yaml
- company: "[Company name]"
  post: "[Role in X domain — e.g. 'Operations Research Intern, Supply Chain']"
  country: "[Country only]"
  date: "[mon. YYYY] -- [mon. YYYY]"
  bullets:
    - "[Bullet 1 — strongest outcome or deliverable, verb-first]"
    - "[Bullet 2]"
    - "[Bullet 3 — max 3 total]"
```

Ask one probing question if deliverables are vague:
> "What's the one thing your manager would remember you for — the most concrete deliverable or outcome from this role?"

---

#### Section — Student Life and Associations

> "Any student clubs, associations, or elected roles?
> For each: exact role title, organization name, dates, and what you actually did (members managed, events organized, budget, concrete achievement)."

Format each entry as:
```yaml
- title: "[Role, Organization]"
  date: "[mon. YYYY] -- [mon. YYYY]"
  bullets:
    - "[Bullet 1 — scale or concrete achievement]"
    - "[Bullet 2]"
    - "[Bullet 3 — max 3 total]"
```

---

#### Section — Projects

> "Academic projects, consulting studies, or personal technical projects — most impactful first.
> For each: project type, client or context, dates, team size, and what you produced, recommended, or built."

Order: competitive prizes → real clients → strong metrics → technical sophistication → purely academic.

Format each entry as:
```yaml
- title: "[Title ≤ 45 chars]"
  subtitle: "[Relevant clarification — client, framework, or differentiator. No tools. No country.]"
  date: "[mon. YYYY]"
  bullets:
    - "[Bullet 1 — most impressive outcome or contribution, verb-first]"
    - "[Bullet 2]"
    - "[Bullet 3 — max 3 total]"
```

---

#### Section — Competitions

> "Any competitions or hackathons with an external ranking or prize?
> For each: result (prize/ranking), organizer, date, team size, and what you built or solved."

Only include externally verifiable results.

Format each entry as:
```yaml
- title: "[Position, Contest Name]"
  subtitle: "[Organizer — context: foundation, company, institution. Include team count.]"
  date: "[mon. YYYY]"
  bullets:
    - "[Bullet 1 — result leads always]"
    - "[Bullet 2 — max 2 total]"
```

---

#### Section — Skills

> "List your technical tools and skills — grouped by category. Omit soft skills.
> Example categories: Programming · Optimisation · Analytics & BI · Design · Project Management · AI & Automation
> For each category, give me the tools separated by commas."

Format as:
```yaml
skills:
  - category: "[Category name]"
    items: "[Tool1, Tool2, Tool3]"
  - category: "[Category name]"
    items: "[...]"
```

---

#### Section — Additional Training (optional)

> "Any courses, certificates, or extracurricular programs worth including?
> For each: program name, organizer, dates, and what made it notable (selective access, executive interaction, strong network). Skip if none."

Format each entry as:
```yaml
- title: "[Program Name] — [Organizer]"
  date: "[mon. YYYY]"
  bullets:
    - "[Bullet 1 — access/selection signal or most differentiating content]"
    - "[Bullet 2 — max 2 total]"
```

If no entries, omit the `additional_training` key entirely — do not write an empty array.

---

### Review Loop

After drafting each YAML entry (from sources or from conversation), present it in full before writing anything:

```
Draft entry for [section name]:

```yaml
[Full YAML object, formatted exactly as it would appear in profile.md]
```

Tell me what to change, or say "add it" to write it to profile.md.
```

On each revision round, show the complete entry again — never just the diff. Only write to `config/profile.md` on explicit approval ("add it", "looks good", "yes", "go ahead", or any clear affirmative).

**If the user drops new files into `sources/` mid-session** (during conversation collection): pause the current section, switch back to Step 2 for those files, then resume where you left off.

---

## Rules

- Never write to `config/profile.md` without explicit user approval at each step.
- Never fabricate metrics, invent outcomes, or inflate contributions. If no metric exists, omit the number — an entry without a figure is fine.
- If the user says "skip" for any section, move on without writing a placeholder — the section simply won't appear in the file until added later via `analyze-sources` or `update-profile`.
- All YAML entries written to the CV sections must follow the entry templates in `modes/analyze-sources.md` exactly: correct keys, verb-first bullets, title ≤ 45 chars for `projects` and `competitions`, max bullets respected.
- `additional_training` entries are ordered reverse chronologically within the array. If the user skips this section, do NOT write the key at all — not even as an empty array. Add it only when the first real entry exists.
- The `projects` array is ordered by decreasing impressiveness to the interviewer (not by date).
- `cv.output_format: "latex"` must always be present in the written file.

---

## Completion

Once all sections are filled (or the user has explicitly skipped sections):

> "Your profile is complete. Here's what was built:
>
> **Phase 1 — YAML:**
> - Identity: candidate.*, location.*
> - [N] target role archetypes
> - Narrative: headline · exit stories · [N] superpowers · [N] proof points
> - Compensation: target range · minimum · location flexibility · approach
> - [N] languages[· N awards]
> - Strategy: adaptive framing · cross-cutting advantage · negotiation scripts · deal-breakers · fit signals
> - Location policy: in_forms · scoring_rules
>
> **Phase 2 — CV sections:**
> - Summary: [filled / empty]
> - Education: [N entries / empty]
> - Experience: [N entries / empty]
> - Student life: [N entries / empty]
> - Projects: [N entries / empty]
> - Competitions: [N entries / empty]
> - Skills: [N categories / empty]
> - Additional training: [N entries / not included]
>
> Next step: run `/career-ops pdf` with a job description to generate a tailored `.tex` CV directly from this profile."

If there are source documents in `sources/` that were not yet processed, note them:
> "There are [N] unprocessed files in sources/. Run `/career-ops analyze-sources` to extract entries from them."
