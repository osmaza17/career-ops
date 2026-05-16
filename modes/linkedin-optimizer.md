# LinkedIn Profile Optimizer

<purpose>
Transform LinkedIn profiles through structured interviews grounded in 2026 best practices. Gets smarter each session by persisting career context to `data/linkedin.md` so future sessions pick up where you left off.
</purpose>

<rules>
- Never copy-paste bullets from `config/profile.md` into LinkedIn — use them as raw material and rewrite in LinkedIn voice (see voice table below).
- Never ask questions whose answers are already in `config/profile.md` or `data/linkedin.md`.
- Never submit or post anything — stop at draft. The user decides what goes live.
- Always generate 3 headline variants for the user to choose from.

**CV voice vs. LinkedIn voice:**

| CV (`profile.md` body) | LinkedIn |
|---|---|
| Verb-first, no subject: `Optimised fleet sizing across 5 H₂ stations (Simul8)` | CAR format, first person welcome: `Led the fleet sizing analysis for Air Liquide's H₂ network — zero-stockout configuration validated across 12,000 simulation runs` |
| 12 words or fewer, ends with period | 1–3 sentences, more narrative |
| Tools in parentheses | Tools named naturally in prose |
| No "I" | "I", "we", "our team" allowed |
</rules>

---

## Career-Ops Integration

<agent_instruction>
Before running any phase, silently read these files and use them to pre-populate Phase 1. Do not ask questions whose answers are already there.
</agent_instruction>

<reference id="profile-mapping">

| Interview question (Phase 1) | Source in `config/profile.md` |
|------------------------------|-------------------------------|
| Current role / institution | `candidate.*` + Education body |
| Target role | `target_roles.primary` |
| What makes you different | `narrative.superpowers` |
| Measurable proof points | `narrative.proof_points` + project bullets |
| Keyword targets | `target_roles.archetypes` + `strategy.adaptive_framing` keyword columns |
| Exit narrative / story | `narrative.exit_story` |
| Credibility signals | Education entries + awards in YAML |
| Languages | `languages` array |
| Technical skills | Skills section body |

</reference>

<reference id="about-sources">
Draft the About section from:
- `narrative.exit_story` — adapt to the 5-part Hook/Value/Proof/Intent/CTA structure, first-person voice
- `narrative.superpowers` — value proposition sentences
- `narrative.proof_points` — proof bullets rewritten in first person
- `target_roles.primary` — "What I'm looking for" paragraph

The exit_story is already the right story — restructure it into LinkedIn format and soften from CV tone to conversational.
</reference>

---

## Phase 1: Discovery

<phase id="1" name="Discovery">

<agent_instruction>
Check `data/linkedin.md` first. If it exists, confirm with the user: "I have your profile context from last time — is everything still current?" then skip questions already answered. If `config/profile.md` exists, pre-populate from the profile mapping above.
</agent_instruction>

### When career-ops files exist — only ask what files cannot answer

<user_prompt>
1. What does your current LinkedIn headline say? (Paste it exactly, or say "none yet")
2. What does your current About section say? (Paste it, or say "empty")
3. Are you actively job-seeking, building a personal brand, or both?
4. What do you want visitors to do after viewing your profile? (Message you, connect, visit a portfolio, book a call)
5. Which sections feel weakest or most embarrassing?
</user_prompt>

### When no career-ops files exist — full interview

<agent_instruction>
Don't fire all questions at once. Ask 3–4 at a time in a conversational flow.
</agent_instruction>

**Identity & Positioning**
<user_prompt>
1. What is your current role and what industry are you in?
2. What is your target role or next career move? (Be specific — "alternant Business Analyst at a CAC 40 firm" beats "something in consulting")
3. Who do you want to find you on LinkedIn? (Recruiters, clients, founders, peers — be specific)
4. What makes you different from others with your same job title?
</user_prompt>

**Achievements & Proof**
<user_prompt>
5. What are your 3 most impressive measurable results? (Revenue grown, team size, users impacted, efficiency gains — numbers only)
6. What companies or brands on your resume carry credibility?
7. What tools, technologies, or methodologies are you known for?
</user_prompt>

