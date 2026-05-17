# Mode — Onboard: Build config/profile.md from Scratch

Two phases: **Phase 1** collects identity, roles, narrative, compensation, languages, strategy, and location policy. **Phase 2** builds the CV sections (summary, education, experience, student life, projects, competitions, skills, additional training). If `config/profile.md` already exists, ask the user whether to (a) continue filling in missing sections or (b) start fresh — if fresh, rename the existing file to `config/profile.md.bak` first.

---

<rules>

- **If `config/profile.md` already exists:** do not overwrite — ask to continue or start fresh. If fresh, rename to `.bak` first.
- Write to `config/profile.md` immediately after each user confirmation — after each Phase 1 group and after each Phase 2 CV entry. Never batch. Never write without explicit approval.
- Always include at least one concrete example answer after every question. Never ask a bare question without an example calibrated to the user's background.
- Always open each group and section with one plain-English sentence explaining what the information will be used for. No YAML keys, no technical jargon.
- Be collaborative on optional fields — ask what the user would *like* to include, explain trade-offs if they hesitate.
- Never fabricate metrics, invent outcomes, or inflate contributions. No metric = omit the number.
- If the user says "skip" for any section, move on without a placeholder — add it later via `analyze-sources`.
- All CV section YAML entries follow the templates in `modes/analyze-sources.md` exactly: verb-first bullets, title ≤ 45 chars for `projects` and `competitions`, max bullets respected.
- `additional_training`: omit the key entirely if empty; reverse chronological order when present.
- `projects`: ordered by decreasing impressiveness to the interviewer, not by date.
- `cv.output_format: "latex"` must always be present in the written file.

</rules>

---

<reference id="thematic_axes">

## Predefined Thematic Axes

Use this table when the user names a target role. Match their role to the closest entry and present the predefined axes for verification. Only ask the user to define axes from scratch if no match exists.

| Role | Thematic Axes |
|---|---|
| Strategy Consultant | Problem structuring, hypothesis-driven analysis, executive communication, strategic framing, stakeholder alignment, business case development |
| Operations Consultant | Process diagnostics, operational redesign, implementation delivery, cross-functional coordination, change management, performance benchmarking |
| Management Consultant | Structured problem-solving, slide-based synthesis, client management, multi-industry exposure, business case development, workshop facilitation |
| Business Analyst (consulting) | Requirements elicitation, process mapping, data-backed recommendations, solution design, gap analysis, stakeholder management |
| Supply Chain Consultant | Supply chain design, inventory optimization, logistics network analysis, cost reduction, supplier management, operational resilience |
| Transformation / Change Management Consultant | Change adoption, organizational design, stakeholder engagement, resistance management, communication planning, program governance |
| Process Improvement Consultant (Lean/Six Sigma) | Lean methodologies, value stream mapping, waste elimination, KPI design, root cause analysis, continuous improvement culture |
| Industrial Engineer | Systems optimization, capacity planning, production flow analysis, facility design, performance measurement, simulation modeling |
| Supply Chain / Logistics Analyst | Inventory management, demand-supply balancing, logistics cost analysis, network optimization, supplier coordination, S&OP process |
| Operations Analyst | Operational KPI monitoring, process improvement, data-driven diagnostics, root cause analysis, reporting automation, cross-functional collaboration |
| Demand Planning Analyst | Statistical forecasting, demand sensing, inventory positioning, S&OP coordination, forecast accuracy improvement, replenishment logic |
| Data Analyst (operations / supply chain focus) | Operational data wrangling, dashboard design, supply chain metrics, insight communication, SQL and Python proficiency, ad-hoc analysis delivery |
| Business Intelligence Analyst | Dashboard design, data modeling, KPI frameworks, self-service analytics, ETL pipeline development, stakeholder reporting |
| Operations Research Analyst | Mathematical optimization, simulation modeling, MILP, decision support, algorithm design, sensitivity analysis |
| Data Scientist (applied / industrial focus) | Predictive modeling, ML pipelines, feature engineering, business impact framing, model validation and monitoring, experiment design |
| Financial Analyst (FP&A) | Financial modeling, budgeting and forecasting, variance analysis, management reporting, scenario planning, business partnering |
| Project Manager (industrial / infrastructure) | Scope and schedule management, risk mitigation, cross-functional delivery, stakeholder coordination, resource planning, budget control |
| Digital Transformation Analyst | Digital roadmap design, process digitization, technology assessment, adoption enablement, agile delivery, vendor evaluation |

</reference>

---

