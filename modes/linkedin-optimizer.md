# LinkedIn Profile Optimizer

Transform LinkedIn profiles through structured interviews grounded in 2026 best practices. Gets smarter each session by persisting career context to `data/linkedin.md` so future sessions pick up where you left off.

## How This Mode Works

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

### CV voice vs. LinkedIn voice (CRITICAL)

Never copy-paste bullets from `config/cv.md` into LinkedIn. The two formats have different voices:

| CV (cv.md) | LinkedIn |
|---|---|
| Verb-first, no subject: `Optimised fleet sizing across 5 H₂ stations (Simul8)` | CAR format, first person welcome: `Led the fleet sizing analysis for Air Liquide's H₂ network — zero-stockout configuration validated across 12,000 simulation runs` |
| 12 words or fewer, ends with period | 1-3 sentences, more narrative |
| Tools in parentheses | Tools named naturally in prose |
| No "I" | "I", "we", "our team" allowed |

Use `cv.md` bullets as **raw material** — they contain the facts. Rewrite them in LinkedIn voice for every section.

### About section source

Draft the About section from:
- `narrative.exit_story` — adapt to LinkedIn's 5-part Hook/Value/Proof/Intent/CTA structure, first-person voice
- `narrative.superpowers` — value proposition sentences
- `narrative.proof_points` — proof bullets (rewritten in first person)
- `target_roles.primary` — "What I'm looking for" paragraph

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
3. Who do you want to find you on LinkedIn? (Recruiters, clients, founders, peers — be specific about the type of person)
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

Evaluate the user's current profile against the best practices below. Present the audit as a scored table:

```
| Section | Current State | Score | Priority |
|---------|--------------|-------|----------|
| Headline | "Software Engineer at ACME" — no keywords, no value prop | 2/10 | CRITICAL |
| About | Empty | 0/10 | CRITICAL |
| Experience | Lists duties, no outcomes | 3/10 | HIGH |
| Skills | Only 5 listed | 4/10 | HIGH |
| Featured | Not used | 0/10 | MEDIUM |
```

Scoring guidelines:
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
- **Session 3+**: The mode already knows keywords, achievements, and style preferences. Focus on refining copy, adding new achievements, adapting to career changes.

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

## Best Practices Reference (2025-2026)

Compiled from analysis of 9 authoritative guides published 2025-2026. Sources include LinkedIn's own data, Taplio, ProResource, Careerflow, and platform-specific algorithm research.

### Why Optimization Matters

- All-Star (complete) profiles receive **40x more opportunities** than incomplete ones
- Profiles with professional photos get **14-21x more profile views**
- Optimized headlines drive **30-40% more profile views**
- **87% of recruiters** use LinkedIn to evaluate candidates during hiring
- LinkedIn processes **60+ billion searches annually** — keywords determine visibility

### The "Big 3" (Optimize These First)

The three sections that determine whether someone clicks or scrolls past:

1. **Photo** — trust signal, 3-6 second first impression
2. **Headline** — the most searchable text on the entire profile
3. **About** — the hook that converts a viewer into a connection/message

Everything else supports and deepens the story these three start.

### Section-by-Section Scoring Criteria

#### 1. Profile Photo

| Score | Criteria |
|-------|----------|
| 0/10 | No photo |
| 3/10 | Casual photo (vacation, group crop, selfie) |
| 5/10 | Decent photo but old, poorly lit, or distracting background |
| 8/10 | Professional headshot, good lighting, simple background |
| 10/10 | Professional headshot + branded banner that reinforces positioning |

Requirements: 400x400px minimum, face takes up 60-70% of frame, recent (within 2 years), wearing work-appropriate attire.

#### 2. Background Banner

| Score | Criteria |
|-------|----------|
| 0/10 | Default grey banner |
| 3/10 | Generic stock image |
| 5/10 | Company-branded banner but no personal value prop |
| 8/10 | Custom banner with value proposition text |
| 10/10 | Custom banner reinforcing headline + brand + contact info |

