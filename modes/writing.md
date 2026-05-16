# Writing Conventions — CV Output Quality

All rules in this file govern **how the CV is written and formatted**. They apply to every mode that produces or edits CV content (`analyze-sources`, `latex`, `pdf`, `update-profile`). They are language-agnostic unless explicitly marked FR or EN.

---

## 1. Bullet Ordering Rules

### 1.1 R0 — Most impressive fact first (within an entry)

The single most impactful bullet goes first. A recruiter who reads only the first bullet of each entry must leave with the best possible impression.

What counts as most impressive: a hard metric, a named client or institution, a competition result, a unique outcome. If two bullets share a metric, the larger or more surprising one goes first.

### 1.2 Default structure when no single fact dominates

When no bullet clearly dominates, order as: **method or approach → scope or scale → outcome**. Outcome goes last unless it is the single most striking fact (prize, record metric) — in which case lead with it.

### 1.3 Project entry ordering (across the Projects section)

Order project entries by decreasing interest to the interviewer:

1. Competitive prizes — ranked against other candidates, judged externally. Most credible signal.
2. Real clients, especially well-known ones — CAC 40, Fortune 500, named institutions. Shows real-world delivery.
3. Projects with strong quantified outcomes — compelling numbers regardless of client prestige.
4. Technically sophisticated projects — novel methodology, complex modelling, original design.
5. Purely academic projects — no external client, no prize. Go last.

When two entries tie on tier, the one with the harder metric or more recognizable name goes first. Never order by date.

---

## 2. Bullet Structure Rules

### 2.1 R1 — Length

Single sentence. ≤ 12 words preferred; 18 words hard limit. Ends with a period.

### 2.2 R2 — Opening

**English:** Opens with a past-tense action verb. No subject — the verb stands alone, implying first person without writing "I".

Pattern: `[Past-tense verb] [object] [qualifier] (tools or scale).`

**French:** Opens with a deverbal noun. Never a past participle, infinitive, gerund, or "Responsible for."

Pattern: `[Deverbal noun] de/d'/du/des [object] [qualifier] (tools or scale).`

> Education bullets are factual statements and do NOT follow these opening rules.

### 2.3 R3 — Parentheses

Parentheses are permitted in exactly **two** contexts:

1. **End of bullet — tools, scale, method:** `(CYPE)`, `(Simul8, white-box validation)`, `(légal, économique, environnemental, comportemental)`
2. **Inline qualifying detail** that would break sentence flow: `(relation client directe, visite terrain, cartographie des flux)`

Not used to explain acronyms that are clear from context. Not used in the middle of a sentence except for case 2. Two parenthetical groups in one bullet → keep only the more informative one.

### 2.4 R4 — One action per bullet

One action per bullet. If two actions exist, split into two bullets.

### 2.5 R5 — Bold usage within bullets

Bold only on:
- Hard verifiable metrics (figures, %, €, rankings, counts)
- Competition results
- Named prestigious clients when they appear in bullets
- Named methodology when it is the key differentiator (MILP, VAN, MCDA) — not just mentioned in the JD

Maximum **2 bold elements per bullet**.

Never bold: verbs, adjectives, tool names (CYPE, Simul8), section names, company names in entry titles.

### 2.6 R6 — Real figures only

Real figures only. If no metric exists, omit the number — a bullet without a figure is fine. If an entire entry has no quantifiable metric, compensate with specificity: name the client, state the scale, or give a concrete qualitative outcome. Never invent or approximate.

### 2.7 R7 — Concision

If the same idea fits in fewer words, use fewer words. Do not pad.

### 2.8 R8 — Manager filter

Every bullet must be useful to a non-specialist hiring manager. If the fact only makes sense to someone already deep in the implementation, cut the technical detail and keep only the outcome, scale, or competence signal. Ask: "Would a recruiter who doesn't know the domain find this useful?" If not, rewrite.

### 2.9 R9 — Purpose clarity

The first bullet of any project or experience entry must make clear (a) what was being done and (b) for whom or to what end. Do not lead with methodology when the project's purpose is still unknown to the reader.

---

## 3. Bold Usage — Global Rules

Bold is a scarce resource. Its only purpose is to make the reader's eye stop on the 3–5 most impressive facts on the entire page. If it appears too often, it stops working.

**Global ceiling: ≤ 8 bold elements across the full CV.** Count before finalizing.

**Bold on:** hard verifiable metrics (figures, %, €, rankings, counts), competition results, named prestigious clients in bullets, named methodology when it is the key differentiator.

