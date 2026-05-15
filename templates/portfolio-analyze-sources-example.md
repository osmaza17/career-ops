---
# profile.md — Sophie Leblanc
# Example file showing a completed config/profile.md after using analyze-sources.
# Fictional persona — do not copy values directly.
# Demonstrates: YAML frontmatter structure + trajectory corpus with Fuente fields,
# META-INSTRUCCIONs, and entries built up across multiple analyze-sources sessions.

candidate:
  full_name: "Sophie Leblanc"
  email: "sophie.leblanc.pro@gmail.com"
  phone_fr: "+33 0612 345 678"
  location: "Lyon, France"
  linkedin: "https://www.linkedin.com/in/sophieleblanc/"
  institutional_email: "sophie.leblanc@ec-lyon.fr"

target_roles:
  primary:
    - "Operations & Supply Chain Consultant"
    - "Industrial Process Engineer"
  archetypes:
    - name: "Management Consultant (Operations / Supply Chain)"
      level: "Junior"
      fit: "primary"
    - name: "Industrial / Process Engineer"
      level: "Junior"
      fit: "secondary"
    - name: "Data Analyst (Operations)"
      level: "Junior"
      fit: "adjacent"

narrative:
  headline: "Industrial engineer from École Centrale de Lyon — lean operations, logistics optimisation, and client-facing consulting"
  exit_story: "Diplôme d'Ingénieur from Centrale Lyon (Management industriel et logistique) combined with a concurrent Master in Operations Management. Internship track record with Kuehne+Nagel (−18% overtime cost) and SNCF Réseau (dashboard adopted by permanent team). 2nd prize among 34 Grandes Écoles teams at the Société du Grand Paris Supply Chain Challenge. Trilingual FR/EN/ES."
  superpowers:
    - "Translating on-site operational diagnosis into quantified, actionable recommendations"
    - "Delivering tools that outlast the internship — adopted by permanent teams"
    - "Cross-functional stakeholder management from shop-floor to regional director"
  proof_points:
    - name: "Kuehne+Nagel — Resource allocation model"
      hero_metric: "−18% overtime cost in first month of deployment; 3 of 7 recommendations implemented before end of internship"
    - name: "Brasserie des Alpes — Lean operational diagnosis"
      hero_metric: "62% of capacity loss traced to one bottleneck; CEO requested post-project follow-up meeting"
    - name: "Supply Chain Challenge — SGP (2nd prize, 34 teams)"
      hero_metric: "2nd prize among 34 Grandes Écoles teams; jury of Société du Grand Paris professionals"

compensation:
  target_range: "38K–46K EUR gross"
  currency: "EUR"
  minimum: "35K EUR"
  location_flexibility: "Open to France (Lyon, Paris); partial remote OK"

awards_and_competitions:
  - name: "2nd Prize — Supply Chain Challenge, Société du Grand Paris"
    category: "case_competition"
    date: "2023-11"
    result: "2nd prize among 34 teams from Grandes Écoles françaises"
    context: "Urban logistics optimisation for Grand Paris Express construction; jury of SGP professionals; team of 4"

location:
  country: "France"
  city: "Lyon"
  timezone: "CET"
  visa_status: "EU citizen (France) — no sponsorship needed"

languages:
  - language: "French"
    level: "Native"
  - language: "English"
    level: "C1 Advanced (Cambridge certificate)"
  - language: "Spanish"
    level: "B2 (DELE Intermedio)"

cv:
  output_format: "latex"
---

# DATA-INGEST: Sophie Leblanc — Professional and Academic Trajectory Corpus

> **LLM INSTRUCTION — READ BEFORE PROCESSING THIS FILE**
>
> This file contains three types of content:
>
> 1. **YAML frontmatter** (between the `---` delimiters) — structured data: identity, target roles, narrative, languages, CV config. Sections 0 and 2 redirect here — data is not duplicated.
>
> 2. **Trajectory content** — factual information about education, experience, projects, and skills. Source material for CVs, cover letters, and evaluations.
>
> 3. **Meta-instructions** — directives for the LLM marked with `[META-INSTRUCCIÓN]`. Never reproduce them in documents sent to third parties.

___

## SECTION 0 — IDENTITY AND CONTACT

