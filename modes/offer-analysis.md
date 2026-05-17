# Mode: offer — Full Evaluation A-G

<purpose>
Evaluate a job offer end-to-end. When the candidate pastes a URL or JD text, deliver all 7 blocks (A–F evaluation + G legitimacy), save a report, and register the result in the tracker.
</purpose>

<rules>
- Deliver ALL blocks A–G in every evaluation — never partial.
- NEVER invent metrics or experience. Read `config/profile.md` at evaluation time.
- Block G does NOT affect the 1–5 global score — it is a separate qualitative tier.
- Tracker additions use the TSV → merge pipeline only. NEVER write directly to `data/applications.md`.
- Archetype detection and scoring system are defined in `_shared.md` — read them from there.
</rules>

---

## Step 0 — Pre-flight

<agent_instruction>
1. Read `config/profile.md` (YAML frontmatter + CV body sections).
2. Check `strategy.score_threshold` — note the user's minimum score; surface a warning at end of evaluation if final score falls below it.
3. Classify the offer into one of the 6 archetypes (see `_shared.md` → Archetype Detection). If hybrid, identify the 2 closest. This determines proof-point priority in Block B and summary framing in Block E.
4. First evaluation of the session: run `node cv-sync-check.mjs`. Notify user if warnings.
</agent_instruction>

---

## Block A — Role Summary

<format>
Table with:
- Detected archetype (or "Hybrid: X + Y")
- Domain (consulting / supply chain / OR / industrial / data analytics / PM / other)
- Function (build / consult / manage / deploy)
- Seniority
- Remote (full / hybrid / onsite)
- Team size (if mentioned)
- TL;DR in 1 sentence
</format>

---

## Block B — CV Match

<agent_instruction>
Read `config/profile.md` CV sections. Map each JD requirement to exact lines from the CV.
Prioritize proof points for the detected archetype (see `_shared.md` → Archetype Detection for per-archetype framing; also read `config/profile.md` key `strategy.adaptive_framing` for user-specific overrides).
</agent_instruction>

<format>
Requirement-to-CV mapping table.

**Gaps** section — for each gap:
1. Hard blocker or nice-to-have?
2. Adjacent experience available?
3. Portfolio project that covers this gap?
4. Concrete mitigation (cover letter phrase, quick project, etc.)
</format>

---

## Block C — Level and Strategy

<format>
1. **Detected level** in JD vs candidate's natural level for that archetype.
2. **"Sell senior without lying" plan** — specific phrases adapted to archetype, achievements to highlight, how to frame founder experience as an advantage.
3. **"If downleveled" plan** — accept if comp is fair, negotiate 6-month review, clear promotion criteria.
</format>

---

## Block D — Comp and Demand

<agent_instruction>
Run 2–3 WebSearch queries: current salaries for the role (Glassdoor, Levels.fyi, Blind) + company compensation reputation + role demand trend. Combine with Block G company research where possible to avoid duplicate queries.
If no data is available, say so — never invent figures.
</agent_instruction>

<format>
Table with salary data and cited sources.
</format>

---

## Block E — Personalization Plan

<format>
| # | Section | Current state | Proposed change | Why |
|---|---------|---------------|-----------------|-----|

Top 5 CV changes + Top 5 LinkedIn changes to maximize match.
</format>

---

## Block F — Red Flags

<agent_instruction>
Identify blockers, deal-breakers, and concerns not already surfaced in Blocks B-E. Read `config/profile.md` (key `strategy.deal_breakers`) for the user's hard stops.

**Check for:**
1. **Hard deal-breakers** — anything that matches `strategy.deal_breakers` in profile.md (relocation required, visa sponsorship unavailable, comp below minimum, etc.)
2. **Contract red flags** — unusual non-competes, IP assignment clauses, forced arbitration, probation unusually long
3. **Role red flags** — responsibilities that contradict the title, scope seems understaffed, "wearing many hats" language in a senior role
4. **Company red flags** — recent mass layoffs in the relevant department, leadership instability, funding concerns for startups, Glassdoor patterns
5. **Process red flags** — extreme urgency ("start immediately"), refusal to share salary range in regulated jurisdictions, ghosting patterns from public Glassdoor reviews

**Scoring impact:** Severe red flags (hard deal-breakers) cap the global score at 2.5 max. Moderate red flags apply a −0.3 to −0.5 adjustment. Minor flags are noted but do not affect the score.
</agent_instruction>