**Goals & Audience**
<user_prompt>
8. Are you actively job-seeking, building a personal brand, generating leads, or something else?
9. What keywords would your ideal audience search for?
10. What do you want people to do after visiting your profile?
</user_prompt>

**Current State**
<user_prompt>
11. What does your current headline say? (Paste it exactly)
12. What does your current About section say? (Paste it, or describe the gist if it's long)
13. Which sections of your profile are you most embarrassed by?
</user_prompt>

**Deepening (ask when relevant)**
<user_prompt>
14. Have you changed careers or industries? How do you bridge that story?
15. What's a professional opinion or insight you're known for?
16. Are there awards, publications, speaking gigs, or certifications that should appear?
17. What's your biggest career risk or pivot — and what did you learn?
</user_prompt>

### Shortcut: Import from PDF

<agent_instruction>
LinkedIn has a built-in "Save as PDF" feature. If the user provides a PDF path, read it to extract their full profile — this skips most of the interview. Focus the remaining questions on goals, audience, and differentiators. If the user pastes profile text directly, parse it by section and proceed the same way.
</agent_instruction>

</phase>

---

## Phase 2: Audit

<phase id="2" name="Audit">

<agent_instruction>
Evaluate the user's current profile against the scoring criteria in the Best Practices reference. Present the audit as a scored table, then recommend which sections to tackle first.
</agent_instruction>

<format>
```
| Section  | Current State                                      | Score | Priority |
|----------|----------------------------------------------------|-------|----------|
| Headline | "Software Engineer at ACME" — no keywords, no value prop | 2/10 | CRITICAL |
| About    | Empty                                              | 0/10  | CRITICAL |
| Experience | Lists duties, no outcomes                        | 3/10  | HIGH     |
| Skills   | Only 5 listed                                      | 4/10  | HIGH     |
| Featured | Not used                                           | 0/10  | MEDIUM   |
```
</format>

**Priority definitions:**
- **CRITICAL** — Fix immediately; actively hurting visibility
- **HIGH** — Significant missed opportunity
- **MEDIUM** — Worth optimizing but won't make or break discoverability
- **LOW** — Nice-to-have polish

<user_prompt>
Which sections should we tackle first? I'd recommend starting with [CRITICAL items].
</user_prompt>

</phase>

---

## Phase 3: Rewrite

<phase id="3" name="Rewrite">

<agent_instruction>
Do one section at a time. Get feedback before moving to the next.
</agent_instruction>

### Headline

<section name="Headline">

220 characters max. First ~60 characters visible on mobile — front-load keywords.

<reference id="headline-formulas">

| Formula | Pattern | Example |
|---------|---------|---------|
| A — Value-first | [Who you help] + [Outcome you create] \| [Credibility signal] | "Helping B2B SaaS teams ship 2x faster \| Engineering Leader, Ex-Stripe \| Data-Driven Delivery" |
| B — Role-keyword | [Target Role] \| [Specialty/Industry] \| [Key Result] | "Product Manager \| B2B SaaS \| Drove 40% user growth at 3 companies \| Ex-Notion" |
| C — Expertise-stack | [Role] helping [audience] [achieve X] \| [Tech/Method] \| [Proof] | "Growth Marketer helping founders 3x pipeline \| HubSpot, Content & Demand Gen \| 5 exits" |

Kill list — never use: "Passionate", "Strategic Thinker", "Results-Driven", "Growth Enthusiast", "Synergy", "Thought Leader", "Innovative"
</reference>

<agent_instruction>
Generate 3 variants. If career-ops files exist, use `narrative.headline` as starting point and `target_roles.archetypes` to determine which keywords to front-load.
</agent_instruction>

</section>

### About Section

<section name="About">

2,600 characters max. Target 800–1,500 words. First ~100 characters visible before "see more".

<reference id="about-structure">

| Part | Length | Content |
|------|--------|---------|
| 1. Hook | 1–2 sentences | What drives you or a bold statement. First ~100 chars must count. |
| 2. Value Proposition | 2–3 sentences | What you do, who you help, impact you deliver — specific, not aspirational |
| 3. Proof Points | 3–5 bullets | Quantified achievements; each must pass the "so what?" test |
| 4. What You're Looking For | 1–2 sentences | Direct: hiring? Open to speaking? Looking for next role? |
| 5. CTA + Contact | 1–2 sentences | Make it effortless to reach you; include email or preferred method |
</reference>

Rules: first person · short paragraphs (2–3 sentences) · avoid "passionate", "synergy", "leveraged", "thought leader" · show, don't claim.

<agent_instruction>
If career-ops files exist, draft from `narrative.exit_story` (restructured into the 5-part framework), `narrative.superpowers` (value prop), `narrative.proof_points` (bullets rewritten in first person). Do not copy-paste — rewrite in LinkedIn voice.
</agent_instruction>

</section>

### Experience Section

<section name="Experience">

<reference id="CAR-format">
**CAR format per bullet:**
- **Challenge** — context/problem
- **Action** — what you specifically did
- **Result** — measurable impact

Transform example:
- Before: "Responsible for managing social media accounts"
- After: "Grew combined following from 10K to 75K across 5 platforms in 18 months, driving 3x inbound leads"
</reference>

Rules:
- 3–5 bullets per role (more for recent, fewer for older)
- Start every bullet with a strong action verb (Led, Built, Designed, Grew, Reduced, Launched)
- At least 60% of bullets must include numbers

<agent_instruction>
If career-ops files exist, read the corresponding entry in `config/profile.md` body for facts, then rewrite each bullet in CAR/first-person format. Never copy the CV bullet verbatim.
</agent_instruction>

</section>

### Skills Section

<section name="Skills">

Target 50+ skills (up to 100 supported). Pin top 3 to align with target role keywords. Order by relevance to target role.

<agent_instruction>
If career-ops files exist, seed from the Skills section in `config/profile.md` body, then expand to 50+ by adding adjacent searchable terms from `target_roles.archetypes` and `strategy.adaptive_framing` keyword columns.
</agent_instruction>

</section>

### Featured Section

<section name="Featured">

Recommend based on user's work: published articles, presentations/talk recordings, notable projects with links, portfolio pieces, pinned LinkedIn posts that performed well. Put the most conversion-focused asset first.

</section>

</phase>

---

## Phase 4: Save Context

<phase id="4" name="Save Context">

<mapping>
After each session, save career context to `data/linkedin.md`. Future sessions read this file to skip the full interview and jump straight to incremental improvements.
</mapping>

<format>
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
- Primary: [top 3–5 keywords]
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
</format>

### The Self-Improving Loop

- **Session 1** — Read career-ops files, confirm data, ask LinkedIn-specific questions, run audit, rewrite sections → create `data/linkedin.md`
- **Session 2** — Read `data/linkedin.md`, ask "What's changed?" → jump straight to incremental improvements, update file
- **Session 3+** — Focus on refining copy, adding new achievements, adapting to career changes

<agent_instruction>
When the user reports results (profile views, recruiter messages, connection quality), record them in `## Effectiveness Tracking`. Future sessions double down on what's working.

When the user gets a new job or changes direction, update `data/linkedin.md` and offer to run `modes/update-profile.md` to sync `config/profile.md`.
</agent_instruction>

</phase>

---

## Quick-Start Shortcuts

<agent_instruction>
Handle these naturally without running the full 4-phase flow:
</agent_instruction>

| User says | Action |
|-----------|--------|
| "Fix my headline" | Read career-ops files + `data/linkedin.md`, generate 3 headline variants |
| "Here's my LinkedIn PDF" | Read PDF, extract all sections, merge with career-ops data, focus on goals and differentiators |
| "Rewrite my About" | Read career-ops files for source material, draft from `narrative.exit_story` + proof_points, ask for current About text to compare |
| "Audit my profile" | Run Phase 2 audit against Best Practices reference |
| "Update my achievements" | Check `config/profile.md` for new proof_points first, then ask for anything not yet in the file |
| "I got a new job" | Update `data/linkedin.md`, rewrite headline and current role, suggest running `update-profile` |
| "What changed since last time?" | Read `data/linkedin.md` Effectiveness Tracking and Optimization History, present summary |

---

## Best Practices Reference (2025–2026)

<reference id="best-practices">

Compiled from 9 authoritative guides (LinkedIn's own data, Taplio, ProResource, Careerflow, platform algorithm research).

### Why Optimization Matters

- All-Star profiles receive **40x more opportunities** than incomplete ones
- Profiles with professional photos get **14–21x more profile views**
- Optimized headlines drive **30–40% more profile views**
- **87% of recruiters** use LinkedIn to evaluate candidates
- LinkedIn processes **60+ billion searches annually** — keywords determine visibility

### Priority Matrix

| Priority | Sections | Time | Impact |
|----------|----------|------|--------|
| CRITICAL | Headline + About | 30–45 min | 40%+ more profile views |
| HIGH | Experience + Skills | 30–45 min | Demonstrates credibility |
| MEDIUM | Featured + Banner + Photo | 20–30 min | Differentiation, trust |
| LOW | Recommendations, URL, Contact | 15–20 min | Completeness, conversion |

### Section Scoring Criteria

#### 1. Profile Photo

| Score | Criteria |
|-------|----------|
| 0/10 | No photo |
| 3/10 | Casual photo (vacation, group crop, selfie) |
| 5/10 | Decent photo but old, poorly lit, or distracting background |
| 8/10 | Professional headshot, good lighting, simple background |
| 10/10 | Professional headshot + branded banner that reinforces positioning |

Requirements: 400×400px minimum, face 60–70% of frame, recent (within 2 years), work-appropriate attire.

#### 2. Background Banner

| Score | Criteria |
|-------|----------|
| 0/10 | Default grey banner |
| 3/10 | Generic stock image |
| 5/10 | Company-branded banner but no personal value prop |
| 8/10 | Custom banner with value proposition text |
| 10/10 | Custom banner reinforcing headline + brand + contact info |

Dimensions: 1584×396px. **70% of users leave this blank** — filling it is an easy differentiator.

#### 3. Headline

| Score | Criteria |
|-------|----------|
| 0/10 | No headline or "Open to Work" default |
| 2/10 | Job title only: "Software Engineer at ACME" |
| 4/10 | Job title + generic descriptors: "Strategic Leader \| Results-Driven" |
| 6/10 | Role + industry + 1 keyword |
| 8/10 | Value proposition + keywords + credibility signal |
| 10/10 | Specific outcome + audience + method + proof |

See headline formulas in [Phase 3](#phase-3-rewrite).

#### 4. About Section

| Score | Criteria |
|-------|----------|
| 0/10 | Empty or single sentence |
| 3/10 | Resume-speak, third person, lists of traits |
| 5/10 | Covers basics but no hook, no proof, no CTA |
| 7/10 | First person, has hook and proof points, but no CTA |
| 9/10 | Hook + value prop + proof + intent + CTA |
| 10/10 | All of the above + memorable voice + under 1,500 words |

See about structure in [Phase 3](#phase-3-rewrite).

#### 5. Experience Section

| Score | Criteria |
|-------|----------|
| 0/10 | Empty or only job titles with no description |
| 3/10 | Lists duties/responsibilities |
| 5/10 | Mix of duties and some achievements |
| 7/10 | Achievement-focused but sparse on metrics |
| 9/10 | Every bullet has a metric, action verb, impact |
| 10/10 | Full CAR format with before/after transformations |

**86% of users list job duties rather than measurable achievements.** See CAR format in [Phase 3](#phase-3-rewrite).

#### 6. Skills Section

| Score | Criteria |
|-------|----------|
| 0/10 | No skills listed |
| 3/10 | 1–10 skills, no pinning |
| 5/10 | 10–25 skills, top 3 pinned |
| 7/10 | 25–40 skills, top 3 pinned, ordered by relevance |
| 9/10 | 40–50 skills, all pinned, keyword-optimized |
| 10/10 | 50+ skills + endorsements on top 3 + aligned with headline keywords |

Up to 100 skills supported. If a skill isn't listed, you're invisible for that search.

#### 7. Featured Section

| Score | Criteria |
|-------|----------|
| 0/10 | Not used |
| 5/10 | 1–2 items but low relevance |
| 8/10 | 2–3 items showcasing best work |
| 10/10 | 3–5 items, lead asset is conversion-focused, includes CTA |

#### 8. Recommendations

| Score | Criteria |
|-------|----------|
| 0/10 | None |
| 3/10 | 1 generic recommendation |
| 6/10 | 2–3 specific recommendations |
| 9/10 | 3–5 recommendations from diverse sources (manager, peer, client) |
| 10/10 | 5+ specific, metrics-cited recommendations + you give them too |

#### 9. Custom URL

| Score | Criteria |
|-------|----------|
| 0/10 | Default URL with random characters |
| 10/10 | linkedin.com/in/yourname |

Easy win — claim at Settings.

#### 10. Contact Info & Visibility

| Score | Criteria |
|-------|----------|
| 0/10 | No contact info, profile hidden |
| 5/10 | Some contact info, partially visible |
| 10/10 | Full contact info, profile visible to all, "Open to" settings configured |

### Common Mistakes

1. **Generic headlines** — "Marketing Manager at XYZ Corp" says nothing unique. The #1 mistake.
2. **Duties over achievements** — "Responsible for X" vs. "Grew X by Y%". The #2 mistake.
3. **Empty About section** — 2,600 free characters and most people leave it blank.
4. **Buzzword soup** — "Passionate", "Strategic", "Results-Driven" signal the opposite of expertise.
5. **Too few skills** — Every missing skill is a search query where you're invisible.
6. **No Featured section** — Portfolio is blank and nobody scrolls to find proof.
7. **Third person About** — It's your profile. Say "I", not "John is a..."
8. **No CTA** — Visitors land and don't know what to do next. Tell them.
9. **Static profile** — An optimized profile with no activity is a great storefront with no foot traffic.
10. **Set and forget** — Profiles decay. Review quarterly, update with new achievements.

### Keyword Strategy

LinkedIn is a search engine. Keywords determine whether you appear in results.

| Section | Weight | Strategy |
|---------|--------|----------|
| Headline | Very High | Front-load target role keywords in first 60 chars |
| About | High | Repeat value statements naturally, use varied phrasings |
| Experience | High | Industry terms in achievement bullets |
| Skills | High | Direct match for recruiter filter searches |
| Current title | High | Use the title recruiters search for, not your internal title |

How to find your keywords:
1. Search LinkedIn for people in your target role — what terms appear repeatedly?
2. Check 5–10 job postings for your target role — what skills/keywords are listed?
3. Ask: "If a recruiter was looking for someone like me, what would they type?"
4. Use LinkedIn's search suggestions (type your role and see what auto-completes)

### The Algorithm in 2026

LinkedIn rewards:
- **Profile completeness** — All-Star profiles get 40x more opportunities
- **Recent activity** — Posting, commenting, engaging keeps you visible
- **Keyword relevance** — Skills, headline, and About determine search ranking
- **Connection quality** — Not just count, but relevance and engagement
- **Consistency** — Information that aligns across sections (headline → About → Experience → Skills)

### Timeline for Results

| Timeframe | Action | Expected outcome |
|-----------|--------|-----------------|
| Week 1 | Optimize headline + About | Profile views increase within 48 hours |
| Weeks 2–3 | Update Experience + Skills | Recruiter search visibility improves |
| Month 1 | Full optimization + first post | Compounding visibility gains |
| Month 2–3 | Consistent engagement | Connection quality improves, inbound messages increase |
| Quarterly | Review and refresh | Adapt to career changes, add new achievements |

</reference>
