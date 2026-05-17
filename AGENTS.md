# Career-Ops -- AI Job Search Pipeline



## Data Contract (CRITICAL)

**`config/profile.md` is the only source of truth about the user.** Every piece of personal data — identity, targets, compensation, narrative, strategy, CV sections — lives there and only there. All other files are system files.

There are two layers:

**User Layer (NEVER auto-updated — all personal data lives here):**

Files marked *(created on first use)* do not exist until the relevant mode runs for the first time — this is expected; treat a missing file as an empty state, not an error.

| File/Path | What it holds | Created by |
|---|---|---|
| `config/profile.md` | Identity, target roles, compensation, narrative, strategy, and all formatted CV sections. The single source of truth. | `onboard` (required before anything else) |
| `config/portals.yml` | Customized company and portal list | `scan` setup (recommended) |
| `data/applications.md` | Application tracker | `onboard` Step C (created with empty header) |
| `data/pipeline.md` | URL inbox | `scan` *(created on first use)* |
| `data/scan-history.tsv` | Scanner dedup history | `scan` *(created on first use)* |
| `data/follow-ups.md` | Follow-up history | `followup` *(created on first use)* |
| `data/linkedin.md` | LinkedIn profile context (saved by `linkedin-optimizer`, read each session) | `linkedin-optimizer` *(created on first use)* |
| `sources/*` | Raw academic documents | User-provided (read by `onboard` for initial setup and `analyze-sources` for ongoing additions) |
| `output/reports/*` | Evaluation reports | `offer-analysis` *(created on first use)* |
| `output/interview-prep/*` | Interview intel reports | `interview-prep` *(created on first use)* |
| `output/CVs/*` | Generated .tex and PDF files | `pdf` *(created on first use)* |
| `jds/*` | Saved job descriptions | `scan` / user *(created on first use)* |

**System Layer (safe to auto-update — no user data here):**
- All files in `modes/`, `batch/`, `dashboard/`, `docs/`, `.claude/skills/`
- `AGENTS.md`, `CLAUDE.md`, `*.mjs` scripts, `states.yml`

**THE RULE: When the user asks to customize anything (archetypes, narrative, negotiation scripts, proof points, location policy, comp targets), ALWAYS write to `config/profile.md` (under the `strategy:` key or the relevant YAML key). NEVER edit `modes/_shared.md` or any system file for user-specific content.** This ensures system updates never overwrite their customizations.

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
| `config/profile.md`           | Single source of truth — YAML frontmatter (identity, roles, narrative, compensation, languages) + formatted CV sections (education, experience, projects, skills, languages). Created by `onboard`, populated by `analyze-sources`, read by every mode and by `pdf` to generate `.tex` CVs. |
| `output/interview-prep/{company}-{role}.md` | Company-specific interview intel reports                                                                                                  |
| `analyze-patterns.mjs`               | Pattern analysis script (JSON output)                                                                                                               |
| `followup-cadence.mjs`               | Follow-up cadence calculator (JSON output)                                                                                                          |
| `data/follow-ups.md`                 | Follow-up history tracker                                                                                                                           |
| `data/linkedin.md`                   | LinkedIn profile context — saved by `linkedin-optimizer`, read each session to skip re-interview                                                    |
| `scan.mjs`                           | Zero-token portal scanner — hits Greenhouse/Ashby/Lever APIs directly, zero LLM cost                                                                |
| `check-liveness.mjs`                 | Job posting liveness checker                                                                                                                        |
| `liveness-core.mjs`                  | Shared liveness logic (expired signals win over generic Apply text)                                                                                 |
| `output/reports/`                    | Evaluation reports (format: `{###}-{company-slug}-{YYYY-MM-DD}.md`). Blocks A-F + G (Posting Legitimacy). Header includes `**Legitimacy:** {tier}`. |

### First Run — Onboarding (IMPORTANT)

**Before doing ANYTHING else, check if the system is set up.** Run these checks silently every time a session starts:

1. Does `config/profile.md` exist?
2. Does `config/portals.yml` exist?

**If ANY of these is missing, enter onboarding mode.** Do NOT proceed with evaluations, scans, or any other mode until the basics are in place. Follow this order exactly — each step depends on the previous one.

#### Step 1: Profile (required — source of truth)