**Never bold:** injected JD keywords, tool names, methodology names just because they appear in the JD, the candidate's name in running text, company names in entry titles, verbs, adjectives, section names.

**Structural bold exception:** The well-known client name in a project title (`--- **Air Liquide**`) counts toward the global ceiling. Do not treat it as exempt.

**Self-check:** Read only the bold text across the CV. Does it alone make a compelling case — a metric, a ranking, a client name, a prize? If not, reassign which elements receive bold.

---

## 4. Parentheses Rule

See section 2.3 (R3) above — two permitted contexts only. Summary:

1. End of bullet (tools, scale, method)
2. Inline qualifying detail that breaks sentence flow

No other use is valid.

---

## 5. Colon Convention

The colon introduces the result or a list of specifics after the method.

**French typography:** space before colon — `[method] : [result or enumeration]`

**English typography:** no space before colon — `[method]: [result or enumeration]`

Examples:
- FR: `Modélisation VAN de l'investissement optimal : retour en 18 mois et +68 % de productivité.`
- EN: `Modelled NPV of the optimal investment: break-even in 18 months and +68% palletisation throughput.`
- Summary S2 (both languages): `My background combines X and Y: [example 1], [example 2], and [example 3].`

---

## 6. Middle-dot (·) Prohibition

`·` is **completely prohibited** in all CV content — titles, headlines, bullets, subtitles, running text.

**The one permitted exception:** the `·` hardcoded inside the `\cventry` LaTeX command between company and post (`\MakeUppercase{#1} · #2`). This is structural and produced by the command definition — it cannot and must not be removed. Do not reproduce this pattern anywhere else.

Replace every other `·` with a comma.

Examples:
- Wrong: `\cvheadline{Élève-ingénieur · Recherche Opérationnelle}` → use `specialized in` pattern
- Wrong: `\cventryprojet{Diagnostic opérationnel · Veganic}{...}` → use a comma
- Wrong: Student Life title `Secretary General · BDI` → use a comma: `Secretary General, BDI`

---

## 7. Triple-dash (---) Rule

`---` (triple hyphen, rendered as an em-dash in LaTeX) is a **structural visual separator** between two labeled elements of equal weight. It has exactly **three** permitted uses:

| Context | Example |
|---|---|
| Project title: type of work ` --- ` client | `Mission de conseil --- **Air Liquide**` |
| Project subtitle: framework ` --- ` domain qualifier | `Projet ST7, CentraleSupélec --- chaîne logistique hydrogène` |
| Education bullet: score ` --- ` ranking | `Moyenne **8,7/10 --- top 2 %** sur 311 étudiants` |

**Banned everywhere else:** bullet body prose, profile/summary text, skills section, headlines, experience titles, and all other running text. Use a comma or rephrase.

Examples:
- Wrong in prose: `Analyse des flux --- identification des goulots.` → use a comma
- Wrong in experience title: `Stage en Génie Civil --- Bucarest 54` → use `\cventry{Bucarest 54}{Stage en Génie Civil}{...}` (the LaTeX command handles the visual separation)
- Correct in project title: `H₂ Supply Chain Optimisation --- **Air Liquide**`
- Correct in education bullet: `Average **8.7/10 --- top 2 %** of 311 students.`

**Date separator is `--` (double hyphen), not `---`.** Never use triple hyphen for date ranges.

---

## 8. Specificity Standard

Every bullet must answer: **what was done, for whom, at what scale?**

- Name the client when possible: not "a company" but `Air Liquide`, `Veganic`
- Name the methodology when it is the differentiator: not "an optimisation model" but `MILP multi-objective model`
- Name the type of contact when it signals seniority: `direct client contact`, `two-level interviews`, `executive jury`
- Name the campus or institution when it contextualizes scale: `CentraleSupélec associations`, `Secretaries-General of the campus`

A bullet without a figure is acceptable; a bullet without specificity is not.

---

## 9. Headline Fixed Structure

`[Professional title] specialized in [specialization]`

One clause. No school names. No `·` separators. No composite lists.

Example: `Industrial Engineer specialized in Operations Research & Risk Analytics`

---

## 10. Hobbies Inclusion Criteria

Include the Hobbies / Centres d'intérêt section only if:
- (a) The hobby directly connects to the role or company culture, or
- (b) The CV does not fill the page.

Format: specific and verifiable — not a generic list.

Wrong: `Hobbies: music, travel, sport.`
Correct: `Competitive sailing, regional ranking. Open-source contributor (github.com/…).`

---

## 11. Bullet Length and Line-Wrap Guidelines