<phase id="1" name="YAML Interview">

<phase_instructions>

Ask the seven question groups in order. Send each group as a single message. Wait for the answer before moving to the next. Never ask all groups at once.

**Confirmation gate — applies to every group:** After the user answers, echo back the key values you understood in one concise line ("Got it — I'll save: [summary of values]. Anything to correct?"). Write to `config/profile.md` immediately on confirmation. If the user corrects something, update and rewrite before moving to the next group. Never skip this step.

</phase_instructions>

---

<group id="1" name="Identity">

<user_prompt>
"Let's build your profile. I need this to set up your CV header and contact details — every part of the system reads from here.

A few basics to start:
- Full name, as it should appear on your CV (e.g. 'Oscar Martínez' or 'Marie Dupont')
- Primary email (e.g. oscar.martinez@gmail.com)
- Phone number(s) — if you have separate numbers for different countries, tell me both (e.g. +34 612 345 678 for Spain, +33 6 12 34 56 78 for France)
- LinkedIn URL (e.g. linkedin.com/in/oscar-martinez)
- University or institutional email — only if you want it on your CV, skip if not (e.g. oscar.martinez@polytechnique.edu)
- Current city and country (e.g. Paris, France)"
</user_prompt>

<mapping>
- `candidate.full_name`, `candidate.email`
- `candidate.phone_es` / `candidate.phone_fr` / `candidate.phone` (use single-key form if only one number)
- `candidate.linkedin`
- `candidate.institutional_email` (omit the key entirely if they say none)
- `location.city`, `location.country`, `location.timezone` (infer timezone from country/city)
- `cv.output_format: "latex"` — write this at this step if not already present
</mapping>

</group>

---

<group id="2" name="Target Roles">

<user_prompt>
"This is how I know which types of roles to tailor your profile and CV for — the more specific you are here, the better I can position you.