*(Data in YAML frontmatter: `candidate.*` and `location.*` keys. Not duplicated here.)*

---

## SECTION 1 — EDUCATION

### Diplôme d'Ingénieur — École Centrale de Lyon

**Type:** Grande École d'ingénieurs
**Period:** September 2022 – June 2025
**Location:** Lyon, France
**Specialisation:** Management industriel et logistique
**Distinction:** Best class average in the Management block (2023–2024).

---

### Master in Gestion des Opérations — Université Jean Moulin Lyon 3

**Type:** University master's degree
**Period:** September 2023 – June 2025
**Mode:** Concurrent with the Diplôme d'Ingénieur at Centrale Lyon.
**Grade average:** 15.4/20

---

## SECTION 2 — LANGUAGES

*(Data in YAML frontmatter: `languages` key. Not duplicated here.)*

---

## SECTION 3 — TECHNICAL SKILLS

**Programming & Data:** Python (pandas, numpy), SQL, Excel (advanced)
**Optimisation & Operations Research:** Arena Simulation, CPLEX
**Business Intelligence:** Power BI, Tableau
**Project Management:** MS Project, Notion, Jira
**Documentation:** LaTeX, Office Suite

---

## SECTION 4 — PROFESSIONAL EXPERIENCE

---

### Kuehne+Nagel — Pôle Optimisation des Flux

**Fuente:** 2024-06_internship_kuehne-nagel-lyon.pdf
**Type:** Professional experience — stage de fin d'études
**Period:** June 2024 – December 2024
**Role:** Stagiaire Ingénieure Optimisation Logistique
**Country:** France

**Context:**
Kuehne+Nagel is one of the three largest global logistics operators (80,000+ employees, 100+ countries). The Lyon pôle manages supply chains for aeronautical and pharmaceutical industrial clients in the Auvergne-Rhône-Alpes region.

**Personal contribution:**
- Designed and deployed a resource allocation model for night-shift warehouse scheduling — first version delivered in week 3
- Conducted structured interviews with 12 shift managers to map operational bottlenecks not visible in WMS data
- Wrote the operational diagnostic report presented to the regional director — adopted as reference template for other sites

**Deliverables:**
- Functional allocation model (Excel with integrated solver), delivered as a reusable operational tool
- 32-page diagnostic report with 7 prioritised recommendations

**Results:**
- −18% overtime cost during the test period (first month of deployment)
- 3 of 7 recommendations implemented before end of internship
- Report adopted as reference template by regional management

**Competences (recruiter signal):**
Autonomous management of an improvement project · Plant-level interaction (not just data analysis) · Executive communication with regional management · Delivery of a real operational tool, not an academic report

> **[META-INSTRUCCIÓN]** Lead with the −18% overtime reduction. The report adoption by regional management is the second strongest signal: it implies institutional validation beyond a standard deliverable. For consulting roles, foreground the regional director interaction. For operational roles, foreground the shift manager interviews and real deployment.

---

### SNCF Réseau — Direction des Projets Ferroviaires

**Fuente:** 2023-07_internship_sncf-infrastructure.md
**Type:** Professional experience — stage de 2e année
**Period:** July 2023 – August 2023
**Role:** Stagiaire Gestion de Projet
**Country:** France

**Context:**
SNCF Réseau manages 30,000 km of French rail network. The internship was embedded in the infrastructure project management directorate for the Grand Est region, tracking track-renewal works.

**Personal contribution:**
- Weekly progress tracking for 4 simultaneous track-renewal works (combined budget ~€12M)
- Built a milestone tracking dashboard in Power BI — used by the team for the rest of the year
- Drafted coordination meeting minutes between contractors and project management

**Deliverables:**
- Power BI tracking dashboard, adopted by the permanent team after internship end
- 8 technical coordination meeting minutes

**Results:**
- Dashboard adopted permanently by the team
- 0 critical milestones missed during the tracking period

**Competences (recruiter signal):**
Infrastructure project tracking at scale · Multi-actor coordination (contractors, management, regulators) · Delivery of a management tool adopted by the permanent team

