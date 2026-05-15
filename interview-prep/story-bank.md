# Story Bank — Master STAR+R Stories

This file accumulates your best interview stories over time. Each evaluation (Block F) adds new stories here. Instead of memorizing 100 answers, maintain 5-10 deep stories that you can bend to answer almost any behavioral question.

## How it works

1. Every time `/career-ops offer-analysis` generates Block F (Interview Plan), new STAR+R stories get appended here
2. Before your next interview, review this file — your stories are already organized by theme
3. The "Big Three" questions can be answered with stories from this bank:
   - "Tell me about yourself" → combine 2-3 stories into a narrative
   - "Tell me about your most impactful project" → pick your highest-impact story
   - "Tell me about a conflict you resolved" → find a story with a Reflection

## Stories

<!-- Stories will be added here as you evaluate offers -->
<!-- Format:
### [Theme] Story Title
**Source:** Report #NNN — Company — Role
**S (Situation):** ...
**T (Task):** ...
**A (Action):** ...
**R (Result):** ...
**Reflection:** What I learned / what I'd do differently
**Best for questions about:** [list of question types this story answers]
-->

### [Techno-Economic] Veganic Operational Improvement
**Source:** Report #001 — Veolia — Alternant Chef de Missions Direction Innovation Décarbonation
**S:** 7-month-old agro-chemistry SME with 5 identified operational weaknesses in a 1,242 m² industrial facility
**T:** Quantify impact and build investment case for 5 synergistic improvement proposals
**A:** Built full VAN/NPV model (18-month payback, 10% discount rate, energy cost net), roller table specs, ABC inventory classification, AutoCAD layout redesign — 48-page technical-economic report delivered
**R:** +68% palletizing throughput, 60→90% shelf utilization, VAN-positive at 10% discount rate
**Reflection:** Would integrate sensitivity analysis on discount rate from the start — the payback threshold is the decision driver, not the base case
**Best for questions about:** Analytical rigor, techno-economic studies, client delivery, quantitative impact, investment decisions

### [Analytical Courage] Bokashi National Feasibility — Contrarian Conclusion
**Source:** Report #001 — Veolia — Alternant Chef de Missions Direction Innovation Décarbonation
**S:** France mandates biowaste separation under AGEC (Jan 2024); 47% of population without access to collection; multiple competing technology options
**T:** Assess whether Bokashi fermentation is a viable national-scale solution
**A:** Bottom-up economic model, full carbon accounting (146,400 t CO₂ at Paris scale), primary interview with RECUP' Occitanie + 2-month participatory field experiment, comparative analysis vs. aerobic composting and anaerobic digestion
**R:** Contrarian conclusion: Bokashi not viable nationally; recommended methanization integrated into existing wastewater infrastructure (Toulouse Métropole operational model as proof of concept)
**Reflection:** The analytical courage to recommend against the hypothesis is as valuable as the analysis — the best consulting deliverable is one the client didn't expect
**Best for questions about:** Rigorous analysis, intellectual honesty, dealing with ambiguity, carbon/sustainability, structured recommendations

### [Technical Delivery] Air Liquide H₂ Supply Chain — DES + Heuristic
**Source:** Report #001 — Veolia — Alternant Chef de Missions Direction Innovation Décarbonation
**S:** Fleet dimensioning problem for H₂ swap distribution network: 1 filling center, 3 ramps, 5 HRS stations, heterogeneous consumption, driver shift + Sunday blackout constraints
**T:** Design dispatch heuristic and validate with discrete-event simulation for Air Liquide
**A:** Proprietary reactive greedy heuristic with multi-component cost function; DES in Simul8 (95% CI, 4 replicas, moving average warm-up k=10); 3 scenarios + 5 sensitivity analyses
**R:** Zero-stockout fleet configuration delivered to Air Liquide; Simul8 model and results presented to client
**Reflection:** Next time I'd formulate the heuristic as an LP relaxation to benchmark optimality gap vs. greedy solution
**Best for questions about:** Technical depth, client-facing delivery, energy transition, green molecules, structured problem-solving, simulation

### [Market Strategy] GENAQ Hackathon — 1st Prize Market Entry
**Source:** Report #001 — Veolia — Alternant Chef de Missions Direction Innovation Décarbonation
**S:** Atmospheric water generator startup needs B2C go-to-market strategy: €2,500 residential unit, pivot from consolidated B2B
**T:** Score all 36,000+ Spanish census sections and recommend full go-to-market strategy including business model and channel
**A:** MCDA model (7 criteria, 36K sections), K-Means clustering (5 clusters), parametric Excel model, competitor benchmarking, channel margin analysis, business model with unit economics and product roadmap
**R:** 1st prize / 80 competing teams; Leroy Merlin as channel partner; Mediterranean arc priority; product roadmap delivered
**Reflection:** Would add demand sensitivity at different price points — census scoring is powerful but has no consumer intent signal
**Best for questions about:** Strategic positioning, business model, market analysis, technology monitoring, competitive intelligence, winning under pressure

### [Autonomy & Initiative] MILP Staff Planning App — Solo Production Deployment
**Source:** Report #001 — Veolia — Alternant Chef de Missions Direction Innovation Décarbonation
**S:** CentraleSupélec associations spending 1–10 hours per event on manual staffing, with frequently suboptimal results
**T:** Design and deploy an optimization tool that works across 20+ associations with diverse operational rules
**A:** MILP (people × tasks × hours × days, 4+ indices), 20+ semi-structured stakeholder interviews, Gurobi, AI-assisted implementation, production validation with BDI Secretary General
**R:** Hours → seconds; validated in production; committed adoption from all CentraleSupélec associations
**Reflection:** Would document constraint specifications as formal contracts with user-operators from Day 1 — informal requirements introduce re-work risk in production
**Best for questions about:** Rigor, autonomy, initiative, stakeholder management, technical problem-solving, going from 0 to production