- Your primary job title (the one you'd write in a LinkedIn headline)
- Then 2–4 role archetypes — the distinct role flavors you're open to. For each:
  - Role name
  - Level (e.g. Junior, Alternant, Mid, Senior)
  - Fit: primary (dream), secondary (strong fit), or stretch (adjacent)
  - In one sentence starting with 'Someone who…', what does the buyer (the hiring manager) want?

Example:
- Primary title: Supply Chain & Operations Consultant
- Archetype 1: Strategy Consultant — Junior — Primary — 'Someone who structures ambiguous problems and turns them into board-ready recommendations'
- Archetype 2: Operations Analyst — Junior — Secondary — 'Someone who diagnoses inefficiencies and builds the roadmap to fix them'
- Archetype 3: Industrial Engineer — Junior — Stretch — 'Someone who designs and optimizes production systems end to end'"
</user_prompt>

<agent_instruction>
Once the user provides their roles, look up each in the `thematic_axes` reference table above:

- **Match found** — present the predefined axes for verification, do not ask the user to write them:
  > "For **[Role Name]**, here are the thematic axes I have on file — these describe the core skills and domains the hiring manager will evaluate you on:
  >
  > *[axis 1], [axis 2], [axis 3], [axis 4], [axis 5], [axis 6]*
  >
  > Do these feel right for how you want to position yourself in this role? Anything to add, remove, or reword?"

- **No match found** — ask the user to define them:
  > "I don't have a preset for **[Role Name]** — what domains or skills define it? Give me 4–6 short phrases (e.g. 'Problem structuring, quantitative analysis, client delivery, stakeholder alignment')."

Process all archetypes one at a time before moving to Group 3.
</agent_instruction>

<validation>
Mandatory for every archetype, no exceptions:
1. After presenting predefined axes (or receiving user-defined ones), **wait** for the user's explicit response. Do not proceed until they reply.
2. If the user edits, removes, or adds any axes, incorporate the changes and show the updated final list: "Here's the updated version — does this look right?"
3. **Only after the user explicitly confirms** ("yes", "looks good", "go ahead", or any clear affirmative) — write `thematic_axes` to `config/profile.md` under the corresponding archetype entry.
4. **Never write thematic axes before the user has validated them** — not the predefined ones, not user-defined ones.
</validation>

<mapping>
- `target_roles.primary` — list of job titles
- `target_roles.archetypes` — one entry per archetype: `name`, `level`, `fit`, `thematic_axes`, `value_proposition`

`value_proposition` cannot be left empty — if the user doesn't provide a 'Someone who…' sentence, ask for it before moving on.
</mapping>

</group>

---

<group id="3" name="Narrative">

<user_prompt>
"Now your story — the part that makes you sound like a person, not a list of jobs. This feeds your CV summary, your LinkedIn About section, and how I draft cover letter openers.

1. **Headline** — one line: how would you describe yourself professionally? (e.g. 'Industrial engineer from X — specializing in Y and Z')
2. **Short exit story** — 3–4 sentences, third-person, written as if a recruiter is describing you. What's your arc, your differentiator, and what you're looking for?
3. **Long version** — same story, first person, 4–6 sentences. This is for LinkedIn summaries and cover letter openers.
4. **Usage notes** — very briefly: when do you use the short version vs. the long one? (e.g. 'Short: PDFs and recruiter intros. Long: LinkedIn About and cold emails.')
5. **Superpowers** — 2–3 concrete differentiators. Not soft skills — things a reference could verify. (e.g. 'First student from UPV to do this exchange', 'MILP optimization adopted by 15+ associations')
6. **Proof points** — 2–3 named achievements, each with a metric. Give me a short label and one sentence with a number. (e.g. 'Staff Planning App — Reduced manual planning from 1–10h to seconds, adopted by 15+ associations')"
</user_prompt>

<agent_instruction>
If the user is vague (e.g., "I'm good at problem-solving"): ask one follow-up — "Can you give me a concrete example — a project, a result, something a reference could verify?" Never fabricate or paraphrase into something more impressive than what they said.
</agent_instruction>

<mapping>
- `narrative.headline`
- `narrative.exit_story` (short, third-person)
- `narrative.exit_story_long` (first-person, multi-sentence block — use YAML `|` block scalar)
- `narrative.exit_story_usage` — keys: `in_pdf`, `in_star`, `in_draft_answers` (or equivalent labels the user provides)
- `narrative.superpowers` — one item per differentiator
- `narrative.proof_points` — one entry per achievement: `name` (short label) + `hero_metric` (one sentence, metric-led)
</mapping>

</group>

---

<group id="4" name="Compensation">

<user_prompt>
"Compensation — this is private config used only by the negotiation and offer evaluation modes. It's never shared with companies.

- What's your target salary range? (include currency and gross/net — e.g. '45,000–55,000 EUR gross/year' or '1,600–2,100 EUR gross/month')
- What's your walk-away minimum? (private — only used by the negotiation mode, never shown to companies)
- Location and remote: where are you based, and what are your constraints? (e.g. Île-de-France only during studies, open after graduation, hybrid preferred)
- How do you like to answer comp questions in interviews?"
</user_prompt>

<agent_instruction>
If the user is in the French market, pre-fill `compensation.market_context` with a note about SYNTEC norms, Grande École comp bands, and convention collective relevance — then ask the user to confirm or adjust before writing.
</agent_instruction>

<mapping>
- `compensation.target_range`, `compensation.currency`, `compensation.minimum`
- `compensation.location_flexibility`
- `compensation.market_context`
- `compensation.approach` — the user's preferred framing when asked about salary expectations
</mapping>

</group>

---

<group id="5" name="Languages, Visa, and Awards">

<user_prompt>
"A few more things — this is what appears in the languages and awards sections of your CV.
- What languages do you speak? For each: name, level, and certificate if you have one (e.g. 'English — C1 Advanced, Cambridge certificate' or 'French — Native' or 'Spanish — B2, no certificate')
- Any prizes or ranked competitive results? (e.g. '1st prize at National Supply Chain Case Competition, 80 teams' or '2nd place, HEC Paris Strategy Challenge, 35 schools') — skip if none"
</user_prompt>

<agent_instruction>
Set `location.visa_status` — infer from nationality/location if mentioned in Group 1. If not clear, ask:
> "One more: what's your visa status? (e.g. 'EU citizen — no sponsorship needed within EU' / 'Requires visa sponsorship')"
</agent_instruction>

<mapping>
- `languages` — one entry per language: `language` + `level` (include certificate in level string, e.g. `"C1 Advanced (Cambridge certificate)"`)
- `awards_and_competitions` — for each prize: `name`, `category`, `date` (YYYY-MM), `result`, `context`. If none, set to `[]`.
- `location.visa_status`
</mapping>

</group>

---

<group id="6" name="Strategy">

<user_prompt>
"Now the strategy layer — this is how I frame you differently depending on the role type, and how I handle negotiation and scoring on your behalf. Keep it short — bullets are fine.

1. **Adaptive framing** — for each archetype we listed, what do you emphasize? Name 1–2 projects or proof points per role type, and say where in your profile they live (e.g. 'For Strategy Consultant: emphasize the operations research project and the junior enterprise consulting work, both in Projects section')
2. **Cross-cutting advantage** — in 2–3 sentences, what's the combination of qualities that makes you hard to replicate at your level? (e.g. 'I combine rigorous quantitative modeling with hands-on client delivery experience — rare at junior level. Trilingual and internationally mobile, which opens roles others can't take.')
3. **AI positioning** — how do you use AI tools in your work? One honest sentence. (e.g. 'I use AI to accelerate analysis and drafting, but always validate outputs before presenting to stakeholders.')
4. **Negotiation scripts** — how do you answer 'What are your salary expectations?' and 'Can you do better on comp?' Draft two short scripts. (e.g. 'Expectations: Based on SYNTEC norms for alternance in consulting, I'm targeting X–Y EUR gross/month. Can you do better: I'd need to understand the full package — benefits, bonus structure, and growth trajectory — before I could consider flexibility.')
5. **Deal-breakers** — 2–3 things that would make you walk away from a role. (e.g. 'No remote flexibility at all', 'Below X EUR gross/month', 'No structured mentorship or feedback culture')
6. **Fit signals** — what energizes you at work? What drains you? (2–3 items each) (e.g. Energizes: 'Solving structured problems with real constraints', 'Cross-functional projects with visible impact'. Drains: 'Repetitive admin with no ownership', 'Siloed teams with no visibility into outcomes')
7. **Portfolio** — do you have a public portfolio or GitHub URL you'd like to include? (e.g. 'github.com/osmaza17/cv-santiago' or 'no public portfolio yet')"
</user_prompt>

