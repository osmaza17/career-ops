# System Context -- career-ops

<purpose>
Shared reference file loaded by every career-ops mode. Contains scoring rubrics, global rules, archetype definitions, writing style calibration, and tool configuration. Read this file BEFORE reading config/profile.md. User customizations in config/profile.md (under the `strategy:` key) override defaults defined here.
</purpose>

<!-- ============================================================
     THIS FILE IS AUTO-UPDATABLE. Don't put personal data here.
     
     Your customizations go in config/profile.md under the strategy: key (never auto-updated).
     This file contains system rules, scoring logic, and tool config
     that improve with each career-ops release.
     ============================================================ -->

<reference id="sources-of-truth">

## Sources of Truth

| File | Path | When |
|------|------|------|
| profile.md | `config/profile.md` | ALWAYS — YAML frontmatter (identity, targets, languages) + markdown body (formatted CV sections: education, experience, projects, skills, languages) |

**RULE: NEVER hardcode metrics from proof points.** Read them from config/profile.md at evaluation time.
**RULE: Read config/profile.md AFTER this file. User customizations in config/profile.md (under the `strategy:` key) override defaults here.**

</reference>

---

<reference id="scoring-system">

## Scoring System

<scoring>

The evaluation uses 6 blocks (A-F) with a global score of 1-5:

| Dimension | What it measures |
|-----------|-----------------|
| CV Match | Skills, experience, proof points alignment |
| North Star alignment | How well the role fits the user's target archetypes (from `config/profile.md`, key `target_roles.archetypes`) |
| Comp | Salary vs market (5=top quartile, 1=well below) |
| Cultural signals | Company culture, growth, stability, remote policy |
| Red flags | Blockers, warnings (negative adjustments) |
| **Global** | Weighted average of above |

**Score interpretation:**
- 4.5+ → Strong match, recommend applying immediately
- 4.0-4.4 → Good match, worth applying
- 3.5-3.9 → Decent but not ideal, apply only if specific reason
- Below 3.5 → Recommend against applying (see Ethical Use in AGENTS.md)

</scoring>

</reference>

<reference id="block-g-legitimacy">

## Posting Legitimacy (Block G)

Block G assesses whether a posting is likely a real, active opening. It does NOT affect the 1-5 global score -- it is a separate qualitative assessment.

**Three tiers:**
- **High Confidence** -- Real, active opening (most signals positive)
- **Proceed with Caution** -- Mixed signals, worth noting (some concerns)
- **Suspicious** -- Multiple ghost indicators, user should investigate first

**Key signals (weighted by reliability):**

| Signal | Source | Reliability | Notes |
|--------|--------|-------------|-------|
| Posting age | Page snapshot | High | Under 30d=good, 30-60d=mixed, 60d+=concerning (adjusted for role type) |
| Apply button active | Page snapshot | High | Direct observable fact |
| Tech specificity in JD | JD text | Medium | Generic JDs correlate with ghost postings but also with poor writing |
| Requirements realism | JD text | Medium | Contradictions are a strong signal, vagueness is weaker |
| Recent layoff news | WebSearch | Medium | Must consider department, timing, and company size |
| Reposting pattern | scan-history.tsv | Medium | Same role reposted 2+ times in 90 days is concerning |
| Salary transparency | JD text | Low | Jurisdiction-dependent, many legitimate reasons to omit |
| Role-company fit | Qualitative | Low | Subjective, use only as supporting signal |

**Ethical framing (MANDATORY):**
- This helps users prioritize time on real opportunities
- NEVER present findings as accusations of dishonesty
- Present signals and let the user decide
- Always note legitimate explanations for concerning signals

</reference>

<reference id="archetype-detection">

## Archetype Detection

Classify every offer into one of these types (or hybrid of 2). Read `config/profile.md` (key `target_roles.archetypes` and `strategy.adaptive_framing`) for the user's specific archetypes — these defaults apply only when no override is present.

| Archetype | Key signals in JD |
|-----------|-------------------|
| Management Consultant | "strategy", "case study", "client", "recommendation", "framework", "problem-solving", "structured analysis", "deliverable" |
| Operations Research / Optimisation | "optimisation", "OR", "MILP", "simulation", "modelling", "solver", "heuristic", "quantitative methods", "algorithm" |
| Supply Chain / Logistics | "supply chain", "logistics", "inventory", "demand planning", "S&OP", "procurement", "distribution", "warehouse", "network design" |
| Process / Industrial Engineer | "Lean", "Six Sigma", "continuous improvement", "process mapping", "Kaizen", "capacity planning", "manufacturing", "plant operations" |
| Data / Quantitative Analyst | "analytics", "data", "Power BI", "SQL", "Python", "KPIs", "reporting", "forecasting", "dashboards", "business intelligence" |
| Project / Programme Manager | "project management", "PMO", "milestones", "stakeholders", "budget", "programme", "risk management", "planning", "coordination" |

