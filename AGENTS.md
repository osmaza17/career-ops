# Career-Ops -- AI Job Search Pipeline



## Data Contract (CRITICAL)

There are two layers. Read `DATA_CONTRACT.md` for the full list.

**User Layer (NEVER auto-updated, personalization goes HERE):**
- `config/cv.md`, `config/profile.md`, `config/strategy.md`, `config/portals.yml`
- `data/*`, `reports/*`, `output/*`, `interview-prep/*`

**System Layer (auto-updatable, DON'T put user data here):**
- `modes/_shared.md`, `modes/offer-analysis.md`, all other modes
- `AGENTS.md`, `CLAUDE.md`, `*.mjs` scripts, `dashboard/*`, `templates/*`, `batch/*`

**THE RULE: When the user asks to customize anything (archetypes, narrative, negotiation scripts, proof points, location policy, comp targets), ALWAYS write to `config/strategy.md` or `config/profile.md`. NEVER edit `modes/_shared.md` for user-specific content.** This ensures system updates don't overwrite their customizations.

## What is career-ops

AI-powered, CLI-agnostic job search automation: pipeline tracking, offer evaluation, CV generation, portal scanning, batch processing. Runs on any AI coding CLI that follows the [open agent skill standard](https://agentskills.io) (Claude Code, Codex, OpenCode, Qwen, Copilot, Kimi).

### Main Files

| File                                 | Function                                                                                                                                            |
| ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `data/applications.md`               | Application tracker                                                                                                                                 |
| `data/pipeline.md`                   | Inbox of pending URLs                                                                                                                               |
| `data/scan-history.tsv`              | Scanner dedup history                                                                                                                               |
| `config/portals.yml`                 | Query and company config                                                                                                                            |
| `generate-pdf.mjs`                   | LaTeX CV validator + pdflatex compiler                                                                                                              |
| `config/profile.md`           | Compact proof points from portfolio (optional)                                                                                                      |
| `interview-prep/story-bank.md`       | Accumulated STAR+R stories across evaluations                                                                                                       |
| `interview-prep/{company}-{role}.md` | Company-specific interview intel reports                                                                                                            |
| `analyze-patterns.mjs`               | Pattern analysis script (JSON output)                                                                                                               |
| `followup-cadence.mjs`               | Follow-up cadence calculator (JSON output)                                                                                                          |
| `data/follow-ups.md`                 | Follow-up history tracker                                                                                                                           |
| `scan.mjs`                           | Zero-token portal scanner — hits Greenhouse/Ashby/Lever APIs directly, zero LLM cost                                                                |
| `check-liveness.mjs`                 | Job posting liveness checker                                                                                                                        |
| `liveness-core.mjs`                  | Shared liveness logic (expired signals win over generic Apply text)                                                                                 |
| `reports/`                           | Evaluation reports (format: `{###}-{company-slug}-{YYYY-MM-DD}.md`). Blocks A-F + G (Posting Legitimacy). Header includes `**Legitimacy:** {tier}`. |

### First Run — Onboarding (IMPORTANT)

**Before doing ANYTHING else, check if the system is set up.** Run these checks silently every time a session starts:

1. Does `config/cv.md` exist?
2. Does `config/profile.md` exist?
3. Does `config/strategy.md` exist (not just templates/strategy.template.md)?
4. Does `config/portals.yml` exist (not just templates/portals.example.yml)?

If `config/strategy.md` is missing, copy from `templates/strategy.template.md` silently. This is the user's customization file — it will never be overwritten by updates.

**If ANY of these is missing, enter onboarding mode.** Do NOT proceed with evaluations, scans, or any other mode until the basics are in place. Guide the user step by step:

#### Step 1: CV (required)
If `config/cv.md` is missing, ask:
> "I don't have your CV yet. You can either:
> 1. Paste your CV here and I'll convert it to markdown
> 2. Paste your LinkedIn URL and I'll extract the key info
> 3. Tell me about your experience and I'll draft a CV for you
>
> Which do you prefer?"

Create `config/cv.md` from whatever they provide. Make it clean markdown with standard sections (Summary, Experience, Projects, Education, Skills).

#### Step 2: Profile (required)
If `config/profile.md` is missing, copy `templates/profile.template.md` → `config/profile.md`, then run the guided interview below to fill in the YAML frontmatter. Work through the groups in order — ask each group as a single message, wait for the answer, then move to the next.

**Group 1 — Identity**
> "Let's set up your profile. First, the basics:
> - Full name (as it should appear on your CV)
> - Primary email
> - Phone number(s) — if you have separate numbers for different countries, tell me both
> - LinkedIn URL
> - University or institutional email (if you have one and want it on your CV)
> - Current city and country"

**Group 2 — Target roles**
> "What roles are you targeting? Give me:
> - Your top 2–3 target job titles (e.g. 'Operations Research Analyst', 'Strategy Consultant')
> - For each, is it a dream role, a strong fit, or a stretch? (I'll set the archetype fit accordingly)
> - What level are you targeting? (e.g. Junior, Mid, Senior)"

**Group 3 — Narrative**
> "Now the most important part — your story:
> - In one line, how would you describe yourself professionally? (e.g. 'Industrial engineer from X — specializing in Y')
> - What makes you stand out from other candidates at your level? What can you do that most can't?
> - What's your strongest achievement so far — the one you'd lead with in any interview?
> - Any other top achievements worth capturing? (I'll build your proof-points list)"

**Group 4 — Compensation**
> "Compensation:
> - What's your target salary range?
> - What's your walk-away minimum? (this stays private — only used by the negotiation mode)
> - Are you open to relocating or working remotely? Any restrictions?"

**Group 5 — Languages and awards**
> "Two final things:
> - What languages do you speak? For each: name, level, and certificate if you have one (e.g. 'English — C1 Advanced, Cambridge certificate')
> - Any prizes or ranked competitive results? (e.g. '1st prize at X competition, 80 teams') — skip if none"

After all five groups are answered, fill in every key in the YAML frontmatter of `config/profile.md` from the collected answers. Show the completed YAML to the user for review before saving — iterate if they want changes. Only write to the file once they confirm.

For archetypes and targeting narrative, store the user-specific mapping in `config/strategy.md` or `config/profile.md` rather than editing `modes/_shared.md`.

#### Step 3: Portals (recommended)
If `config/portals.yml` is missing:
> "I'll set up the job scanner with 45+ pre-configured companies. Want me to customize the search keywords for your target roles?"

Copy `templates/portals.example.yml` → `config/portals.yml`. If they gave target roles in Step 2, update `title_filter.positive` to match.

#### Step 4: Tracker
If `data/applications.md` doesn't exist, create it:
```markdown
# Applications Tracker

| #   | Date | Company | Role | Score | Status | PDF | Report | Notes |
| --- | ---- | ------- | ---- | ----- | ------ | --- | ------ | ----- |
```

#### Step 5: Get to know the user (important for quality)

After the basics are set up, proactively ask for more context. The more you know, the better your evaluations will be:

> "The basics are ready. But the system works much better when it knows you well. Can you tell me more about:
> - What makes you unique? What's your 'superpower' that other candidates don't have?
> - What kind of work excites you? What drains you?
> - Any deal-breakers? (e.g., no on-site, no startups under 20 people, no Java shops)
> - Your best professional achievement — the one you'd lead with in an interview
> - Any projects, articles, or case studies you've published?
>
> The more context you give me, the better I filter. Think of it as onboarding a recruiter — the first week I need to learn about you, then I become invaluable."

Store any insights the user shares in `config/profile.md` (narrative, proof points) or `config/strategy.md` (archetypes, negotiation scripts). Do not put user-specific content into `modes/_shared.md`.

**After every evaluation, learn.** If the user says "this score is too high, I wouldn't apply here" or "you missed that I have experience in X", update your understanding in `config/strategy.md` or `config/profile.md`. The system should get smarter with every interaction without putting personalization into system-layer files.

#### Step 6: Ready
Once all files exist, confirm:
> "You're all set! You can now:
> - Paste a job URL to evaluate it
> - Run `/career-ops scan` (or `/career-ops-scan` if using OpenCode) to search portals
> - Run `/career-ops` to see all commands
>
> Everything is customizable — just ask me to change anything.
>
> Tip: Having a personal portfolio dramatically improves your job search. If you don't have one yet, there is one, open source: github.com/santifer/cv-santiago — feel free to fork it and make it yours."

Then suggest automation:
> "Want me to scan for new offers automatically? I can set up a recurring scan every few days so you don't miss anything. Just say 'scan every 3 days' and I'll configure it."

If the user accepts, use the `/loop` or `/schedule` skill (if available) to set up a recurring `/career-ops scan` (or `/career-ops-scan` if using OpenCode). If those aren't available, suggest adding a cron job or remind them to run `/career-ops scan` (or `/career-ops-scan` if using OpenCode) periodically.

### Personalization

This system is designed to be customized by YOU (AI Agent). When the user asks you to change archetypes, translate modes, adjust scoring, add companies, or modify negotiation scripts -- do it directly. You read the same files you use, so you know exactly what to edit.

**Common customization requests:**
- "Change the archetypes to [backend/frontend/data/devops] roles" → edit `config/strategy.md` or `config/profile.md`
- "Translate the modes to English" → edit all files in `modes/`
- "Add these companies to my portals" → edit `config/portals.yml`
- "Update my profile" → edit `config/profile.md`
- "Adjust the scoring weights" → edit `config/strategy.md` for user-specific weighting, or edit `modes/_shared.md` and `batch/batch-prompt.md` only when changing the shared system defaults for everyone

### Language Modes

Default modes are in `modes/` (English). French language modes are available in `modes/fr/`.

- **French (Francophone market):** `modes/fr/` — native French translations with France/Belgium/Switzerland/Luxembourg-specific vocabulary (CDI/CDD, convention collective SYNTEC, RTT, mutuelle, prévoyance, 13e mois, intéressement/participation, titres-restaurant, CSE, portage salarial, etc.). Includes `_shared.md`, `offre.md` (evaluation), `postuler.md` (apply), `pipeline.md`.

**When to use French modes:** If the user is targeting French-language job postings, lives in France/Belgium/Switzerland/Luxembourg/Quebec, or asks for French output. Either:
1. User says "use French modes" → read from `modes/fr/` instead of `modes/`
2. User sets `language.modes_dir: modes/fr` in `config/profile.md` → always use French modes
3. You detect a French JD → suggest switching to French modes

**When NOT to:** If the user applies to English-language roles, even at French companies, use the default English modes.

### Skill Modes

| If the user...                                              | Mode                                              |
| ----------------------------------------------------------- | ------------------------------------------------- |
| Pastes JD or URL                                            | auto-pipeline (evaluate + report + PDF + tracker) |
| Asks to evaluate offer                                      | `offer-analysis`                                  |
| Asks to compare offers                                      | `offers-comparison`                               |
| Wants LinkedIn outreach                                     | `contact`                                         |
| Asks for company research                                   | `deep`                                            |
| Preps for interview at specific company                     | `interview-prep`                                  |
| Wants to generate CV/PDF                                    | `pdf`                                             |
| Evaluates a course/cert                                     | `training`                                        |
| Evaluates portfolio project                                 | `project`                                         |
| Asks about application status                               | `tracker`                                         |
| Fills out application form                                  | `apply`                                           |
| Searches for new offers                                     | `scan`                                            |
| Processes pending URLs                                      | `pipeline`                                        |
| Batch processes offers                                      | `batch`                                           |
| Asks about rejection patterns or wants to improve targeting | `patterns`                                        |
| Asks about follow-ups or application cadence                | `followup`                                        |
| Wants to rebuild cv.md from profile.md                    | `ingest`                                          |
| Has raw academic documents to digest (project reports, internship descriptions, association activity files) | `analyze-sources` |

### CV Source of Truth

- `config/cv.md` is the canonical CV
- `config/profile.md` has detailed proof points (optional)
- `sources/` holds raw academic documents — **only `modes/analyze-sources.md` reads from this folder**
- **NEVER hardcode metrics** -- read them from these files at evaluation time

### Full pipeline (raw documents → CV)

```
sources/          →  analyze-sources  →  config/profile.md
config/profile.md  →  ingest           →  config/cv.md
config/cv.md         →  pdf / latex      →  output/*.pdf
```

**Access rule:** `sources/` is exclusively accessed by `analyze-sources`. No other mode, agent, or script reads files from that folder.

---

## Ethical Use -- CRITICAL

**This system is designed for quality, not quantity.** The goal is to help the user find and apply to roles where there is a genuine match -- not to spam companies with mass applications.

- **NEVER submit an application without the user reviewing it first.** Fill forms, draft answers, generate PDFs -- but always STOP before clicking Submit/Send/Apply. The user makes the final call.
- **Strongly discourage low-fit applications.** If a score is below 4.0/5, explicitly recommend against applying. The user's time and the recruiter's time are both valuable. Only proceed if the user has a specific reason to override the score.
- **Quality over speed.** A well-targeted application to 5 companies beats a generic blast to 50. Guide the user toward fewer, better applications.
- **Respect recruiters' time.** Every application a human reads costs someone's attention. Only send what's worth reading.

---

## Offer Verification -- MANDATORY

**NEVER trust WebSearch/WebFetch to verify if an offer is still active.** ALWAYS use Playwright:
1. `browser_navigate` to the URL
2. `browser_snapshot` to read content
3. Only footer/navbar without JD = closed. Title + description + Apply = active.

**Exception for batch workers (headless mode):** Playwright is not available in headless pipe mode. Use WebFetch as fallback and mark the report header with `**Verification:** unconfirmed (batch mode)`. The user can verify manually later.

---

## Headless / Batch Mode

When spawning headless workers for batch processing, use the appropriate command for your CLI:

| CLI         | Command                 |
| ----------- | ----------------------- |
| Claude Code | `claude -p "prompt"`    |
| Copilot CLI | `copilot -p "prompt"`   |
| Codex       | `codex exec "prompt"`   |
| OpenCode    | `opencode run "prompt"` |
| Qwen        | `qwen -p "prompt"`      |

## Stack and Conventions

- Node.js (mjs modules), Playwright (scraping + offer verification), YAML (config), LaTeX (CV template), Markdown (data), Canva MCP (optional visual CV)
- Scripts in `.mjs`, configuration in YAML
- Output in `output/` (gitignored), Reports in `reports/`
- JDs in `jds/` (referenced as `local:jds/{file}` in pipeline.md)
- Batch in `batch/` (gitignored except scripts and prompt)
- Report numbering: sequential 3-digit zero-padded, max existing + 1
- **RULE: After each batch of evaluations, run `node merge-tracker.mjs`** to merge tracker additions and avoid duplications.
- **RULE: NEVER create new entries in applications.md if company+role already exists.** Update the existing entry.

### TSV Format for Tracker Additions

Write one TSV file per evaluation to `batch/tracker-additions/{num}-{company-slug}.tsv`. Single line, 9 tab-separated columns:

```
{num}\t{date}\t{company}\t{role}\t{status}\t{score}/5\t{pdf_emoji}\t[{num}](reports/{num}-{slug}-{date}.md)\t{note}
```

**Column order (IMPORTANT -- status BEFORE score):**
1. `num` -- sequential number (integer)
2. `date` -- YYYY-MM-DD
3. `company` -- short company name
4. `role` -- job title
5. `status` -- canonical status (e.g., `Evaluated`)
6. `score` -- format `X.X/5` (e.g., `4.2/5`)
7. `pdf` -- `✅` or `❌`
8. `report` -- markdown link `[num](reports/...)`
9. `notes` -- one-line summary

**Note:** In applications.md, score comes BEFORE status. The merge script handles this column swap automatically.

### Pipeline Integrity

1. **NEVER edit applications.md to ADD new entries** -- Write TSV in `batch/tracker-additions/` and `merge-tracker.mjs` handles the merge.
2. **YES you can edit applications.md to UPDATE status/notes of existing entries.**
3. All reports MUST include `**URL:**` in the header (between Score and PDF). Include `**Legitimacy:** {tier}` (see Block G in `modes/offer-analysis.md`).
4. All statuses MUST be canonical (see `templates/states.yml`).
5. Health check: `node verify-pipeline.mjs`
6. Normalize statuses: `node normalize-statuses.mjs`
7. Dedup: `node dedup-tracker.mjs`

### Canonical States (applications.md)

**Source of truth:** `templates/states.yml`

| State       | When to use                            |
| ----------- | -------------------------------------- |
| `Evaluated` | Report completed, pending decision     |
| `Applied`   | Application sent                       |
| `Responded` | Company responded                      |
| `Interview` | In interview process                   |
| `Offer`     | Offer received                         |
| `Rejected`  | Rejected by company                    |
| `Discarded` | Discarded by candidate or offer closed |
| `SKIP`      | Doesn't fit, don't apply               |

**RULES:**
- No markdown bold (`**`) in status field
- No dates in status field (use the date column)
- No extra text (use the notes column)