<agent_instruction>
Keep this group lightweight. If the user says "skip" for any sub-key, leave it as an empty placeholder — they can fill it in later.
</agent_instruction>

<mapping>
- `strategy.adaptive_framing` — list of objects: `role_type`, `emphasize`, `proof_sources`
- `strategy.cross_cutting_advantage` — multi-line string (YAML `|` block scalar)
- `strategy.ai_positioning` — multi-line string
- `strategy.negotiation_scripts` — object with keys the user defines (e.g. `salary_expectations`, `counter_offer`, `grande_ecole_angle`)
- `strategy.deal_breakers` — list of strings
- `strategy.fit_signals` — object with `energizes` (list) and `drains` (list)
- `portfolio.public_url` — the URL they provided, or `null` if none
- `portfolio.notes` — one sentence on portfolio status
</mapping>

</group>

---

<group id="7" name="Location Policy">

<user_prompt>
"Last group — this controls how location shows up on application forms and how I score roles based on geography when evaluating offers for you.

1. When you fill in a 'Location' field on an application form, what do you write? Give me 2–3 bullets — one for on-site roles during studies, one for remote/hybrid, one for post-graduation. (e.g. 'On-site during studies: Paris, Île-de-France only'; 'Remote/hybrid: open to all of France'; 'Post-graduation: open to relocation within Europe')
2. How should the system score roles by location? (e.g. Remote or hybrid in your city → score normally; Fully on-site outside your city during studies → score 3.0, it's a constraint but not a dealbreaker; Geographically incompatible, requires mid-studies relocation → score 1.0)"
</user_prompt>

<mapping>
- `location.in_forms` — list of 2–3 bullet strings
- `location.scoring_rules` — object with keys like `remote_or_hybrid`, `onsite_outside_city_during_studies`, `geographically_incompatible` — values are the scoring policy (e.g. `"Score normally"`, `"Score 3.0 (constraint, not dealbreaker)"`, `"Score 1.0"`)
</mapping>

</group>

---

<review name="Phase 1 Review">

After all seven groups, present the full accumulated content as a final checkpoint. By this point everything has already been written to `config/profile.md` progressively — this is a review, not the first save:

```
Here's everything saved so far — take a look before we move on to your CV sections:

---
[full content of config/profile.md as currently written]
---

Anything to change? If not, say "looks good" and we'll move on to Phase 2.
```

If the user requests changes: apply them, write the corrected keys to `config/profile.md` immediately, then show the updated block and ask for re-confirmation before proceeding.

</review>

</phase>

---

<phase id="2" name="CV YAML Sections">

<phase_instructions>
After Phase 1 is complete, automatically inventory `sources/` before doing anything else. Do not ask the user which method they prefer — check first, then report.
</phase_instructions>

<step id="1" name="Automatic Sources Inventory">

List all files in `sources/` (excluding `README.md` if present).

**If the folder is empty or doesn't exist**, skip to Step 3 and open with:
> "Phase 1 done — your profile metadata has been saved. The `sources/` folder is empty, so let's fill in your CV sections through a short interview. You can also drop documents into `sources/` at any point and I'll process them."

**If files are found**, announce the count and immediately begin with the first file — do not show a full inventory table or ask for ordering:
> "Phase 1 done — your profile metadata has been saved. I found [N] documents in `sources/` — I'll process them one at a time. Starting with **[filename 1]**."

Then proceed directly to Step 2 for that file.

</step>

---

<step id="2" name="Process Each Source File">

Follow `modes/analyze-sources.md` for the full processing sequence — classification, reading strategy, recruiter lens, probing questions, YAML drafting, review loop, metadata update, and file transition. The logic is identical. After all source files are processed or skipped, move to Step 3.

</step>

---

<step id="3" name="Conversation Collection">

After processing all source files (or immediately if `sources/` was empty), check which CV sections are still missing. Ask about each missing section in order. Skip any section already filled by source documents.

For YAML entry formats, follow the templates in `modes/analyze-sources.md`. Exceptions are noted below.

<review_loop>

After drafting each CV entry (from sources or from conversation), present it in full before writing:

> "Here's the draft entry for [section] — tell me what to change, or say 'add it' to write it to profile.md."
> ```yaml
> [full entry]
> ```

Show the complete entry on every revision round — never just the diff. Write to `config/profile.md` only on explicit approval ("add it", "looks good", "yes", "go ahead", or any clear affirmative).

**If the user drops new files into `sources/` mid-session:** pause, switch to Step 2 for those files, then resume where you left off.

</review_loop>

---

<section name="Summary">

<user_prompt>
"This is the first thing a recruiter reads — it sits at the top of your CV and sets the frame for everything below. Write a 3-sentence professional summary:
- Sentence 1: who you are + your specialization
- Sentence 2: your strongest proof points (the 2 most impressive things you've done)
- Sentence 3: what you're looking for

Example: 'Industrial engineer and operations researcher specializing in combinatorial optimization and supply chain systems. Developed a staff-planning MILP model adopted by 15+ student associations, and completed a supply chain internship with a focus on inventory reduction. Currently seeking a junior consulting or data role where analytical rigor meets client-facing delivery.'"
</user_prompt>

<agent_instruction>
Write as: `summary: |` followed by the three-sentence block. No first-person pronouns. Written as if introducing the user to a hiring manager.
</agent_instruction>

</section>

---

<section name="Education">

<user_prompt>
"This is the backbone of your CV for a junior profile — it tells recruiters what you studied, where, and what made it worth their attention. Tell me about your education — most recent first. Give me one degree at a time.
For each: exact degree title, institution name, start and end dates, country, and 2–3 bullets (key specialization, academic ranking or distinction, any notable exchange or award).

Example:
- Title: MSc Industrial Engineering & Operations Research
- Institution: École Polytechnique / Université Paris-Saclay
- Dates: Sept. 2023 -- Apr. 2025
- Country: France
- Bullets: 'Specialization in combinatorial optimization, supply chain systems, and stochastic modeling'; 'Ranked top 10% of cohort'; 'Exchange semester at TU Munich — focus on production systems'"
</user_prompt>

</section>

---

<section name="Experience">

<user_prompt>
"Tell me about your professional experience — internships, jobs, placements — most recent first.
For each: company, exact role/title, dates, country, and 2–3 bullets (deliverables, outcomes, metrics if you have them)."
</user_prompt>

<agent_instruction>
If the user has documents in `sources/` covering this experience, redirect:
> "You have a document in sources/ that likely covers this. Want me to process that instead? It'll give us better bullets."
Then switch to Step 2 for that file.

If deliverables are vague, ask: "What's the one thing your manager would remember you for — the most concrete deliverable or outcome from this role?"
</agent_instruction>

</section>

---

<section name="Student Life and Associations">

<user_prompt>
"This section shows recruiters you operate outside the classroom — leadership, scale, and ownership are what matter here. Any student clubs, associations, or elected roles?
For each: exact role title, organization name, dates, and what you actually did (members managed, events organized, budget, concrete achievement).

Example:
- Role: VP Operations, École Polytechnique Junior Enterprise — Sept. 2024 – Jun. 2025
- Bullets: 'Coordinated 8-person team across 12 client consulting projects'; 'Managed €45,000 annual project budget'; 'Reduced average delivery time by 3 weeks by standardizing project reporting'"
</user_prompt>

</section>

---

<section name="Projects">

<user_prompt>
"Projects are often the most powerful section for a junior profile — they show what you can actually deliver, not just what you studied. List academic projects, consulting studies, or personal technical projects, most impactful first.
For each: project type, client or context, dates, team size, and what you produced, recommended, or built.

Example:
- Title: Staff Planning Optimization Tool
- Context: Personal project — Python/MILP, no client
- Date: Jun. 2024
- Team: Solo
- What: Built a MILP-based scheduling tool that reduced manual planning from 1–10 hours to under 30 seconds; adopted by 15+ student associations at the university"
</user_prompt>

<agent_instruction>
Order entries by: competitive prizes → real clients → strong metrics → technical sophistication → purely academic.
</agent_instruction>

</section>

---

<section name="Competitions">

<user_prompt>
"Externally ranked results are one of the strongest signals on a junior CV — they show you can compete and win against peers from other schools. Any competitions or hackathons with an external ranking or prize?
For each: result (prize/ranking), organizer, date, team size, and what you built or solved.

Example:
- Result: 1st prize out of 42 teams
- Organizer: National Supply Chain Case Competition, CentraleSupélec Foundation
- Date: Nov. 2024
- What: Demand forecasting model that reduced simulated stockouts by 23%; presented to a jury of 5 industry executives"
</user_prompt>

<agent_instruction>
Only include externally verifiable results.
</agent_instruction>

</section>

---

<section name="Skills">

<user_prompt>
"List your technical tools and skills — grouped by category. Omit soft skills.
Example categories: Programming · Optimisation · Analytics & BI · Design · Project Management · AI & Automation
For each category, give me the tools separated by commas."
</user_prompt>

<agent_instruction>
Write as a `skills` array: each entry has `category` (string) and `items` (comma-separated string, not a list).
</agent_instruction>

</section>

---

<section name="Additional Training">

<user_prompt>
"This section is optional but can add real weight — selective programs signal access and ambition beyond the degree. Any courses, certificates, or extracurricular programs worth including?
For each: program name, organizer, dates, and what made it notable (selective access, executive interaction, strong network). Skip if none.

Example:
- Program: Executive Leadership Seminar — Institut Montaigne, Paris — Mar. 2024
- Notable: Selective admission (30 students from 200+ applicants); 3-day immersion with C-suite executives from CAC 40 companies"
</user_prompt>

<agent_instruction>
If no entries: omit the `additional_training` key entirely — do not write an empty array. Entries are ordered reverse chronologically.
</agent_instruction>

</section>

</step>

</phase>

---

<completion>

Once all sections are filled (or explicitly skipped):

**Step A — Show profile summary:**
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
> - Additional training: [N entries / not included]"

If unprocessed files remain in `sources/`:
> "There are [N] unprocessed files in `sources/`. Run `/career-ops analyze-sources` to extract entries from them."

**Step B — Set up portals (if `config/portals.yml` doesn't exist):**
> "I'll now set up the job scanner with 45+ pre-configured companies. Want me to customize the search keywords for your target roles? (I can pre-fill them from your archetypes.)"

If the user agrees, proceed with the portal setup from `modes/scan.md` (keyword customization only — do not run a scan yet).

**Step C — Initialize tracker (if `data/applications.md` doesn't exist):**

Create `data/applications.md` with this exact header and no data rows:

```markdown
# Applications Tracker

| #   | Date | Company | Role | Score | Status | PDF | Report | Notes |
| --- | ---- | ------- | ---- | ----- | ------ | --- | ------ | ----- |
```

**Step D — Show the ready message:**
> "You're all set! You can now:
> - Paste a job URL to evaluate it
> - Run `/career-ops scan` to search portals
> - Run `/career-ops pdf` with a job description to generate a tailored CV
> - Run `/career-ops` to see all commands
>
> Everything is customizable — just ask me to change anything.
>
> Tip: Having a personal portfolio dramatically improves your job search. If you don't have one yet, there is one open-source: github.com/osmaza17/cv-santiago — feel free to fork it and make it yours."

**Step E — Offer scan automation:**
> "Want me to scan for new offers automatically? I can set up a recurring scan every few days so you don't miss anything. Just say 'scan every 3 days' and I'll configure it."

If the user accepts, use the `/loop` or `/schedule` skill (if available) to set up a recurring `/career-ops scan`. If those aren't available, suggest adding a cron job or remind them to run the scan periodically.

</completion>
