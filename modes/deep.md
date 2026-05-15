# Mode: deep — Deep Research Prompt

## Language

This mode is **user-facing**: the output is a research doc the user reads, not content sent to the company. Override the shared "language of the JD (EN default)" rule and resolve output language in this order:

1. **User prompt language** — if the surrounding chat is in a non-English language, emit the doc in that language.
2. **JD language** — only as a last resort when the user prompt has no language signal (e.g., a bare URL).

---

## Step 1 — Detect context

Before generating the prompt, identify from the JD and company name:

| Signal | What to detect |
|--------|---------------|
| **Sector** | consulting · industrial/manufacturing · logistics/supply chain · finance/banking · energy/utilities · engineering services · tech/software · healthcare/pharma · public sector · other |
| **Company type** | listed · private · PE-backed · startup · public institution |
| **Role type** | analyst/consultant · engineer (civil/industrial/process) · operations · data/quantitative · product · other |

Use this to adapt axis 1 (sector lens) and axis 4 (operations and culture questions).

---

## Step 2 — Generate the research prompt

Output a structured prompt the user can paste into Perplexity, Claude, or ChatGPT:

```
## Deep Research: [Company] — [Role]

Context: I'm evaluating a candidacy for [role] at [company] ([sector], [country]).
I need actionable information for the interview and to assess fit.

### 1. Company and business model
- What does the company do, exactly? What is its core revenue model?
- What is its size? (employees, revenue, market cap or valuation if known)
- Who owns it? (listed, private equity, family-controlled, public institution)
- What markets or geographies does it operate in?
[SECTOR ADAPTER — see §Sector-specific questions below]

### 2. Strategic direction
- What are the company's stated priorities for the next 2–3 years?
- Are they growing, consolidating, or restructuring?
- What major bets or transformations are underway?
- What is their public positioning — what do they say about themselves?

### 3. Recent moves (last 6–12 months)
- Relevant hires at leadership or team level?
- Acquisitions, partnerships, or divestitures?
- New product/service launches or contract wins?
- Funding rounds, IPO, or ownership changes?
- Any notable leadership departures or restructuring?

### 4. Culture and work environment
- What does Glassdoor / LinkedIn / employer review sites say about the work environment?
- What is the office/remote/hybrid policy?
- What is the typical career trajectory from this type of role?
- What is the management style — hierarchical, flat, autonomous?
[SECTOR ADAPTER — see §Sector-specific questions below]

### 5. Challenges and pressures
- What are the main business pressures this company faces right now?
- Who are their main competitors and how are they positioned against them?
- What pain points come up repeatedly in reviews, press, or analyst reports?
- Is the company growing faster or slower than its sector average?

### 6. The role and team
- What does this team do day-to-day? What are typical projects or deliverables?
- Why are they hiring for this role now — growth, replacement, or new capability?
- What would success look like in 6–12 months for someone in this role?
- Who would I be working with and reporting to?

### 7. Candidate angle
Given my profile (read from config/cv.md and config/profile.md for specific experience):
- What unique value do I bring to this team given their current priorities?
- Which of my projects or experiences are most directly relevant?
- What story should I tell in the interview?
- What gaps or objections should I prepare for?
```

---

## Sector-specific questions

Inject into **Axis 1** and **Axis 4** based on the detected sector. Replace the `[SECTOR ADAPTER]` placeholder with the relevant block below.

### Consulting / Professional services
**Axis 1 addition:**
- What are their main practice areas and which are growing?
- Who are their key clients — by sector, size, or name if known?
- How do they differentiate from other firms at their tier?
- What is their staffing model — generalist, specialist, or hybrid?

**Axis 4 addition:**
- What is the typical utilization rate and travel expectation?
- Up-or-out culture or more stable progression?

---

### Industrial / Manufacturing
**Axis 1 addition:**
- What do they produce and where? What is their production footprint?
- What is their supply chain structure — vertically integrated or outsourced?
- What are their key inputs and how exposed are they to commodity price risk?
- What are their sustainability or decarbonization commitments?

**Axis 4 addition:**
- Is the role site-based, plant-based, or office-based?
- What is the typical interface between the role and production operations?

---

### Logistics / Supply Chain
**Axis 1 addition:**
- What logistics services do they offer — freight, warehousing, last-mile, 4PL?
- What is their network structure (hubs, depots, routes)?
- How tech-enabled are their operations — WMS, TMS, automation?
- What is their exposure to e-commerce vs. industrial/B2B flows?

**Axis 4 addition:**
- Is the role operations-facing or more analytical/strategic?
- What is the typical mix of client-facing vs. internal work?

---

### Finance / Banking
**Axis 1 addition:**
- What products or services do they offer — retail, corporate, investment, asset management?
- What is their regulatory environment and key compliance exposure?
- What is their risk appetite and recent credit/market performance?
- Are they growing organically or through M&A?

**Axis 4 addition:**
- What is the desk/team structure for this role?
- What is the regulatory and compliance workload for someone in this role?

---

### Energy / Utilities
**Axis 1 addition:**
- What is their energy mix — fossil, renewable, nuclear, hybrid?
- What is their exposure to energy transition regulation (EU taxonomy, carbon markets)?
- What major infrastructure projects are underway?
- How exposed are they to commodity price and regulatory risk?

**Axis 4 addition:**
- Is the role project-based, operational, or strategic?
- What is the typical interface between this role and field/plant operations?

---

### Engineering services / Technical consulting
**Axis 1 addition:**
- What sectors do they serve and what is their technical specialisation?
- Do they do design, project management, or both?
- What is their typical project size and duration?
- What software tools and methods are standard in their practice?

**Axis 4 addition:**
- What is the typical split between client-site and office work?
- What certifications or norms are relevant for this team?

---

### Tech / Software
**Axis 1 addition:**
- What is their core product and what problem does it solve?
- What is their tech stack at a high level?
- Do they have an engineering blog or public talks? What do they publish?
- What is their AI/ML adoption — core to product, supporting, or exploratory?

**Axis 4 addition:**
- How do they ship — deploy cadence, CI/CD practices?
- Remote-first or office-first culture for the engineering team?

---

### Other / Unknown sector
**Axis 1 addition:**
- What is the company's primary value creation mechanism?
- What are their key assets, capabilities, or relationships?
- What would make this company hard to replace in its market?

**Axis 4 addition:**
- What does a typical week look like for someone in this role?

---

## Output format

Emit the filled-in research prompt as a clean markdown block the user can copy and paste. Customise every `[placeholder]` with the actual values from the JD. Do not add commentary around the prompt — just output it directly.