If `config/profile.md` is missing, run `modes/onboard.md`. This mode handles everything needed to build a complete profile: the YAML frontmatter (identity, target roles, narrative, compensation, languages) and the formatted CV sections (education, experience, projects, associations, competitions, training). It also handles processing raw documents from `sources/` if the user has them.

`config/profile.md` is both the data source AND the formatted CV — there is no intermediate file. `modes/pdf.md` reads the CV sections directly from the body of `profile.md`.

**Do not proceed to Step 2 until `config/profile.md` is complete.**

#### Step 2: Portals (recommended)

If `config/portals.yml` is missing:
> "I'll set up the job scanner with 45+ pre-configured companies. Want me to customize the search keywords for your target roles?"

The portal configuration is built by the scanner setup in `modes/scan.md`. If target roles are already in `config/profile.md`, the setup will auto-populate `title_filter.positive` to match.

#### Step 3: Tracker

If `data/applications.md` doesn't exist, create it:
```markdown
# Applications Tracker

| #   | Date | Company | Role | Score | Status | PDF | Report | Notes |
| --- | ---- | ------- | ---- | ----- | ------ | --- | ------ | ----- |
```

#### Step 4: Ready

Once all files exist, confirm:
> "You're all set! You can now:
> - Paste a job URL to evaluate it
> - Run `/career-ops scan` (or `/career-ops-scan` if using OpenCode) to search portals
> - Run `/career-ops` to see all commands
>
> Everything is customizable — just ask me to change anything.
>
> Tip: Having a personal portfolio dramatically improves your job search. If you don't have one yet, there is one, open source: github.com/osmaza17/cv-santiago — feel free to fork it and make it yours."

Then suggest automation:
> "Want me to scan for new offers automatically? I can set up a recurring scan every few days so you don't miss anything. Just say 'scan every 3 days' and I'll configure it."

If the user accepts, use the `/loop` or `/schedule` skill (if available) to set up a recurring `/career-ops scan` (or `/career-ops-scan` if using OpenCode). If those aren't available, suggest adding a cron job or remind them to run the scan periodically.

**After every evaluation, learn.** If the user says "this score is too high, I wouldn't apply here" or "you missed that I have experience in X", update your understanding in `config/profile.md` (under the `strategy:` key or the relevant section). The system should get smarter with every interaction without putting personalization into system-layer files.

### Personalization

This system is designed to be customized by YOU (AI Agent). When the user asks you to change archetypes, translate modes, adjust scoring, add companies, or modify negotiation scripts -- do it directly. You read the same files you use, so you know exactly what to edit.

**Common customization requests:**
- "Change the archetypes to [backend/frontend/data/devops] roles" → edit `config/profile.md` (under `target_roles.archetypes` and `strategy.adaptive_framing`)
- "Translate the modes to English" → edit all files in `modes/`
- "Add these companies to my portals" → edit `config/portals.yml`
- "Update my profile" → edit `config/profile.md`
- "Adjust the scoring weights" → edit `config/profile.md` under the `strategy:` key for user-specific weighting, or edit `modes/_shared.md` and `batch/batch-prompt.md` only when changing the shared system defaults for everyone

### Language Modes

All modes are in `modes/` (English). If the user is targeting French-language job postings or asks for French output, the agent should produce French-language content using the same modes — adapting vocabulary to the French market (CDI/CDD, SYNTEC, RTT, mutuelle, 13e mois, intéressement/participation, titres-restaurant, etc.) without switching to a separate folder.

### Skill Modes

| If the user...                                              | Mode                                              |
| ----------------------------------------------------------- | ------------------------------------------------- |
| Pastes JD or URL                                            | `auto-pipeline` (evaluate + report + PDF + tracker) |
| Asks to evaluate offer                                      | `offer-analysis`                                  |
| Asks to compare offers                                      | `offers-comparison`                               |
| Wants LinkedIn outreach (messages to recruiters/contacts)   | `contact`                                         |
| Wants to optimize their LinkedIn profile                    | `linkedin-optimizer`                              |
| Preps for interview at specific company                     | `interview-prep`                                  |
| Wants to generate CV/PDF                                    | `pdf`                                             |
| Asks about application status                               | `tracker`                                         |
| Fills out application form                                  | `apply`                                           |
| Searches for new offers                                     | `scan`                                            |
| Processes pending URLs                                      | `pipeline`                                        |
| Batch processes offers                                      | `batch`                                           |
| Asks about rejection patterns or wants to improve targeting | `patterns`                                        |
| Asks about follow-ups or application cadence                | `followup`                                        |
| Has raw academic documents to digest (project reports, internship descriptions, association activity files) or new files in sources/ to process | `analyze-sources` |
| Needs to create or complete config/profile.md              | `onboard`                                         |
| Names 2+ companies to research, compare, rank, or build a target list from | `intel-sweep`  |