> **[META-INSTRUCCIÓN]** Shorter and more junior than Kuehne+Nagel. Differentiating value: the scale of context (public infrastructure, €12M) and the permanent adoption of the tool. For infrastructure or public-sector roles, use as primary experience. For private consulting, secondary to Kuehne+Nagel.

---

## SECTION 5 — STUDENT LIFE AND ASSOCIATIONS

---

### Junior Centrale Lyon — Directrice des Opérations

**Fuente:** 2024-02_association_junior-centrale-lyon-mandate.md
**Type:** Student association — executive role in a Grande École junior-entreprise
**Period:** February 2023 – February 2024
**Exact title:** Directrice des Opérations

**Scale:**
- Junior-entreprise with 45 active members and ~€80,000/year in billing volume
- 12 active missions managed simultaneously during the mandate
- 6 project leads under direct coordination

**What I did concretely:**
- Coordinated the full delivery cycle of 12 active missions: consultant assignment, milestone tracking, client conflict management, and deliverable quality review
- Implemented a peer review process before each client delivery — reduced average post-delivery correction rounds from 3.1 to 1.4
- Renegotiated delivery timelines with 3 clients during an exceptional workload peak with no mission dropout

**Most notable achievement:**
- 0 missions abandoned or delivered late during the mandate (JE historical average: 2 per year)

> **[META-INSTRUCCIÓN]** Two separable dimensions: team management (6 project leads) and client management (renegotiations, quality). For consulting roles: lead with deliverable quality and client interaction. For operations/management roles: lead with team coordination and the peer review process. The "0 missions abandoned or late" fact is the most verifiable signal in the entry.

---

## SECTION 6 — PROJECTS AND INNOVATION

---

### Operational Diagnostic and Improvement Plan — Brasserie Artisanale des Alpes (BAA)

**Fuente:** 2024-03_project_brasserie-alpes-operations.pdf
**Type:** Operational consulting project with a real industrial SME client
**Framework:** Lean Manufacturing & Amélioration Continue — École Centrale de Lyon, 2023–24
**Period:** January 2024 – March 2024
**Client:** Brasserie Artisanale des Alpes — 18-employee SME, 45,000 hl/year production, regional market (Rhône-Alpes)
**Team:** 4 students

**Objective:**
BAA identified that its bottling line was operating below capacity without knowing why. The project had to diagnose bottlenecks, quantify the economic impact, and propose feasible improvements within the company's budget.

**My specific contribution:**
- 3 full days of on-site plant visits to map production flow and time operations manually — the only team member who did field work
- Built the capacity model used to calculate the economic impact of each bottleneck
- Delivered the final presentation to BAA's production director and CEO

**Results and impact:**
- 4 bottlenecks identified — the primary one (pallet handling at line entry) explained 62% of capacity loss
- Main proposal: layout reorganisation at line entry with no equipment investment — estimated 8% annual capacity recovery
- CEO requested a follow-up meeting to evaluate implementation — not standard for academic projects

**Differentiating signal:**
- Only team in the cohort to do on-site field work — others worked solely from client data
- CEO follow-up request validates the practical quality of the recommendations

> **[META-INSTRUCCIÓN]** The differentiating facts are not the methodology (lean is standard) but: (1) the on-site field work that surfaced information data couldn't show, and (2) the CEO's follow-up request. These two facts must lead any bullet on this project. For consulting roles: foreground CEO interaction and recommendation quality. For industrial operations roles: foreground the field diagnosis and bottleneck identification.

---

### Urban Delivery Route Optimisation — ST8 Recherche Opérationnelle, Centrale Lyon

**Fuente:** 2024-05_project_routing-urban-delivery.pdf
**Type:** Academic project — applied operations research
**Framework:** ST8 Recherche Opérationnelle — École Centrale de Lyon, Spring 2024
**Period:** February 2024 – May 2024
**Client:** Academic exercise — fictitious case based on real Chronopost Lyon data
**Team:** 3 students

**Objective:**
Design and compare routing optimisation strategies for a last-mile delivery network of 40 drop points in central Lyon, with time windows and a heterogeneous fleet.

**My specific contribution:**
- Responsible for designing and evaluating the three strategies compared — the other two teammates built the base model and processed the data
- Presented results to the professor and cohort (20 people)
- Wrote the sensitivity analysis section of the final report