Dimensions: 1584 x 396px. **70% of users leave this blank** — filling it is an easy differentiator.

#### 3. Headline (Most Critical Section)

| Score | Criteria |
|-------|----------|
| 0/10 | No headline or "Open to Work" default |
| 2/10 | Job title only: "Software Engineer at ACME" |
| 4/10 | Job title + generic descriptors: "Strategic Leader | Results-Driven" |
| 6/10 | Role + industry + 1 keyword |
| 8/10 | Value proposition + keywords + credibility signal |
| 10/10 | Specific outcome + audience + method + proof (e.g., "Helping B2B SaaS Founders Build $500K+ Pipelines Through LinkedIn Content Strategy") |

**220 characters max. First ~60 characters visible on mobile — front-load keywords.**

Proven formulas:
- **A**: [Who you help] + [Outcome you create] | [Credibility signal]
- **B**: [Target Role] | [Specialty/Industry] | [Key Result]
- **C**: [Role] helping [audience] [achieve X] | [Tech/Method] | [Proof]

Kill list (never use): "Passionate", "Strategic Thinker", "Results-Driven", "Growth Enthusiast", "Synergy", "Thought Leader", "Innovative". These signal inexperience, not expertise.

#### 4. About Section (Summary)

| Score | Criteria |
|-------|----------|
| 0/10 | Empty or single sentence |
| 3/10 | Resume-speak, third person, lists of traits |
| 5/10 | Covers basics but no hook, no proof, no CTA |
| 7/10 | First person, has hook and proof points, but no CTA |
| 9/10 | Hook + value prop + proof + intent + CTA |
| 10/10 | All of the above + memorable voice + under 1,500 words |

**2,600 characters max. Target 800-1,500 words. First ~100 characters visible before "see more".**

Required structure:
1. Hook (1-2 sentences) — what drives you
2. Value proposition (2-3 sentences) — who you help, what impact
3. Proof points (3-5 bullets) — quantified achievements
4. What you're looking for (1-2 sentences)
5. CTA + contact (1-2 sentences)

Write first person. Short paragraphs (2-3 sentences). Conversational, not corporate.

#### 5. Experience Section

| Score | Criteria |
|-------|----------|
| 0/10 | Empty or only job titles with no description |
| 3/10 | Lists duties/responsibilities |
| 5/10 | Mix of duties and some achievements |
| 7/10 | Achievement-focused but sparse on metrics |
| 9/10 | Every bullet has a metric, action verb, impact |
| 10/10 | Full CAR format with before/after transformations |

**86% of users list job duties rather than measurable achievements.** Flipping this single thing is the highest-leverage change in the Experience section.

CAR format per bullet:
- **Challenge** — context/problem
- **Action** — what you specifically did
- **Result** — measurable impact

3-5 bullets per role. Recent roles get more. Action verbs: Led, Built, Grew, Reduced, Launched, Designed, Managed, Created. At least 60% of bullets should include numbers.

#### 6. Skills Section

| Score | Criteria |
|-------|----------|
| 0/10 | No skills listed |
| 3/10 | 1-10 skills, no pinning |
| 5/10 | 10-25 skills, top 3 pinned |
| 7/10 | 25-40 skills, top 3 pinned, ordered by relevance |
| 9/10 | 40-50 skills, all pinned, keyword-optimized |
| 10/10 | 50 skills + endorsements on top 3 + aligned with headline keywords |

**Up to 100 skills now supported (50 was the old limit).** LinkedIn's search algorithm uses skills as a primary filter. If a skill isn't listed, you're invisible for that search.

Pin top 3 to match target role keywords. Order by relevance to what you want to be found for.

#### 7. Featured Section

| Score | Criteria |
|-------|----------|
| 0/10 | Not used |
| 5/10 | 1-2 items but low relevance |
| 8/10 | 2-3 items showcasing best work |
| 10/10 | 3-5 items, lead asset is conversion-focused, includes CTA |