### CV Source of Truth

- `config/profile.md` is the single source of truth — YAML frontmatter (identity, targets, compensation, languages) + formatted CV sections (Education, Experience, Student Life, Projects, Competitions, Skills, Languages). There is no intermediate CV file.
- `modes/pdf.md` reads the CV sections from `config/profile.md` directly.
- `sources/` holds raw academic documents — read by `modes/onboard.md` (during initial setup) and `modes/analyze-sources.md` (ongoing additions). No other mode accesses this folder.
- **NEVER hardcode metrics** — read them from `config/profile.md` at evaluation time

### Full pipeline (raw documents → CV)

```
(first run)        →  onboard          →  config/profile.md  (YAML + formatted CV sections)
sources/           →  analyze-sources   →  config/profile.md  (one or more files, interactive)
config/profile.md  →  pdf              →  output/*.pdf
```

**Access rule:** `sources/` is accessed only by `modes/onboard.md` (initial setup) and `modes/analyze-sources.md` (ongoing additions). No other mode, agent, or script reads files from that folder.

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

> **Note:** `batch/batch-runner.sh` is Claude Code-specific — it uses `--dangerously-skip-permissions` and `--append-system-prompt-file` flags not available in other CLIs. For other CLIs, spawn workers manually using the base command above with the contents of `batch/batch-prompt.md` as the prompt.

## Stack and Conventions

- Node.js (mjs modules), Playwright (scraping + offer verification), YAML (config), LaTeX (CV template), Markdown (data), Canva MCP (optional visual CV)
- Scripts in `.mjs`, configuration in YAML
- CVs in `output/CVs/` (gitignored), Reports in `output/reports/`, Interview prep in `output/interview-prep/`
- JDs in `jds/` (referenced as `local:jds/{file}` in pipeline.md)
- Batch in `batch/` (gitignored except scripts and prompt)
- Report numbering: sequential 3-digit zero-padded, max existing + 1
- **RULE: After each batch of evaluations, run `node merge-tracker.mjs`** to merge tracker additions and avoid duplications.
- **RULE: NEVER create new entries in applications.md if company+role already exists.** Update the existing entry.

### TSV Format for Tracker Additions

Write one TSV file per evaluation to `batch/tracker-additions/{num}-{company-slug}.tsv`. Single line, 9 tab-separated columns:

```
{num}\t{date}\t{company}\t{role}\t{status}\t{score}/5\t{pdf_emoji}\t[{num}](output/reports/{num}-{slug}-{date}.md)\t{note}
```

**Column order (IMPORTANT -- status BEFORE score):**
1. `num` -- sequential number (integer)
2. `date` -- YYYY-MM-DD
3. `company` -- short company name
4. `role` -- job title
5. `status` -- canonical status (e.g., `Evaluated`)
6. `score` -- format `X.X/5` (e.g., `4.2/5`)
7. `pdf` -- `✅` or `❌`
8. `report` -- markdown link `[num](output/reports/...)`
9. `notes` -- one-line summary

**Note:** In applications.md, score comes BEFORE status. The merge script handles this column swap automatically.

### Pipeline Integrity

1. **NEVER edit applications.md to ADD new entries** -- Write TSV in `batch/tracker-additions/` and `merge-tracker.mjs` handles the merge.
2. **YES you can edit applications.md to UPDATE status/notes of existing entries.**
3. All reports MUST include `**URL:**` in the header (between Score and PDF). Include `**Legitimacy:** {tier}` (see Block G in `modes/offer-analysis.md`).
4. All statuses MUST be canonical (see `states.yml`).
5. Health check: `node verify-pipeline.mjs`
6. Normalize statuses: `node normalize-statuses.mjs`
7. Dedup: `node dedup-tracker.mjs`

### Canonical States (applications.md)

**Source of truth:** `states.yml`

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