<format>
| Flag | Severity (Hard / Moderate / Minor) | Impact on score | Source |
|------|-------------------------------------|-----------------|--------|

If no significant red flags: "No significant red flags identified."
</format>

---

## Block G — Posting Legitimacy

<agent_instruction>
Assess whether the posting is a real, active opening. This helps the user prioritize effort. Does NOT affect the 1–5 global score.

**Signals to analyze** (reliability weights defined in `_shared.md` → Posting Legitimacy):

1. **Posting freshness** — date posted / "X days ago", apply button state, redirect detection (from Playwright snapshot captured in Step 0).
2. **Description quality** — tech specificity, team/org context, requirements realism, 6–12 month scope, salary mention, boilerplate ratio, internal contradictions.
3. **Company hiring signals** — 2–3 WebSearch queries (combine with Block D): `"{company}" layoffs {year}`, `"{company}" hiring freeze {year}`. Note department scope of any layoffs.
4. **Reposting detection** — check `data/scan-history.tsv` for same company + similar title with different URL. Note frequency and time span.
5. **Role market context** (qualitative, no additional queries) — typical fill time, role-company fit, seniority fill timeline.

**Edge cases:**
- Government/academic: 60–90 day age is normal — adjust thresholds.
- Evergreen/continuous hire: explicit "ongoing" or "rolling" language — note as context, not a ghost signal.
- Niche/executive (Staff+, VP, Director, highly specialized): months-long posting is expected.
- Startup/pre-revenue: vague JD may reflect a genuinely undefined role — weight vagueness less heavily.
- No date available: default to "Proceed with Caution" with a note. NEVER default to "Suspicious" without evidence.
- Recruiter-sourced (no public posting): freshness signals unavailable — active recruiter contact is itself a positive legitimacy signal.

**Ethical framing (MANDATORY):** Present observations, not accusations. Every signal has legitimate explanations. The user decides how to weigh them.
</agent_instruction>

<scoring>
**Assessment tier** (one of three — defined in `_shared.md` → Posting Legitimacy):
- **High Confidence** — multiple signals suggest a real, active opening
- **Proceed with Caution** — mixed signals worth noting
- **Suspicious** — multiple ghost indicators, investigate before investing time
</scoring>

<format>
**Signals table:** signal · finding · weight (Positive / Neutral / Concerning)

**Context Notes:** caveats that explain potentially concerning signals (niche role, government posting, evergreen position, etc.)
</format>

---

## Post-Evaluation

<agent_instruction>
Always execute both steps below after generating blocks A–G.
</agent_instruction>

### 1. Save Report

<mapping>
Save full evaluation to `output/reports/{###}-{company-slug}-{YYYY-MM-DD}.md`.
- `{###}` = next sequential number (3-digit zero-padded, max existing + 1)
- `{company-slug}` = company name lowercase with hyphens
- `{YYYY-MM-DD}` = current date
</mapping>

<format>
```markdown
# Evaluation: {Company} — {Role}

**Date:** {YYYY-MM-DD}
**Archetype:** {detected}
**Score:** {X/5}
**URL:** {url}
**Legitimacy:** {High Confidence | Proceed with Caution | Suspicious}
**PDF:** {path or pending}

---

## A) Role Summary
## B) CV Match
## C) Level and Strategy
## D) Comp and Demand
## E) Personalization Plan
## F) Red Flags
## G) Posting Legitimacy
## H) Draft Application Answers
(only if score >= 4.5 — skip entirely if below threshold)

For each visible application form question (from the JD or known ATS): draft a 2–4 sentence answer using the candidate's superpowers from `config/profile.md`. Lead with a specific achievement. Never exceed the visible character/word limit. Mark each answer with the form field name. Do NOT fabricate experience.

---

## Extracted Keywords
(15–20 JD keywords for ATS optimization)
```
</format>

### 2. Register in Tracker

<mapping>
Write one TSV line to `batch/tracker-additions/{num}-{company-slug}.tsv`:

```
{num}\t{YYYY-MM-DD}\t{company}\t{role}\tEvaluated\t{X.X}/5\t❌\t[{num}](output/reports/{num}-{slug}-{date}.md)\t{one-line note}
```

Column order: num · date · company · role · **status** · score · pdf · report · notes
(Status comes BEFORE score — `merge-tracker.mjs` handles the column swap into `applications.md`.)
</mapping>

<agent_instruction>
Immediately after writing the TSV, run:
```bash
node merge-tracker.mjs
```
</agent_instruction>