Showcase: articles, presentations, project links, portfolio pieces, pinned posts. **Put your most conversion-focused asset first** — many visitors don't scroll.

#### 8. Recommendations

| Score | Criteria |
|-------|----------|
| 0/10 | None |
| 3/10 | 1 generic recommendation |
| 6/10 | 2-3 specific recommendations |
| 9/10 | 3-5 recommendations from diverse sources (manager, peer, client) |
| 10/10 | 5+ specific, metrics-cited recommendations + you give them too |

#### 9. Custom URL

| Score | Criteria |
|-------|----------|
| 0/10 | Default URL with random characters |
| 10/10 | linkedin.com/in/yourname |

Easy win. Claim your custom URL at Settings.

#### 10. Contact Info & Visibility

| Score | Criteria |
|-------|----------|
| 0/10 | No contact info, profile hidden |
| 5/10 | Some contact info, partially visible |
| 10/10 | Full contact info, profile visible to all, "Open to" settings configured |

### Priority Matrix

When time is limited, optimize in this order:

| Priority | Sections | Time | Impact |
|----------|----------|------|--------|
| CRITICAL | Headline + About | 30-45 min | 40%+ more profile views |
| HIGH | Experience + Skills | 30-45 min | Demonstrates credibility |
| MEDIUM | Featured + Banner + Photo | 20-30 min | Differentiation, trust |
| LOW | Recommendations, URL, Contact | 15-20 min | Completeness, conversion |

### Common Mistakes

1. **Generic headlines** — "Marketing Manager at XYZ Corp" says nothing unique. The #1 mistake.
2. **Duties over achievements** — "Responsible for X" vs. "Grew X by Y%". The #2 mistake.
3. **Empty About section** — 2,600 free characters to tell your story and most people leave it blank.
4. **Buzzword soup** — "Passionate", "Strategic", "Results-Driven" signal the opposite of expertise.
5. **Too few skills** — Every missing skill is a search query where you're invisible.
6. **No Featured section** — Your portfolio is blank and nobody scrolls to find proof.
7. **Third person About** — It's your profile. Say "I", not "John is a..."
8. **No CTA** — Visitors land and don't know what to do next. Tell them.
9. **Static profile** — An optimized profile with no posting activity is a great storefront with no foot traffic.
10. **Set and forget** — Profiles decay. Review quarterly, update with new achievements.

### Keyword Strategy

LinkedIn is a search engine. Keywords determine whether you appear in results.

#### Where Keywords Matter (by weight)

| Section | Weight | Strategy |
|---------|--------|----------|
| Headline | Very High | Front-load target role keywords in first 60 chars |
| About | High | Repeat value statements naturally, use varied phrasings |
| Experience | High | Industry terms in achievement bullets |
| Skills | High | Direct match for recruiter filter searches |
| Current title | High | Use the title recruiters search for, not your internal title |

#### How to Find Your Keywords

1. Search LinkedIn for people in your target role — what terms appear repeatedly?
2. Check 5-10 job postings for your target role — what skills/keywords are listed?
3. Ask: "If a recruiter was looking for someone like me, what would they type?"
4. Use LinkedIn's search suggestions (type your role and see what auto-completes)

### The Algorithm in 2026

LinkedIn's algorithm rewards:
- **Profile completeness** — All-Star profiles get 40x more opportunities
- **Recent activity** — Posting, commenting, engaging keeps you visible
- **Keyword relevance** — Skills, headline, and About determine search ranking
- **Connection quality** — Not just count, but relevance and engagement
- **Consistency** — Information that aligns across sections (headline → About → Experience → Skills)

### Timeline for Results

- **Week 1**: Optimize headline + About → profile views typically increase within 48 hours
- **Weeks 2-3**: Update Experience + Skills → recruiter search visibility improves
- **Month 1**: Full optimization + first post → compounding visibility gains
- **Month 2-3**: Consistent engagement → connection quality improves, inbound messages increase
- **Quarterly**: Review and refresh — adapt to career changes, add new achievements
