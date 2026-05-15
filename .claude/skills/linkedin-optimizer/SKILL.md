---
name: linkedin-optimizer
description: Optimize LinkedIn profiles through structured interviews and best-practice-driven rewriting. Use when the user mentions LinkedIn, profile optimization, professional branding, headline rewriting, About section improvement, or wants to make their LinkedIn more discoverable and compelling. Also use when the user shares their LinkedIn profile text, asks about recruiter visibility, or mentions profile views, connections, or networking strategy.
---

# LinkedIn Profile Optimizer

A skill that transforms LinkedIn profiles through structured interviews grounded in 2026 best practices. It gets smarter each time you use it by persisting career context to a local file so future sessions pick up where you left off.

## How This Skill Works

Four phases. Enter at any phase depending on what the user needs:

1. **Discovery** — Learn who the user is and what they want from LinkedIn
2. **Audit** — Evaluate their current profile against best practices
3. **Rewrite** — Generate optimized copy section by section
4. **Save** — Persist career context so future sessions build on past work

---

## Career-Ops Integration (Read This First)

Before running any phase, check for career-ops data. If these files exist, read them silently and use them to pre-populate Phase 1 — do not ask questions whose answers are already there.

### Step 0 — Read career-ops files

| File | What to extract |
|------|----------------|
| `config/profile.md` | `candidate.*` (identity, contact), `target_roles.*` (primary roles, archetypes), `narrative.*` (headline, exit_story, superpowers, proof_points), `languages` |
| `config/cv.md` | Experience bullets (raw material for LinkedIn rewrite), Skills section (seed for 50+ LinkedIn skills), Education entries, Projects entries |
| `config/strategy.md` | Archetype table (role-specific framing and proof point mapping), exit narrative |
| `data/linkedin.md` | Previous session context — if it exists, skip questions already answered and confirm: "I have your profile context from last time — is everything still current?" |

### Data mapping: profile.md → LinkedIn sections

| Interview question (Phase 1) | Answered by |
|------------------------------|-------------|
| Current role / institution | `candidate.*` + Education in `cv.md` |
| Target role | `target_roles.primary` |
| What makes you different | `narrative.superpowers` |
| Measurable proof points | `narrative.proof_points` + project bullets in `cv.md` |
| Keyword targets | `target_roles.archetypes` names + `config/strategy.md` archetype keyword columns |
| Exit narrative / story | `narrative.exit_story` |
| Credibility signals | Education entries + awards in `profile.md` YAML |
| Languages | `languages` array |
| Technical skills | Skills section in `cv.md` |

### CV voice ≠ LinkedIn voice (CRITICAL)

Never copy-paste bullets from `config/cv.md` into LinkedIn. The two formats have different voices:

| CV (cv.md) | LinkedIn |
|---|---|
| Verb-first, no subject: `Optimised fleet sizing across 5 H₂ stations (Simul8)` | CAR format, first person welcome: `Led the fleet sizing analysis for Air Liquide's H₂ network — zero-stockout configuration validated across 12,000 simulation runs` |
| ≤ 12 words, ends with period | 1-3 sentences, more narrative |
| Tools in parentheses | Tools named naturally in prose |
| No "I" | "I", "we", "our team" allowed |

Use `cv.md` bullets as **raw material** — they contain the facts. Rewrite them in LinkedIn voice for every section.

### About section source

Draft the About section from:
- `narrative.exit_story` → adapt to LinkedIn's 5-part Hook/Value/Proof/Intent/CTA structure, first-person voice
- `narrative.superpowers` → value proposition sentences
- `narrative.proof_points` → proof bullets (rewritten in first person)
- `target_roles.primary` → "What I'm looking for" paragraph

The exit_story is already the right story — it just needs to be restructured into LinkedIn format and softened from CV tone to conversational tone.

---

## Phase 1: Discovery

Start by checking for existing context (`data/linkedin.md`). If career-ops files exist, pre-populate from them (see Career-Ops Integration above) and confirm the pre-filled data with the user before proceeding.

If career-ops files exist, **only ask** what the files cannot answer:

1. What does your current LinkedIn headline say? (Paste it exactly, or say "none yet")
2. What does your current About section say? (Paste it, or say "empty")
3. Are you actively job-seeking, building a personal brand, or both?
4. What do you want visitors to do after viewing your profile? (Message you, connect, visit a portfolio, book a call)
5. Which sections feel weakest or most embarrassing?

If NO career-ops files exist, run the full structured interview below. Don't fire all questions at once — ask 3-4 at a time in a conversational flow.

### Shortcut: Import from PDF

LinkedIn has a built-in "Save as PDF" feature (Profile page > Save as PDF). If the user provides a PDF file path, read it to extract their full profile. This skips most of the interview since the profile data is already captured. Then focus the interview on goals, audience, and differentiators.

If the user pastes profile text directly, parse it by section and proceed the same way.

### Full interview (only when no career-ops files exist)

