# Mode: deep — Deep Research Prompt

<purpose>
Generate a structured research prompt the user can paste into Perplexity, Claude, or ChatGPT to investigate a company and role before an interview or application decision. Output is user-facing; language follows the user, not the JD.
</purpose>

<rules>
- Output language: user-prompt language first; JD language only when the user gives no language signal (e.g. bare URL).
- Never add commentary around the prompt — output it directly as a clean markdown block.
- Never hardcode metrics or profile data — read candidate context from `config/profile.md` at generation time.
- Replace every `[placeholder]` with actual values from the JD before emitting.
</rules>

---

## Step 1 — Detect context

<process>

<phase id="1" name="Context detection">

<agent_instruction>
Before generating the prompt, detect the following signals from the JD and company name to adapt the sector-specific axes:
</agent_instruction>

<reference id="context-signals">

| Signal | Values |
|--------|--------|
| **Sector** | consulting · industrial/manufacturing · logistics/supply chain · finance/banking · energy/utilities · engineering services · tech/software · healthcare/pharma · public sector · other |
| **Company type** | listed · private · PE-backed · startup · public institution |
| **Role type** | analyst/consultant · engineer (civil/industrial/process) · operations · data/quantitative · product · other |

</reference>

<agent_instruction>
Use detected sector to adapt Axis 1 (sector lens) and Axis 4 (operations and culture). Inject the matching block from the `<reference id="sector-adapters">` table below.
</agent_instruction>

</phase>

---

## Step 2 — Generate the research prompt

<phase id="2" name="Prompt generation">

<output>
Emit the following prompt, fully filled in, as a clean markdown block:
</output>

<format>
```
## Deep Research: [Company] — [Role]

Context: I'm evaluating a candidacy for [role] at [company] ([sector], [country]).
I need actionable information for the interview and to assess fit.

### 1. Company and business model
- What does the company do, exactly? What is its core revenue model?
- What is its size? (employees, revenue, market cap or valuation if known)
- Who owns it? (listed, private equity, family-controlled, public institution)
- What markets or geographies does it operate in?
[SECTOR ADAPTER — inject from §Sector-specific questions, Axis 1]

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
[SECTOR ADAPTER — inject from §Sector-specific questions, Axis 4]

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
Given my profile (read from config/profile.md for specific experience):
- What unique value do I bring to this team given their current priorities?
- Which of my projects or experiences are most directly relevant?
- What story should I tell in the interview?
- What gaps or objections should I prepare for?
```
</format>

</phase>

</process>

---

## Sector-specific questions

<reference id="sector-adapters">
Inject the matching block into `[SECTOR ADAPTER]` placeholders in Axis 1 and Axis 4.

### Consulting / Professional services
**Axis 1:** What are their main practice areas and which are growing? · Key clients by sector or name? · How do they differentiate at their tier? · Staffing model — generalist, specialist, or hybrid?
**Axis 4:** Typical utilization rate and travel expectation? · Up-or-out culture or stable progression?

### Industrial / Manufacturing
**Axis 1:** What do they produce and where? Production footprint? · Supply chain — vertically integrated or outsourced? · Exposure to commodity price risk? · Sustainability / decarbonization commitments?
**Axis 4:** Site-based, plant-based, or office-based? · Interface between this role and production operations?

### Logistics / Supply Chain
**Axis 1:** Services offered — freight, warehousing, last-mile, 4PL? · Network structure (hubs, depots, routes)? · Tech stack — WMS, TMS, automation? · E-commerce vs. industrial/B2B exposure?
**Axis 4:** Operations-facing or analytical/strategic? · Mix of client-facing vs. internal work?

### Finance / Banking
**Axis 1:** Products — retail, corporate, investment, asset management? · Regulatory environment and compliance exposure? · Risk appetite and recent performance? · Organic growth or M&A?
**Axis 4:** Desk/team structure for this role? · Regulatory and compliance workload?

### Energy / Utilities
**Axis 1:** Energy mix — fossil, renewable, nuclear, hybrid? · Exposure to energy transition regulation (EU taxonomy, carbon markets)? · Major infrastructure projects underway? · Commodity price and regulatory risk?
**Axis 4:** Project-based, operational, or strategic role? · Interface with field/plant operations?

### Engineering services / Technical consulting
**Axis 1:** Sectors served and technical specialisation? · Design, project management, or both? · Typical project size and duration? · Standard software tools and methods?
**Axis 4:** Split between client-site and office work? · Relevant certifications or norms for this team?

### Tech / Software
**Axis 1:** Core product and problem solved? · High-level tech stack? · Engineering blog or public talks? · AI/ML adoption — core to product, supporting, or exploratory?
**Axis 4:** Deploy cadence, CI/CD practices? · Remote-first or office-first for engineering?

### Other / Unknown sector
**Axis 1:** Primary value creation mechanism? · Key assets, capabilities, or relationships? · What makes this company hard to replace in its market?
**Axis 4:** What does a typical week look like in this role?

</reference>
