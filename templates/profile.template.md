---
# profile.md — [Your Full Name]
# ─────────────────────────────────────────────────────────────────
# This is the single source of truth for career-ops.
# Copy to config/profile.md and fill in your details.
#
# Structure:
#   - YAML frontmatter (this block) — structured data read by all modes
#   - Markdown body (below) — your full trajectory corpus
#
# The agent will guide you through filling this out on first run.
# ─────────────────────────────────────────────────────────────────

candidate:
  full_name: ""               # Your full name as it appears on your CV
  email: ""                   # Primary contact email
  phone_es: ""                # Spain / WhatsApp — remove line if not applicable
  phone_fr: ""                # France / calls — remove line if not applicable
  # phone: ""                 # Use this instead if you only have one number
  location: ""                # e.g. "Paris, France (Paris area)"
  linkedin: ""                # Full URL: https://www.linkedin.com/in/yourhandle/
  institutional_email: ""     # University or company email — remove if not applicable

target_roles:
  # North Star roles — what you are optimizing for
  primary:
    - ""                      # e.g. "Operations Research Analyst"
    - ""                      # e.g. "Strategy & Operations Consultant"
  # Archetypes feed the scoring and framing logic across all modes
  archetypes:
    - name: ""                # e.g. "Management Consultant (Operations / Strategy)"
      level: ""               # e.g. "Junior", "Mid", "Senior", "Staff"
      fit: "primary"          # primary = dream role | secondary = good fit | adjacent = stretch
    - name: ""
      level: ""
      fit: "secondary"
    # Add or remove archetype entries as needed

narrative:
  headline: ""                # One line — professional identity summary
                              # e.g. "Industrial engineer from CentraleSupélec — OR and data-driven operations"
  exit_story: ""              # 2–4 sentences: what makes you unique, your strongest achievements,
                              # what you are looking for. Used in cover letters and outreach.
  superpowers:
    - ""                      # Your top differentiator — the thing others in your field rarely combine
    - ""                      # A second distinctive strength
    - ""                      # A third (add or remove lines as needed)
  proof_points:               # 2–4 top achievements — short and metric-led.
    - name: ""                # Short project or achievement name
      hero_metric: ""         # One sentence: the strongest verifiable outcome
                              # e.g. "+68% palletizing productivity; VAN-positive in 18 months"
    # Populated by analyze-sources as you build the markdown body below.
    # Add entries manually or let the mode propose them after each analysis session.

compensation:
  target_range: ""            # e.g. "40K–50K EUR gross" or "TBD — research current market rates"
  currency: ""                # e.g. "EUR", "USD", "GBP"
  minimum: ""                 # Your walk-away number — private, used only by negotiation mode
  location_flexibility: ""    # e.g. "Open to France and Spain; partial remote OK"

awards_and_competitions:      # Prizes and ranked competitive results only.
                              # Leave the block empty (just the key) if none yet.
  - name: ""                  # Full prize name and organizer
                              # e.g. "1st Prize — Bankinter Akademia Hackathon"
    category: ""              # hackathon | research_competition | case_competition | other
    date: ""                  # YYYY-MM
    result: ""                # e.g. "1st prize among 80 teams"
    context: ""               # One sentence: format, scale, your role in the team

location:
  country: ""                 # Country of current residence
  city: ""                    # City
  timezone: ""                # e.g. "CET", "WET", "EST", "PST"
  visa_status: ""             # e.g. "EU citizen (Spain) — no sponsorship needed within EU"
                              # or "Requires visa sponsorship"

languages:
  - language: ""              # e.g. "English"
    level: ""                 # e.g. "Native" | "C1 Advanced (Cambridge certificate)" | "B2 (DELF)"
  - language: ""
    level: ""
  # Add or remove language entries as needed

cv:
  output_format: "latex"      # "latex" (default, produces PDF) or "html"
  # canva_resume_design_id: "" # Optional — fill if using Canva visual CV
                               # Find in your Canva URL: canva.com/design/DAxxxxxxx/...
---

# DATA-INGEST: [Your Full Name] — Professional and Academic Trajectory Corpus

> **LLM INSTRUCTION — READ BEFORE PROCESSING THIS FILE**
>
> This file contains three types of content:
>
> 1. **YAML frontmatter** (between the `---` delimiters at the top) — structured data: identity, contact, target roles, narrative, languages, CV config. Read as data, not narrative text. **Sections 0 and 2 in the markdown body redirect here — data is not duplicated.**
>
> 2. **Trajectory content** — factual information about education, experience, projects, and skills. This is the material used to generate CVs, cover letters, and evaluations.
>
> 3. **Meta-instructions** — directives for the LLM on how to use, prioritize, or frame the trajectory content. Marked with the tag `[META-INSTRUCCIÓN]`. **Never reproduce them in documents sent to third parties. They are instructions for the agent, not candidate content.**

> Note: section names can be written in any language — the agent navigates by section number, not by name.

___

## SECTION 0 — IDENTITY AND CONTACT

*(Data lives in the YAML frontmatter: `candidate.*` and `location.*` keys. Not duplicated here.)*

---

## SECTION 1 — EDUCATION

*(Add your degrees here, most recent first. For each entry: institution, exact degree name, dates, GPA or ranking if strong, any notable distinction. Add a `[META-INSTRUCCIÓN]` block if there is framing context the agent should always apply — e.g. a pioneering achievement or a pairing rule.)*

---

## SECTION 2 — LANGUAGES

*(Data lives in the YAML frontmatter: `languages` key. Not duplicated here.)*

---

## SECTION 3 — TECHNICAL SKILLS

*(List your tools and skills grouped by category. Omit soft skills. Example categories: Programming & Data · Optimisation & Simulation · Analytics & BI · Design & Engineering · Documentation · Project Management · AI & Automation)*

---

## SECTION 4 — PROFESSIONAL EXPERIENCE

*(Internships, jobs, and professional placements. Most recent first. Added by `analyze-sources` or manually. Use the entry template from `modes/analyze-sources.md`.)*

---

## SECTION 5 — STUDENT LIFE AND ASSOCIATIONS

*(Student clubs, associations, elected or appointed roles. Added by `analyze-sources` or manually.)*

---

## SECTION 6 — PROJECTS AND INNOVATION

*(Academic projects, consulting studies, personal technical projects, innovation work. Added by `analyze-sources` or manually.)*

---

## SECTION 7 — COMPETITIONS AND HACKATHONS

*(Competitions with an external prize or verifiable ranking only. Added by `analyze-sources` or manually.)*

---

## SECTION 8 — ADDITIONAL TRAINING

*(Courses, certificates, extracurricular programs. Reverse chronological — most recent first. Added by `analyze-sources` or manually.)*

---

## SECTION 9 — MISCELLANEOUS (archive)

> **[META-INSTRUCCIÓN — SECTION]** Low-priority content documented for completeness and as a narrative reserve for interviews. Excluded from cv.md by default. Each entry may carry its own `[META-INSTRUCCIÓN]` specifying when and how to use it.

*(Low-priority items: early experiences, extracurricular projects, archived material with limited CV value. Document here so nothing is lost; `ingest` will skip this section by default.)*
