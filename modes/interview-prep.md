# Mode: interview-prep — Company-Specific Interview Intelligence

<purpose>
Builds a company- and role-specific interview preparation report. Triggered when the user asks to prep for an interview at a specific company+role, or when an evaluation scores 4.0+ and the user updates status to `Interview`. Outputs a structured intel report covering process overview, round breakdown, likely questions, story bank mapping, technical checklist, and company signals.
</purpose>

<rules>
- NEVER invent interview questions and attribute them to sources. Inferred questions must be labeled `[inferred from JD]`.
- NEVER fabricate Glassdoor ratings or statistics. If data is missing, write "unknown — not enough data".
- Cite everything: every question, stat, and claim gets a source or an `[inferred]` tag.
- Generate in the language of the JD (EN default).
- Be direct — this is a working prep document, not a pep talk.
</rules>

## Inputs

<reference id="inputs">
| # | Source | What to read |
|---|--------|-------------|
| 1 | Company name + role title | Required — provided by user |
| 2 | `reports/` | Evaluation report (if exists) — archetype, gaps, matched proof points |
| 3 | `interview-prep/story-bank.md` | Existing prepared stories |
| 4 | `config/profile.md` (body) | CV sections — proof points |
| 5 | `config/profile.md` (YAML frontmatter + `strategy.*`) | Candidate context, narrative, gaps |
</reference>

## Process

<process>

<phase id="1" name="Research">

<step id="1" name="Run web searches">
<agent_instruction>
Run these WebSearch queries. Extract structured data, not summaries. Cite sources for every claim. If the company is small/obscure and yields few results, broaden to the role archetype at similar-stage companies, and note that intel is sparse.
</agent_instruction>

<reference id="search-queries">
| Query | What to extract |
|-------|-----------------|
| `"{company} {role} interview questions site:glassdoor.com"` | Actual questions, difficulty rating, experience rating, process timeline, rounds, offer/reject ratio |
| `"{company} interview process site:teamblind.com"` | Candid process descriptions, recent data points, comp negotiation details, hiring bar |
| `"{company} {role} interview process"` | Case study/problem-solving format, round structure, typical questions, prep advice |
| `"{company} culture values" OR "{company} annual report"` | Strategic priorities, stated values, what they publish about |
| `"{company} interview process {role}"` (general) | Blog posts, YouTube, prep guides, candidate write-ups — fills gaps from above |
</reference>
</step>

</phase>

<phase id="2" name="Build the Report">

<step id="2" name="Process overview">
<format>
```markdown
## Process Overview
- **Rounds:** {N} rounds, ~{X} days end-to-end
- **Format:** {e.g., recruiter screen → technical phone → take-home → onsite (4 rounds) → hiring manager}
- **Difficulty:** {X}/5 (Glassdoor avg, N reviews)
- **Positive experience rate:** {X}%
- **Known quirks:** {e.g., "case study in first round", "take-home analysis exercise", "group assessment centre", "presentation to panel"}
- **Sources:** {links}
```
</format>
</step>

<step id="3" name="Round-by-round breakdown">
<agent_instruction>
For each round discovered in research, produce one block. If round structure is unknown, state that and provide best available intel based on company size, stage, and role level.
</agent_instruction>
<format>
```markdown
### Round {N}: {Type}
- **Duration:** {X} min
- **Conducted by:** {peer / manager / skip-level / recruiter — if known}
- **What they evaluate:** {specific skills or traits}
- **Reported questions:**
  - {question} — [source: Glassdoor 2026-Q1]
  - {question} — [source: Blind]
- **How to prepare:** {1-2 concrete actions}
```
</format>
</step>

<step id="4" name="Likely questions">
<agent_instruction>
Categorize all discovered and inferred questions into four sections. For inferred questions, always label `[inferred from JD]`.
</agent_instruction>

<section name="Technical">
Questions about system design, coding, architecture, domain knowledge. For each: question, source, and what a strong answer looks like for this candidate specifically (reference CV proof points from `config/profile.md`).
</section>

<section name="Behavioral">
Questions about leadership, conflict, collaboration, failure. For each: question, source, and which story from `story-bank.md` maps best.
</section>

<section name="Role-Specific">
Questions tied to the specific JD (archetype-aware). For each: question, why they're likely asking it (which JD requirement it maps to), and the candidate's best angle.
</section>

<section name="Background Red Flags">
<agent_instruction>
Read `config/profile.md` (narrative + strategy keys) to identify gaps, transitions, or unusual elements that may raise questions.
</agent_instruction>
For each: likely question, why it comes up, recommended framing — honest, specific, forward-looking, never defensive.
</section>
</step>

<step id="5" name="Story bank mapping">
<format>
```markdown
| # | Likely question/topic | Best story from story-bank.md | Fit | Gap? |
|---|----------------------|-------------------------------|-----|------|
| 1 | ... | [Story Title] | strong / partial / none | |
```
</format>

<reference id="fit-legend">
- **strong** — story directly answers the question
- **partial** — story is adjacent, needs reframing
- **none** — no existing story — flag for the user
</reference>

<agent_instruction>
For each gap, suggest: "You need a story about {topic}. Consider: {specific experience from config/profile.md that could become a STAR+R story}." If the user wants to draft missing stories, help them build STAR+R format and append to `interview-prep/story-bank.md`.
</agent_instruction>
</step>

<step id="6" name="Technical prep checklist">
<agent_instruction>
Base items on what the company actually tests, not generic advice. Prioritize by frequency and relevance. Max 10 items.
</agent_instruction>
<format>
```markdown
- [ ] {topic} — why: "{evidence from research}"
- [ ] {topic} — why: "{their blog/product suggests this matters}"
- [ ] {topic} — why: "{asked in N/M recent Glassdoor reviews}"
```
</format>
</step>

<step id="7" name="Company signals">
- **Values they screen for:** name them, cite source (careers page, blog, Glassdoor reviews)
- **Vocabulary to use:** terms the company uses internally — shows homework (e.g., Stripe says "increase the GDP of the internet", Anthropic says "safety" not "alignment")
- **Things to avoid:** specific anti-patterns flagged in interview reviews
- **Questions to ask them:** 2-3 sharp questions that demonstrate research, tied to recent news or blog posts discovered in Step 1
</step>

</phase>

</process>

## Output

<output>
Save the full report to `interview-prep/{company-slug}-{role-slug}.md`.
</output>

<mapping>
File: `interview-prep/{company-slug}-{role-slug}.md`

Header:
```markdown
# Interview Intel: {Company} — {Role}

**Report:** {link to evaluation report if exists, or "N/A"}
**Researched:** {YYYY-MM-DD}
**Sources:** {N} Glassdoor reviews, {N} Blind posts, {N} other
```
Followed by all sections from Steps 2–7.
</mapping>

## Post-Delivery

<completion>
After delivering the report:
1. Ask if the user wants to draft stories for any gaps found in Step 5.
2. If they have a scheduled interview date, say: "Your interview is in {X} days. Want me to set a reminder to review this prep?"
3. If company research in Step 1 was thin, suggest: "Want me to run `deep` mode for a fuller picture of their strategy, culture, and competitive landscape?"
</completion>