**Identity & Positioning**
1. What is your current role and what industry are you in?
2. What is your target role or next career move? (Be specific — "alternant Business Analyst at a CAC 40 firm" beats "something in consulting")
3. Who do you want to find you on LinkedIn? (Recruiters, clients, founders, peers — be specific about the *type* of person)
4. What makes you different from others with your same job title? (This becomes your differentiator in the headline)

**Achievements & Proof**
5. What are your 3 most impressive measurable results? (Revenue grown, team size, users impacted, efficiency gains — numbers only)
6. What companies or brands on your resume carry credibility? (This feeds the "Ex-X, Ex-Y" credibility signal)
7. What tools, technologies, or methodologies are you known for? (These become searchable keywords)

**Goals & Audience**
8. Are you actively job-seeking, building a personal brand, generating leads, or something else?
9. What keywords would your ideal audience search for? (Think about what they'd type, not what you'd call yourself)
10. What do you want people to do after visiting your profile? (Connect, message you, visit your website, book a call)

**Current State**
11. What does your current headline say? (Paste it exactly)
12. What does your current About section say? (Paste it, or describe the gist if it's long)
13. Which sections of your profile are you most embarrassed by?

### Deepening Questions (ask when relevant)

14. Have you changed careers or industries? How do you bridge that story?
15. What's a professional opinion or insight you're known for that could seed content ideas?
16. Are there awards, publications, speaking gigs, or certifications that should appear?
17. What's your biggest career risk or pivot — and what did you learn?

---

## Phase 2: Audit

Evaluate the user's current profile against the best practices framework. Read `references/best-practices.md` for the full checklist.

Present the audit as a scored table:

```
| Section | Current State | Score | Priority |
|---------|--------------|-------|----------|
| Headline | "Software Engineer at ACME" — no keywords, no value prop | 2/10 | CRITICAL |
| About | Empty | 0/10 | CRITICAL |
| Experience | Lists duties, no outcomes | 3/10 | HIGH |
| Skills | Only 5 listed | 4/10 | HIGH |
| Featured | Not used | 0/10 | MEDIUM |
```

Scoring guidelines (from `references/best-practices.md`):
- **CRITICAL** = Fix immediately — this section is actively hurting visibility
- **HIGH** = Significant missed opportunity
- **MEDIUM** = Worth optimizing but won't make or break discoverability
- **LOW** = Nice-to-have polish

After the audit, ask: "Which sections should we tackle first? I'd recommend starting with [CRITICAL items]."

---

## Phase 3: Rewrite

Generate optimized copy section by section. Do one section at a time, get feedback, then move to the next.

### Headline (220 characters, most impactful section)

Use the proven formula. Pick the variant that best fits the user's positioning:

**Formula A — Value-first:** [Who you help] + [Outcome you create] | [Credibility signal]
> "Helping B2B SaaS teams ship 2x faster | Engineering Leader, Ex-Stripe | Data-Driven Delivery"

**Formula B — Role-keyword:** [Target Role] | [Specialty/Industry] | [Key Result]
> "Product Manager | B2B SaaS | Drove 40% user growth at 3 companies | Ex-Notion"

**Formula C — Expertise-stack:** [Role] helping [audience] [achieve X] | [Tech/Method] | [Proof]
> "Growth Marketer helping founders 3x pipeline | HubSpot, Content & Demand Gen | 5 exits"

Rules:
- Front-load the most searchable keywords in the first 60 characters (mobile truncation)
- Never use generic labels: "Strategic Thinker", "Passionate Leader", "Results-Driven"
- Always include at least one measurable result or credibility signal
- Use pipe separators (|) for scannability
- Generate 3 headline variants for the user to choose from
- **If career-ops files exist:** use `narrative.headline` as the starting point and `target_roles.archetypes` to determine which keywords to front-load

### About Section (2,600 characters max, target 800-1,500 words)

Structure using the 5-part framework:

1. **The Hook** (1-2 sentences) — Open with what drives you or a bold statement about your work. First person, conversational. The first ~100 characters are visible before "see more" — make them count.

2. **Value Proposition** (2-3 sentences) — What you do, who you help, and the impact you deliver. Be specific, not aspirational.

3. **Proof Points** (3-5 bullet points) — Quantified achievements. Numbers, percentages, before/after transformations. Each bullet should pass the "so what?" test.

4. **What You're Looking For** (1-2 sentences) — State clearly: hiring? Open to speaking? Looking for your next role? Be direct.

5. **Call to Action + Contact** (1-2 sentences) — Make it effortless to reach you. Include email or preferred contact method.

Rules: write in first person · short paragraphs (2-3 sentences) · avoid buzzwords: "passionate", "synergy", "leveraged", "thought leader" · show, don't claim.

**If career-ops files exist:** draft from `narrative.exit_story` (restructured into the 5-part framework above), `narrative.superpowers` (value prop sentences), `narrative.proof_points` (proof bullets rewritten in first person). Do not copy-paste — rewrite in LinkedIn voice.

### Experience Section (per role)

Transform each role from duties to outcomes. Use the CAR format:
- **Challenge** — What was the situation or problem?
- **Action** — What specifically did you do?
- **Result** — What measurable impact did it have?

Transform examples:
- Before: "Responsible for managing social media accounts"
- After: "Grew combined following from 10K to 75K across 5 platforms in 18 months, driving 3x inbound leads"

Rules:
- 3-5 bullets per role (more for recent roles, fewer for older ones)
- Start every bullet with a strong action verb (Led, Built, Designed, Grew, Reduced, Launched)
- Include numbers in at least 60% of bullets
- Don't list responsibilities — list achievements
- **If career-ops files exist:** read the corresponding entry in `config/cv.md` for facts, then rewrite each bullet in CAR/first-person format. Never copy the CV bullet verbatim.

### Skills Section

- Target 50+ skills (LinkedIn supports up to 100 — more skills = more search hits)
- Pin top 3 to align with target role keywords
- Order: most relevant to target role first
- Include both hard skills (tools, technologies) and validated skills (methodologies, frameworks)
- **If career-ops files exist:** seed from the Skills section in `config/cv.md`, then expand to 50+ by adding adjacent searchable terms from `target_roles.archetypes` keyword columns in `config/strategy.md`

### Featured Section

Recommend what to feature based on the user's work:
- Published articles or blog posts
- Presentations or talk recordings
- Notable projects with links
- Portfolio pieces
- Pinned LinkedIn posts that performed well

---

## Phase 4: Save Context

After each session, save the user's career context to `data/linkedin.md` (career-ops data layer — never auto-updated). Future sessions read this file to skip the full interview and jump straight to incremental improvements.

### Context File Template

```markdown
# LinkedIn Profile Context

_Last updated: {YYYY-MM-DD}_

## Career Identity
- Role: [current role]
- Institution: [current institution or company]
- Target: [what they're aiming for]
- Audience: [who should find them]
- Differentiator: [what makes them stand out]

## Key Achievements
- [achievement 1 with metric]
- [achievement 2 with metric]
- [achievement 3 with metric]

## Credibility Signals
- Education: [degrees, institutions]
- Certifications: [if any]
- Publications/speaking/awards: [if any]

## Keywords & Search Terms
- Primary: [top 3-5 keywords]
- Secondary: [supporting keywords]
- Audience search terms: [what their audience actually types]

## Current Profile State
- Headline: "[current headline text]"
- About: [current about summary — key themes only, not full text]
- Skills: [N] listed
- Featured: [N] items

## Goals & Intent
- Primary goal: [job seeking / branding / lead gen / other]
- Desired action: [what visitors should do]
- Open to work: [yes/no/quietly]

## Optimization History
- [YYYY-MM-DD] Session: [what was changed — before → after for each section]

## Effectiveness Tracking
- [YYYY-MM-DD] Profile views: [N] (up/down from previous)
- [YYYY-MM-DD] Recruiter messages: [N] this month
- [YYYY-MM-DD] Connection quality: [observation]
```

### The Self-Improving Loop

- **Session 1**: Read career-ops files, confirm data, ask LinkedIn-specific questions, run audit, rewrite sections → create `data/linkedin.md`
- **Session 2**: Read `data/linkedin.md`, ask "What's changed?" → jump straight to incremental improvements, update file
- **Session 3+**: The skill already knows keywords, achievements, and style preferences. Focus on refining copy, adding new achievements, adapting to career changes.

**Effectiveness feedback loop**: When the user reports results (more profile views, recruiter messages, connection quality), record that in `## Effectiveness Tracking`. Future sessions can double down on what's working and drop what isn't.

**Career change sync**: When the user gets a new job or changes direction, update `data/linkedin.md` — and offer to re-run `modes/ingest.md` to regenerate `config/cv.md` if `config/profile.md` was updated.

---

## Quick-Start Shortcuts

The user might want to jump straight to a specific section. Handle these naturally:

- **"Fix my headline"** → Read career-ops files + `data/linkedin.md` if exists, generate 3 headline variants
- **"Here's my LinkedIn PDF"** → Read the PDF, extract all sections, merge with career-ops data, focus on goals and differentiators
- **"Rewrite my About"** → Read career-ops files for source material, draft from `narrative.exit_story` + proof_points, ask for current About text to compare
- **"Audit my profile"** → Run Phase 2 audit against best practices
- **"Update my achievements"** → Check `config/profile.md` for new proof_points first, then ask for anything not yet in the file
- **"I got a new job"** → Update `data/linkedin.md`, rewrite headline and current role, suggest running `update-profile` to keep career-ops in sync
- **"What changed since last time?"** → Read `data/linkedin.md` Effectiveness Tracking and Optimization History, present summary

---

## Reference Files

- `references/best-practices.md` — Full best-practices checklist compiled from 2025-2026 research, with scoring criteria and optimization priorities. Read this when conducting an audit or when you need to justify a recommendation.