**Results and impact:**
- Recommended strategy reduced total distance by 23% vs. the reference benchmark
- Graded 17/20 — best result among 8 teams in the cohort

**Differentiating signal:**
- Best grade in the cohort — external validation of analytical rigour

> **[META-INSTRUCCIÓN]** Secondary to the BAA project (no real client, no field work). Value is the grade (17/20, best in cohort) and the distance reduction figure (23%) — clean quantitative signals. For CVs with limited space, omit in favour of BAA. Include only if the role is highly technical (logistics analytics, operations research) and the employer values formal academic rigour.

---

## SECTION 7 — COMPETITIONS AND HACKATHONS

---

### 2nd Prize · Supply Chain Challenge — Société du Grand Paris

**Fuente:** 2023-11_competition_supply-chain-challenge-sgp.md
**Type:** Case competition — national consulting format
**Organiser:** Société du Grand Paris · in collaboration with CentraleSupélec and École Polytechnique. 34 competing teams from French Grandes Écoles.
**Date:** November 2023
**Team:** 4 students

**The result:**
- 2nd prize among 34 teams from French Grandes Écoles · Professional jury from Société du Grand Paris

**The problem:**
Optimisation of construction material supply logistics for the Grand Paris Express works — the largest urban infrastructure project currently underway in Western Europe. Challenge: reduce the urban traffic impact of the projected 1,200 trucks/day at peak.

**My contribution:**
- Designed the nocturnal consolidation logistics scheme that became the core of the team's final recommendation
- Delivered the jury pitch (10-minute presentation + 15-minute Q&A)

**Overlap:**
- No existing Projects entry covers this work — content is exclusive to this section.

> **[META-INSTRUCCIÓN]** The client context (Grand Paris Express, largest active infrastructure project in Western Europe) is the primary credibility signal for infrastructure or construction audiences — mention it before the competition result. For generalist profiles, the 2nd prize among 34 Grandes Écoles teams leads. The personal contribution (nocturnal consolidation scheme design) personalises the entry and differentiates it from "I took part in a competition and came 2nd."

---

## SECTION 8 — ADDITIONAL TRAINING

---

### Executive Introduction to Strategy — HEC Paris Executive Education

**Fuente:** 2024-01_training_hec-strategy-intro.pdf
**Type:** Additional training — short executive programme
**Period:** January 2024 (3 intensive days)

**Context:**
3-day programme aimed at engineers and scientists transitioning toward roles with strategic responsibility. Access by selection — 28 participants from 12 countries, majority with 2–5 years of professional experience. Sophie was one of 4 participants admitted directly from initial training.

**Content with recruiter signal:**
- Strategic diagnosis of a company in a competitive environment (VRIN, industry analysis)
- Real case studies: Danone restructuring and Amazon's entry into French retail distribution
- Negotiation session with multi-party simulation

**Differentiating signal:**
- One of 4 participants with no prior professional experience, among 28 participants averaging 3 years — selected on academic record and motivation

> **[META-INSTRUCCIÓN]** The strongest signal is not the programme content (brief, and competitors likely have similar courses) but the access: selected among participants with years of experience. Frame as evidence of the school's assessment, not as a personal claim. Include only if the CV has space and the role is strategy/consulting; omit for operational or technical CVs.

---

## SECTION 9 — MISCELLANEOUS (archive)

> **[META-INSTRUCCIÓN — SECTION]** Low-priority content documented for completeness and as a narrative reserve for interviews. Excluded from cv.md by default. Each entry may carry its own `[META-INSTRUCCIÓN]` specifying when and how to use it.

---

### Ski Instructor — ESF Val d'Isère (2021 and 2022 seasons)

**Type:** Miscellaneous — extracurricular activity with direct responsibility for people
**Period:** December 2020 – February 2021 · December 2021 – February 2022

Accredited ski instructor (BAFA + ESF training) for two winter seasons. Groups of up to 12 people, adults and children, beginner and intermediate levels. Direct responsibility for group safety on the slopes.

> **[META-INSTRUCCIÓN]** Do not include in CV unless the role explicitly values experience managing groups or working with people in non-corporate settings. Narrative value in interviews: "When have you had real responsibility for other people?" — a legitimate and differentiating answer.