After detecting archetype, read `config/profile.md` (key `strategy.adaptive_framing`) for the user's specific framing and proof points for that archetype.

</reference>

<rules>

## Global Rules

### NEVER

1. Invent experience or metrics
2. Modify config/profile.md (except via designated modes: onboard, analyze-sources)
3. Submit applications on behalf of the candidate
4. Share phone number in generated messages
5. Recommend comp below market rate
6. Generate a PDF without reading the JD first
7. Use corporate-speak
8. Ignore the tracker (every evaluated offer gets registered)

### ALWAYS

0. **Cover letter:** If the form allows it, ALWAYS include one. Same visual design as CV. JD quotes mapped to proof points. 1 page max.
1. Read config/profile.md (YAML frontmatter + CV body sections) before evaluating
1b. **First evaluation of each session:** Run `node cv-sync-check.mjs`. If warnings, notify user.
2. Detect the role archetype and adapt framing per `config/profile.md` (key `strategy.adaptive_framing`)
3. Cite exact lines from CV when matching
4. Use WebSearch for comp and company data
5. Register in tracker after evaluating
6. Generate content in the language of the JD (EN default)
7. Be direct and actionable -- no fluff
8. Concise professional English. Short sentences, action verbs, no passive voice.
8b. Key proof points and metrics in PDF Professional Summary — recruiters may only read this section.
9. **Tracker additions as TSV** -- NEVER edit applications.md directly. Write TSV in `batch/tracker-additions/`. For single evaluations: immediately run `node merge-tracker.mjs` after writing the TSV. For batch sessions: run once at the end (see `modes/batch.md`).
9b. **Writing style** -- Before generating any candidate-facing text (cover letters, outreach messages, form answers, follow-up emails): read `config/profile.md` under `strategy.writing_style`. If that key is absent, apply the ATS Writing Rules defined below in this file (`<reference id="ats-writing-rules">`) and ask the user if they want to save a style preference to `config/profile.md`.
10. **Include `**URL:**` in every report header.**

### Tools

<section name="tool-config">

| Tool | Use |
|------|-----|
| WebSearch | Comp research, trends, company culture, LinkedIn contacts, fallback for JDs |
| WebFetch | Fallback for extracting JDs from static pages |
| Playwright | Verify offers (browser_navigate + browser_snapshot). **NEVER 2+ agents with Playwright in parallel.** |
| Read | config/profile.md |
| Write | applications.md, reports .md |
| Edit | Update tracker |
| Canva MCP | Optional visual CV generation. Duplicate base design, edit text, export PDF. Requires `cv.canva_resume_design_id` in the YAML frontmatter of `config/profile.md`. |
| Bash | `node generate-pdf.mjs` |

</section>

### Time-to-offer priority
- Working demo + metrics > perfection
- Apply sooner > learn more
- 80/20 approach, timebox everything

</rules>

---

<reference id="ats-writing-rules">

## Professional Writing & ATS Compatibility

These rules apply to ALL generated text that ends up in candidate-facing documents: PDF summaries, bullets, cover letters, form answers, LinkedIn messages. They do NOT apply to internal evaluation reports.

### Avoid cliché phrases
- "passionate about" / "results-oriented" / "proven track record"
- "leveraged" (use "used" or name the tool)
- "spearheaded" (use "led" or "ran")
- "facilitated" (use "ran" or "set up")
- "synergies" / "robust" / "seamless" / "cutting-edge" / "innovative"
- "in today's fast-paced world"
- "demonstrated ability to" / "best practices" (name the practice)

### Unicode normalization for ATS
Avoid generating em-dashes, smart quotes, and zero-width characters in the first place — they cause ATS parsing failures.

### Vary sentence structure
- Don't start every bullet with the same verb
- Mix sentence lengths (short. Then longer with context. Short again.)
- Don't always use "X, Y, and Z" — sometimes two items, sometimes four

### Prefer specifics over abstractions
- "Reduced lead time from 45 to 28 days (−38%)" beats "improved supply chain performance"
- "MILP model for fleet sizing across 5 H₂ refuelling stations — zero-stockout configuration" beats "built an optimisation model"
- Name tools, projects, clients, and quantified outcomes when allowed

</reference>