The left column is narrow (approximately 35% of A4 width). Bullets placed in sections that render in the left column (Experience, Student Life) are especially susceptible to unwanted line wrapping.

- Keep bullets at ≤ 12 words where possible. 18 words is the absolute hard limit.
- Avoid long compound noun phrases — they are harder to scan and more likely to wrap.
- Tools and scale go in parentheses at the end, not mid-sentence — this keeps the main clause short.
- When a bullet overflows the column width, trim it first (R7) before adjusting column ratios or font sizes.

---

## 12. Trim Signals

Rewrite a bullet immediately if any of the following appear:

| Signal | Action |
|---|---|
| Em-dash in bullet prose (`—` or `---`) | Remove — replace with comma or rephrase. `---` is only permitted in project titles, project subtitles, and education score/ranking bullets. |
| Middle-dot (`·`) in titles, headline, or bullet prose | Replace with a comma. |
| "through" or "via" followed by another long phrase | Cut after the verb + object. |
| Two parenthetical groups in one bullet | Keep only the more informative one. |
| Statistical or configuration details in prose (replicas, confidence intervals, warmup periods, parameter names) | Cut from prose; name the method in parentheses only: `(Simul8)` not `(Simul8, 4 replicas, 95% CI)`. |
| Technical jargon with no managerial value (internal parameter names, solver settings, data structure details) | Cut entirely unless they signal a rare, named competence. |
| Problem input dimensions (geographic extents, exact raw volumes, distances) | Cut. State the conclusion, not the geometry. Wrong: `for 5 stations across 50–200 km` → Correct: `for 5 delivery stations`. |
| Connectors: "as well as", "while", "in order to", "allowing", and French equivalents | Remove and split or rephrase. |

---

## 13. Bullet Structure Default (Full Reminder)

**Default ordering:** method or approach → scope or scale → outcome.

Override the default only when the outcome is the single most striking fact (prize, record metric) — in that case, lead with it.

**Wrong (method leads when result is more impressive):**
```
Designed a proprietary dispatch heuristic (Simul8).
Won a zero-stockout fleet recommendation accepted by Air Liquide.
```

**Correct (result leads):**
```
Won a **zero-stockout** fleet recommendation accepted by Air Liquide.
Designed a proprietary dispatch heuristic with a multi-component cost function (Simul8).
```

---

## 14. Vocabulary — Banned and Required

### 14.1 Banned opening forms

- Noun phrase: `Design of a tool…`, `Conception d'un outil…`
- Infinitive: `Develop a tool…`, `Développer un outil…`
- Gerund: `Developing…`, `Développant…`
- Explicit subject: `I developed…`, `J'ai développé…`
- Implicit first person: `In charge of…`, `Responsible for…`, `En charge de…`

### 14.2 Banned connectors

`as well as` · `while` · `in order to` · `allowing` · `ainsi que` · `tout en` · `afin de` · `permettant de` · `qui a permis de`

### 14.3 Globally banned vocabulary

`passionate about` · `results-driven` · `team player` · `leveraged` · `spearheaded` · `synergy` · `go-getter` · `in order to` · `so as to`

### 14.4 Banned grandiose verbs

`orchestrated` · `championed` · `catalyzed` · `pioneered` · `evangelized` · `galvanized` · `drove` (as in "drove impact") · `secured commitment`

### 14.5 Banned adjectives

`strategic` · `transversal` · `complex` · `high-impact` · `ambitious` · `challenging` · `innovative` · `dynamic` · `rigorous` · `autonomous` · `significant` · `notable` · `key` · `critical` · `solid`

---

## 15. Summary (Profile) Section Rules

Three sentences, fixed structure:

**S1 — Identity:** Current school, specialization, expected graduation, role sought and start date. No double-degree mentions, no exchange semesters, no average/ranking.

**S2 — Track record:** `"My background combines [quantitative skill] and [delivery skill]: [example 1 with named client], [example 2 with quantified outcome], and [example 3 with award/result]."` Named clients and verified results are mandatory. Hackathon wins and client names belong here.

**S3 — Aspiration:** Frame as a contribution: `"Seeking to bring this dual competence to [target domain]."` Never a personal want.

**Summary-specific rules:**
- No first-person "I" — use "My background" (possessive is acceptable)
- No em-dashes
- No `·`
- Parentheses allowed in S2 for technical clusters: `(combinatorial optimisation, simulation)`
- Colon in S2 follows the language typography rule (section 5)

---

## 16. Date Format

All dates: `[mon. YYYY] -- [mon. YYYY]`

| Language | Month abbreviations |
|---|---|
| French | `janv.` `févr.` `mars` `avr.` `mai` `juin` `juil.` `août` `sept.` `oct.` `nov.` `déc.` |
| English | `Jan.` `Feb.` `Mar.` `Apr.` `May` `June` `July` `Aug.` `Sept.` `Oct.` `Nov.` `Dec.` |

- Separator: `--` (double hyphen). Never `-` (single) or `---` (triple).
- Single-month entry: `avr. 2026` (no separator).
- Ongoing: `oct. 2025 -- en cours` (FR) / `Oct. 2025 -- present` (EN).

---

## 17. Bullet Self-Check (Per Bullet)

Before writing the next bullet, verify the current one passes all nine rules:

1. Opens with a past-tense verb (EN) or deverbal noun (FR)? No subject, no noun phrase?
2. ≤ 12 words? If not, can it be cut without losing meaning?
3. No em-dash in prose? No `---` except in project titles and education score/ranking? No "through/via" padding? No double parenthetical?
4. Tools and scales in parentheses, not prose?
5. Bold only on a hard metric or named methodology? ≤ 2 bold per bullet?
6. Free of banned adjectives, connectors, and grandiose verbs?
7. Exactly one action?
8. Would a non-specialist hiring manager find this useful? No implementation minutiae. (R8)
9. If this is the first bullet: is the purpose and beneficiary of the project/task immediately clear? (R9)

**Per entry (after writing all bullets):**
10. Is the most impressive bullet first?

Any failure → rewrite before moving on.

---

## 18. French Deverbal Noun Reference

When the CV is in French, replace the English past-tense verb with its deverbal noun.

| Past participle | Deverbal noun |
|---|---|
| Conçu / Développé | Conception de / Développement de |
| Recommandé | Recommandation de |
| Réduit | Réduction de |
| Identifié | Identification de |
| Proposé | Proposition de |
| Modélisé | Modélisation de |
| Formulé | Formulation de |
| Diagnostiqué | Diagnostic de |
| Géré | Gestion de |
| Piloté | Pilotage de |
| Coordonné | Coordination de |
| Obtenu | Obtention de |
| Déterminé | Détermination de |
| Élaboré | Élaboration de |
| Comparé | Comparaison de |
| Conduit | Conduite de |
| Identifié | Identification de |
| Réalisé | Réalisation de |

Wrong: `Conçu un algorithme de dispatch propriétaire (Simul8).`
Correct: `Conception d'un algorithme de dispatch propriétaire (Simul8).`

All other rules (R1–R9, trim signals, self-check) apply equally in French.

---

## 19. Project Entry Titles and Subtitles

### 19.1 Title pattern

`[Type of deliverable] [description] --- [**Client**]`

- Always begins with the **type of work**, never the client name.
- Client comes after ` --- ` and is **bold** if it is a well-known company (CAC 40, Fortune 500, named institution). If not well-known, no bold.
- If there is no external client, omit ` --- ` entirely.

Canonical title openers: `Application de` / `Mission de conseil en` / `Étude de faisabilité` / `Stratégie de lancement` / `Mission d'amélioration` / `Diagnostic` / `Simulation` / `Analyse` / `Modélisation`

Wrong: `H₂ Supply Chain Optimisation · Air Liquide` — wrong separator
Wrong: `Air Liquide · H₂ Supply Chain Optimisation` — client must not lead
Correct: `H₂ Supply Chain Optimisation --- **Air Liquide**` — type first, ` --- `, bold client
Correct: `Staff Planning Optimisation App` — internal project, no ` --- `

Title character limit: ≤ 45 characters (right-column layout constraint).

### 19.2 Subtitle pattern

`[Academic framework or organizational context] --- [Domain qualifier]`

- Framework or origin organization goes first.
- ` --- ` separates context from domain qualifier.
- **Do NOT list tools in the subtitle.** Tools belong exclusively in bullet points.
- No team composition in the subtitle.
- Never include the country of execution in either title or subtitle.

Examples:
- `Pôle Projet Associatif Numérique, CentraleSupélec` (no domain qualifier needed)
- `Projet ST7, CentraleSupélec --- chaîne logistique hydrogène`
- `Programme Akademia, Fondation Innovation Bankinter --- Hackathon national`

Wrong: `Self-initiated · Solo · MILP · CentraleSupélec Associations` — tools in subtitle, wrong separator

---

## 20. Skills Section Rules

- Omit soft skills entirely.
- Sort items within each row by relevance to the most common target role.
- Maximum 5 rows.
