# Skill — CV Formatting and Content in LaTeX

---

<pipeline>

## Pipeline

1. Read `config/cv.md` as source of truth
2. Read `config/profile.yml` for candidate identity and contact info
3. Ask the user for the JD if not already in context (text or URL)
4. Extract 15-20 keywords from the JD
5. Detect JD language → CV language (EN default, FR is usual also)
6. Detect role archetype → adapt framing
7. Rewrite Professional Summary injecting JD keywords (NEVER invent skills — see Keyword Injection section)
8. Select top 3-4 most relevant projects for the offer, ordered by relevance to the JD
9. Reorder experience bullets by JD relevance
10. Inject keywords naturally into existing achievements
11. Generate the `.tex` file following the preamble in §3 and the section structure in §8
12. Write to `output/cv-{candidate}-{company}-{YYYY-MM-DD}.tex`
13. Run: `node generate-pdf.mjs output/cv-{candidate}-{company}-{YYYY-MM-DD}.tex output/cv-{candidate}-{company}-{YYYY-MM-DD}.pdf`
14. Report: .tex path, .pdf path, file sizes, section count, keyword coverage %

**Requires:** `tectonic` (preferred — `brew install tectonic`, auto-downloads packages) or `pdflatex` (MiKTeX / TeX Live) on PATH.

</pipeline>

---

<global-format>

## 1. Global Format Rules

- R1: 1 A4 page mandatory. If it overflows → see §10 (adjustment).
- R2: Never reduce font size below 10pt or margins below 1 cm top/bottom, 1.2 cm left/right.
- R3: No photo. Do not add a photo or space for one.
- R4: CV language = language of the job posting.
- R5: Every LaTeX file must include `\pdfgentounicode=1` in the preamble (after `\pagestyle{empty}`). Non-negotiable.

</global-format>

---

<ats-rules>

## 1b. ATS Rules

- Two-column layout via `paracol` — ATS parsers read left column first, then right; order sections accordingly
- Section headers must use standard names in the language of the posting (e.g. "Formation", "Expérience Professionnelle" in FR)
- Keywords distributed: first bullet of each experience entry + skills section
- No images, no graphics, no color in body text — color is only in structural elements (section rules, bullet markers)
- UTF-8 enforced by `[utf8]{inputenc}` + `[T1]{fontenc}`

</ats-rules>

---

<color-palettes>

## 2. Color Palettes

Only one active (uncommented in the LaTeX `COLORS` block); the other three commented out. Choose based on sector and tone of the job posting.

| # | Name | `primary` | `accent` | When to use |
|---|---|---|---|---|
| 1 | Corporate Blue | `#1F3A5F` | `#2E86AB` | Large industrial, aerospace, automotive, energy companies. |
| 2 | Teal / Forest Green | `#1A5F7A` | `#2E7D6B` | Sustainability, renewables, environmental engineering. |
| 3 | Burgundy / Wine | `#6B2D3E` | `#9B3A52` | Strategic consulting, banking, management. |
| 4 | Slate + Copper | `#3C3C3C` | `#B87333` | Tech, product, startups, data/AI profiles. |

`muted` and `bodytext` → `#555555` fixed across all palettes. Do not modify.

</color-palettes>

---

<preamble>

## 3. LaTeX Preamble

The preamble defines all packages, colors, commands and environments. Copy it in full; only modify the active palette.

```latex
\documentclass[a4paper,10pt]{article}

%==================== PACKAGES ============================
\usepackage[utf8]{inputenc}
\usepackage[T1]{fontenc}
\usepackage{lmodern}
\usepackage[a4paper,top=1cm,bottom=1cm,left=1.2cm,right=1.2cm]{geometry}
\usepackage{paracol}       % independent columns
\usepackage{xcolor}
\usepackage{fontawesome5}  % contact icons
\usepackage[hidelinks]{hyperref}
\usepackage{titlesec}      % section formatting
\usepackage{enumitem}      % list control
\usepackage{parskip}
\usepackage{ragged2e}
\usepackage{microtype}
\usepackage{xurl}

%==================== COLORS =============================
% Uncomment ONE palette and comment out the other three:

% Palette 1 — Corporate Blue (default)
\definecolor{primary}{HTML}{1F3A5F}
\definecolor{accent}{HTML}{2E86AB}

% Palette 2 — Teal / Forest Green
% \definecolor{primary}{HTML}{1A5F7A}
% \definecolor{accent}{HTML}{2E7D6B}

% Palette 3 — Burgundy / Wine
% \definecolor{primary}{HTML}{6B2D3E}
% \definecolor{accent}{HTML}{9B3A52}

% Palette 4 — Slate + Copper
% \definecolor{primary}{HTML}{3C3C3C}
% \definecolor{accent}{HTML}{B87333}

% Fixed — do not touch:
\definecolor{muted}{HTML}{555555}
\definecolor{bodytext}{HTML}{555555}

%==================== HYPERLINKS =========================
\hypersetup{colorlinks=true, urlcolor=accent, linkcolor=accent}

%==================== PAGE ================================
\pagestyle{empty}
\pdfgentounicode=1     % mandatory — ensures PDF text layer is Unicode-searchable
\setlength{\columnsep}{0.7cm}

%==================== SECTIONS ===========================
\titleformat{\section}
    {\large\bfseries\color{primary}}
    {}{0em}{}[\vspace{-2pt}{\color{accent}\titlerule[0.8pt]}]
\titlespacing*{\section}{0pt}{6pt}{3pt}

%==================== COMMANDS ===========================

% Document header
\newcommand{\cvname}[1]{{\Huge\bfseries\color{primary} #1}}
\newcommand{\cvheadline}[1]{{\Large\color{muted} #1}}

% \cventry — 2-line header for ALL sections with dates
%   #1 name/title           → line 1 left, bold
%   #2 subtitle/role        → line 2 left, italic | pass {} if empty
%   #3 dates                → line 1 right, muted color
%   #4 location             → line 2 right, muted color | ALWAYS present
\newcommand{\cventry}[4]{%
    \noindent{\normalsize\textbf{#1}}\hfill\textcolor{muted}{\small #3}\\%
    {\small\itshape #2}\hfill\textcolor{muted}{\small #4}\par\vspace{-4pt}%
}

% \cvskill — skill row: label : values
\newcommand{\cvskill}[2]{\noindent\textbf{#1~:} {\small #2}\par\vspace{1pt}}

%==================== LANGUAGES ENVIRONMENT ==============
% \lang{Language}{level --- certificate}
\newenvironment{cvlanguages}{%
    \begin{list}{}{%
        \setlength{\leftmargin}{0pt}\setlength{\rightmargin}{0pt}%
        \setlength{\topsep}{0pt}\setlength{\partopsep}{0pt}%
        \setlength{\itemsep}{0pt}\setlength{\parsep}{0pt}%
    }\small\color{bodytext}}{\end{list}}
\newcommand{\lang}[2]{\item \textbf{#1~:} #2}

%==================== BULLET ENVIRONMENTS ================
%
% Each section uses its own environment with a distinct visual marker.
% They are NOT interchangeable.
%
%   cvbulletsformation  → Formation              ►  (faAngleRight)
%   cvbulletsexp        → Expérience             ►  (faAngleRight)
%   cvbulletsassociatif → Vie Étudiante          ►  (faAngleRight)
%   cvbullets           → Projets & Innovation   ►  (faAngleRight)

\newenvironment{cvbulletsformation}{%
    \begin{itemize}[leftmargin=12pt,topsep=0pt,itemsep=0pt,parsep=0pt,
        label=\color{accent}\scriptsize\faAngleRight]\small\color{bodytext}}
    {\end{itemize}}

\newenvironment{cvbulletsexp}{%
    \begin{itemize}[leftmargin=12pt,topsep=0pt,itemsep=0pt,parsep=0pt,
        label=\color{accent}\scriptsize\faAngleRight]\small\color{bodytext}}
    {\end{itemize}}

\newenvironment{cvbulletsassociatif}{%
    \begin{itemize}[leftmargin=12pt,topsep=0pt,itemsep=0pt,parsep=0pt,
        label=\color{accent}\scriptsize\faAngleRight]\small\color{bodytext}}
    {\end{itemize}}

\newenvironment{cvbullets}{%
    \begin{itemize}[leftmargin=12pt,topsep=0pt,itemsep=0pt,parsep=0pt,
        label=\color{accent}\scriptsize\faAngleRight]\small\color{bodytext}}
    {\end{itemize}}
```

### 3b. Document Assembly Skeleton

After the preamble, the document body follows this fixed structure. Do not alter the column split or the order of sections.

```latex
\begin{document}

%==================== HEADER (full width, above columns) ====================
% \cvname, \cvheadline, \hrule, \section*{Profil}  → see §8.1
% \vspace{2pt}

%==================== TWO-COLUMN BODY =======================================
\columnratio{0.33}   % default — adjust only per §10
\begin{paracol}{2}

%---------- LEFT COLUMN (33 %) ----------
% \section*{Contact}        → §8.2
% \section*{Langues}        → §8.3
% \section*{Expérience Professionnelle} → §8.4
% \section*{Vie Étudiante}  → §8.5
% \section*{Compétences}    → §8.6

\switchcolumn

%---------- RIGHT COLUMN (67 %) ----------
% \section*{Formation}             → §8.7
% \section*{Projets \& Innovation} → §8.8
% \section*{Centres d'intérêt}     → §8.9 (optional)

\end{paracol}

\end{document}
```

> `\columnratio` sets the left-column fraction. Default is `0.33`. Adjust only when the CV overflows (§10); never go below `0.28` or above `0.38`.

</preamble>

---

<latex-escaping>

## 3c. LaTeX Escaping (CRITICAL)

All user-supplied text content must be escaped before insertion into the `.tex` file:

| Character | Escape |
|-----------|--------|
| `&` | `\&` |
| `%` | `\%` |
| `$` | `\$` |
| `#` | `\#` |
| `_` | `\_` |
| `{` | `\{` |
| `}` | `\}` |
| `~` | `\textasciitilde{}` |
| `^` | `\textasciicircum{}` |
| `\` | `\textbackslash{}` |
| `±` | `$\pm$` |
| `→` | `$\rightarrow$` |

**Exception:** Do NOT escape LaTeX commands (`\cventry`, `\textbf`, etc.) — only user-supplied text content.

**Exception for URLs:** Do NOT escape inside `\href{URL}{...}` first argument. Only escape the display text (second argument):
```latex
\href{https://example.com/path_with_underscores}{Example\_Display}
```

</latex-escaping>

---

<general-writing>

## 4. General Writing Rules

### 4.1 Globally banned vocabulary

| Category | Banned |
|---|---|
| Clichés | passionate about, results-driven, team player, detail-oriented, go-getter, synergy, leverage(d), spearheaded |
| Filler phrases | "in order to", "so as to", "with the aim of" and equivalents in FR/ES |
| Empty adjectives | see §5.5 |

---

### 4.2 Tone by application type

IF **alternance** → emphasize fast learning, applied academic projects, any company experience however brief.  
IF **junior CDI** → emphasize concrete output and responsibilities taken on from day one.

---

### 4.3 Tone by sector

| Sector | Use | Avoid |
|---|---|---|
| Consulting / banking | résultats, impact, livrables, portefeuille, gouvernance | sprint, MVP, iterate |
| Startup / tech | shipped, deployed, built, stack | synergy, leverage, stakeholders |
| Industry / engineering | Lean, Six Sigma, OEE, takt time, flux | agile, go-getter, disruption |

---

### 4.4 Acronyms

- R1: Only acronyms that appear in the job posting or are universally known (KPI, IT, B2B, ROI).
- R2: If it does not meet R1 → write the full term.

</general-writing>

---

<keyword-injection>

## 4b. Keyword Injection Strategy

- NEVER add skills the candidate doesn't have
- Only reformulate existing experience using JD vocabulary
- Examples:
  - JD says "gestion de projet" → reword "coordination d'équipe" to "gestion de projet transverse"
  - JD says "data-driven" → reword "analyse des résultats" to "décision data-driven"
  - JD says "supply chain" → reword "flux logistiques" to "chaîne logistique"
- Inject into: first bullet of each experience entry, profile sentence 2, skills section
- Target: ≥60% of JD's top 15 keywords present in the final CV

</keyword-injection>

---

<bullet-rules>

## 5. Bullet Rules

> Apply to Expérience Professionnelle, Vie Étudiante and Projets & Innovation. Formation has its own rules (§8.7).

### 5.1 Structure

Pattern: `[Action noun] + de/d' [object] + [avec|sur|pour|:] [specification] + (means/scale)`

- R1: Single sentence. Maximum 18 words / 120 characters. Ends with a period.
- R2: The action noun always comes first, without an article.
- R3: Tools, scales and KPIs always go in parentheses at the end, separated by commas, maximum 4 elements. Never in the prose.
- R4 (R1 vs R3 conflict): if parentheses push over 18 words → merge or drop the least relevant element. Never move tools into the prose.

✗ `J'ai développé un outil Python pour automatiser le traitement des données de la chaîne logistique.`  
✗ `Développement d'un outil Python permettant d'automatiser le traitement des données logistiques.`  
✓ `Développement d'un outil d'automatisation du traitement des données logistiques (Python, pandas).`

---

### 5.2 Opening word

**Allowed:** deverbal noun.

| Family | Examples |
|---|---|
| Design / creation | Conception, Développement, Modélisation, Réalisation, Production, Rédaction |
| Improvement / change | Optimisation, Refonte, Mise en place, Déploiement, Intégration, Lancement |
| Analysis | Analyse, Audit, Diagnostic, Étude, Recherche, Synthèse |
| Management | Pilotage, Gestion, Suivi, Coordination, Encadrement, Animation, Cadrage |
| Participation | Participation à, Contribution à |

**Banned:**

| Banned | Example |
|---|---|
| Conjugated verb | `J'ai développé…` |
| Infinitive | `Développer un outil…` |
| Gerund | `Développant un outil…` |
| Implicit first person | `Responsable de…`, `En charge de…` |
| Opening adjective | `Forte implication dans…` |

✗ `J'ai coordonné une équipe de 5 personnes pour le lancement du projet.`  
✓ `Coordination d'une équipe projet de 5 personnes pour le lancement (Jira, Slack).`

---

### 5.3 One action per bullet

- R1: One bullet = one action. If there are two → split into two bullets.
- R2: "et" only joins two parallel objects of the same action noun.
- R3: Banned: `ainsi que`, `tout en`, `afin de`, `permettant de`, `qui a permis de`, relative clauses with `qui`.

✗ `Optimisation des stocks ainsi que mise en place d'un tableau de bord de suivi.`  
✓ `Optimisation des niveaux de stock (ABC-XYZ, SAP).`  
✓ `Mise en place d'un tableau de bord de suivi des flux (Power BI).`

✓ Correct "et" (two objects, same action): `Audit des processus B2B et B2C de la filiale espagnole.`

---

### 5.4 Bold

- R1: Maximum 2 bold elements per bullet.
- R2: Bold only on: (a) domain asset (methodology, regulatory framework, core platform), (b) hard verifiable metric (figure, %, €, volume, ranking), (c) technology explicitly named in the job posting.
- R3: Never bold: the action noun, adjectives, verbs, connectors.

✗ `**Optimisation** des stocks de 15 %` → ✓ `Optimisation des stocks de **15 %**`  
✗ `Analyse **détaillée** des flux` → ✓ `Analyse des flux (**Value Stream Mapping**)`  
✗ `**Développement** d'un **outil Python**` → ✓ `Développement d'un outil de simulation (**Python**, **AnyLogic**)`

---

### 5.5 Banned adjectives

| Banned |
|---|
| stratégique, transversal, structurant, ambitieux |
| complexe, à fort enjeu, à forte valeur ajoutée, à fort impact |
| challenging, clé, critique, sensible, exigeant |
| solide, robuste, innovant, dynamique, motivé, rigoureux, autonome |
| significatif, important, notable |
| avec succès, efficacement, notamment, particulièrement |

✗ `Pilotage d'un projet stratégique et complexe à fort enjeu.`  
✓ `Pilotage d'un projet de refonte du système de planification (6 mois, 3 sites).`

---

### 5.6 Metrics

- R1: If a real figure exists → include it in bold.
- R2: No real figure → do not invent one. A bullet without a metric is better than a vague metric.
- R3: Banned: "amélioration significative", "fort impact", "gains importants".

✗ `Optimisation des délais de livraison avec des gains importants.`  
✓ `Optimisation des délais de livraison de **3 jours** sur le périmètre Europe.`  
✓ `Optimisation des délais de livraison sur le périmètre Europe (ABC-XYZ, SAP).`

---

<self-check id="bullets">

### ✅ Bullet checklist

1. Does it open with a deverbal noun (§5.2)?
2. Is it a single sentence ≤ 18 words?
3. Are tools and scales in parentheses, not in the prose?
4. Is bold used only on a hard metric, domain asset or tool from the posting?
5. Does it contain any adjective from the banned list §5.5?
6. Is there more than one action, or any banned connector from §5.3?

Any failure → rewrite before moving on.

</self-check>

</bullet-rules>

---

<entry-headers>

## 6. Entry Headers (`\cventry`)

Applies to all sections with dates: Expérience, Vie Étudiante, Projets & Innovation and Formation. No exceptions.

`\cventry{#1}{#2}{#3}{#4}` — 4 mandatory arguments:

- `#1` name/title of the entry (bold).
- `#2` clarification: role, client, degree, etc. If line 1 already says it all → `{}`.
- `#3` dates (see §7.1).
- `#4` location. **Always present**, never empty.

Rules:
- R1: Location always goes in `#4`. Never inside `#1` or `#3`.
- R2: Pass `{}` in `#2` if empty; never omit the argument.
- R3: Formation: institution → `#1`, degree/parcours → `#2`, city → `#4`.

✗
```latex
\cventry{Renault — Mission Supply Chain · Boulogne-Billancourt}
        {Stagiaire Planification}
        {avr. 2024 -- sept. 2024}
```
✓
```latex
\cventry{Renault --- Mission Supply Chain}
        {Stagiaire Planification, Direction Logistique}
        {avr. 2024 -- sept. 2024}
        {Boulogne-Billancourt, France}
```

</entry-headers>

---

<dates-and-dashes>

## 7. Dates and Em-dashes

### 7.1 Dates

LaTeX format: `{sept. 2023 -- juin 2024}`

- Month in lowercase abbreviated form.
- Separator: `--` (en-dash). Not `-` or `---`.
- Ongoing: `{déc. 2024 -- en cours}`

✗ `{09/2023 - 06/2024}` · `{Septembre 2023 -- Juin 2024}` · `{sept. 2023 --- juin 2024}`  
✓ `{sept. 2023 -- juin 2024}`

---

### 7.2 Em-dashes

- R1: Allowed only in `#1` and `#2` of `\cventry`.
- R2: Banned in bullets, Profil and any paragraph.

✓ `\cventry{EXEO --- Stage Ingénierie Logistique}{...}{...}{...}`  
✗ bullet: `Analyse des flux --- identification des goulots --- proposition de solutions.`

</dates-and-dashes>

---

<sections>

## 8. CV Sections

Sections are distributed across two columns:

- **Left column (33%):** Contact · Langues · Expérience Professionnelle · Vie Étudiante · Compétences
- **Right column (67%):** Formation · Projets & Innovation

---

### 8.1 Header and Profil

The headline reflects the target job title from the posting, not a generic title.

The Profil has exactly 3 sentences:
1. Who you are + institution + graduation year.
2. 1-2 differentiators aligned with the posting (do not need to repeat CV experiences).
3. Type of contract sought + area.

Additional rules: first person · no "I am a passionate…" or equivalents · no specific projects · no em-dashes.

```latex
\begin{center}
    \cvname{Óscar Martínez Zamora}\\[3pt]
    \cvheadline{Élève-ingénieur orienté Conseil en Opérations}
\end{center}
\vspace{2pt}{\color{accent}\hrule height 0.8pt}\vspace{4pt}

\section*{Profil}
\small
Élève-ingénieur en double diplôme CentraleSupélec x UPV, spécialisation
\emph{Operations Research and Risk Analytics}, en recherche d'une alternance
en conseil opérationnel à partir de septembre 2026.
Mon parcours associe modélisation quantitative (optimisation combinatoire,
simulation stochastique) et conduite de missions client réelles :
dimensionnement de flotte pour Air Liquide, diagnostic d'une PME industrielle
et lancement \emph{go-to-market} primé.
Je cherche à mettre cette double compétence au service de projets de
transformation des opérations et de la supply chain.

\vspace{2pt}
```

---

### 8.2 Contact

```latex
\section*{Contact}
\footnotesize
\faMapMarker* \ Gif-sur-Yvette, France\\[2pt]
\faPhone \ +33 7 65 50 64 85\\[2pt]
\faEnvelope \ \href{mailto:oscar.martinez-zamora@student-cs.fr}
    {oscar.martinez-zamora@student-cs.fr}\\[2pt]
\faLinkedin \ \href{https://www.linkedin.com/in/oscarmartinezzamora/}
    {oscarmartinezzamora}
```

---

### 8.3 Langues

```latex
\section*{Langues}
\begin{cvlanguages}
    \lang{Espagnol}{natif}
    \lang{Anglais}{C1 --- Cambridge Advanced}
    \lang{Français}{B2 --- DELF}
    \lang{Allemand}{A2}
\end{cvlanguages}
```

---

### 8.4 Expérience Professionnelle

Environment: `cvbulletsexp` (►). Maximum 3 bullets per entry. Reverse chronological order.

```latex
\section*{Expérience Professionnelle}

\cventry{Stage en bureau d'études en Génie Civil --- Bucarest 54}
        {Ingénieur stagiaire, département conception}
        {juin -- août 2023}
        {Murcia, Espagne}
\begin{cvbulletsexp}
    \item{} Conception des installations électriques, plomberie et CVC
            d'un bâtiment résidentiel (CYPE).
\end{cvbulletsexp}
```

---

### 8.5 Vie Étudiante

Environment: `cvbulletsassociatif` (►). Add `\vspace{4pt}` between entries.

```latex
\section*{Vie Étudiante}

\cventry{Secrétaire Général}
        {Bureau de l'International (BDI), CentraleSupélec}
        {fév. 2026 -- fév. 2027}
        {Gif-sur-Yvette, France}
\begin{cvbulletsassociatif}
    \item{} Coordination d'une association de \textbf{35 membres} dédiée
            à l'intégration des étudiants internationaux.
    \item{} Pilotage du staffing événementiel (semaine d'intégration,
            World Week, \textbf{200 participants}).
    \item{} Gestion des partenariats corporate du Pôle Relation Entreprises
            (\textbf{McKinsey} Paris et Casablanca, \textbf{Société Générale}).
\end{cvbulletsassociatif}\vspace{4pt}

\cventry{Trésorier}
        {Club Espagnol de CentraleSupélec}
        {fév. 2026 -- fév. 2027}
        {Gif-sur-Yvette, France}
\begin{cvbulletsassociatif}
    \item{} Gestion financière du club pour des événements culturels
            (budget annuel, justificatifs BDI).
    \item{} Négociation de subventions devant le Conseil d'Administration du BDI.
\end{cvbulletsassociatif}
```

---

### 8.6 Compétences

Three fixed categories in this order. Sort each list by relevance to the posting.

```latex
\section*{Compétences}
\cvskill{Logiciels}{Python, SQL, Gurobi, Simul8, Power BI, Excel avancé,
                    MATLAB, \mbox{LaTeX}}
\cvskill{Méthodes}{MILP, analyse multicritère, modélisation financière
                   (VAN, payback), développement assisté par IA}
\cvskill{Soft skills}{leadership et coordination d'équipe, gestion de
                      parties prenantes multiculturelles,
                      communication synthétique}
```

---

### 8.7 Formation

Environment: `cvbulletsformation` (►). Reverse chronological order.

Bullets in this priority order:
1. Specialisation, mention or programme focus — only if not already in `#2` of `\cventry`.
2. Quantitative achievement: GPA, ranking position.
3. _(Optional)_ Award, scholarship, Erasmus or unique milestone.

```latex
\section*{Formation}

\cventry{Diplôme d'Ingénieur Généraliste --- CentraleSupélec}
        {Spécialisation Operations Research and Risk Analytics}
        {sept. 2025 -- avr. 2027}
        {Gif-sur-Yvette, France}
\begin{cvbulletsformation}
    \item{} \textbf{Premier étudiant} du Master Génie Industriel de l'UPV
            à intégrer ce programme d'échange.
\end{cvbulletsformation}

\cventry{Master en Ingénierie Industrielle --- UPV}
        {Mention spécialisation Opérations et Logistique}
        {sept. 2024 -- avr. 2027}
        {Valencia, Espagne}
\begin{cvbulletsformation}
    \item{} Moyenne \textbf{7,7/10 --- top 10\,\%} de la promotion.
\end{cvbulletsformation}

\cventry{Licence en Ingénierie Industrielle --- UPV}
        {}
        {sept. 2020 -- juil. 2024}
        {Valencia, Espagne}
\begin{cvbulletsformation}
    \item{} Moyenne \textbf{8,7/10 --- top 2\,\%} sur 311 étudiants.
    \item{} \textbf{25\,\%} des crédits validés avec Mention Très Bien ·
            Prix de Haute Performance Académique.
    \item{} Semestre Erasmus à Politechnika Krakowska (Cracovie).
\end{cvbulletsformation}
```

---

### 8.8 Projets & Innovation

Environment: `cvbullets` (►). Order by relevance to the posting, not by date.

Titles: what the project was, not who funded it.
- R1: IF there is a client → they may appear at the end of the title after `---`, never at the beginning.

✗ `Air Liquide --- Optimisation de planning`  
✓ `Optimisation de planning de production --- projet industriel chez Air Liquide`

```latex
\section*{Projets \& Innovation}

\cventry{Application d'optimisation du \emph{staff planning}}
        {Pôle Projet Associatif Numérique CentraleSupélec --- Python, Gurobi}
        {déc. 2024 -- en cours}
        {Gif-sur-Yvette, France}
\begin{cvbullets}
    \item{} Conception d'un modèle \textbf{MILP} multi-objectif pour
            automatiser la planification des événements des associations.
    \item{} Conduite de \textbf{20+} entretiens utilisateurs auprès des
            Secrétaires Généraux du campus et recueil des besoins.
    \item{} Réduction du temps de planification d'un \textbf{facteur >10}
            (de plusieurs heures à quelques minutes).
\end{cvbullets}

\cventry{Mission de conseil en opérations --- Air Liquide}
        {Projet ST7 CentraleSupélec --- chaîne logistique hydrogène}
        {janv. -- avr. 2026}
        {Gif-sur-Yvette, France}
\begin{cvbullets}
    \item{} Conception d'une heuristique de dispatch pour une flotte
            de remorques de gaz (Simul8).
    \item{} Modélisation de la chaîne logistique en simulation à
            événements discrets (validation white-box).
    \item{} Recommandation de dimensionnement de flotte sous contraintes
            de niveau de service et coût opérationnel.
\end{cvbullets}

\cventry{Étude de faisabilité du système Bokashi en France}
        {Pôle Projet S7 CentraleSupélec --- analyse multicritère}
        {sept. 2025 -- fév. 2026}
        {Gif-sur-Yvette, France}
\begin{cvbullets}
    \item{} Conduite d'une étude de faisabilité multicritère
            (légal, économique, environnemental, comportemental).
    \item{} Réalisation d'entretiens terrain et expérimentation empirique
            pour valider les hypothèses de déploiement.
\end{cvbullets}

\cventry{Stratégie de lancement d'un MVP B2C --- GENAQ}
        {Programme Akademia, Fondation Innovation Bankinter --- Hackathon national}
        {sept. 2024 -- fév. 2025}
        {Valencia, Espagne}
\begin{cvbullets}
    \item{} Pilotage d'une équipe de \textbf{6 personnes} sur une mission
            de conseil go-to-market (générateurs d'eau atmosphérique).
    \item{} Conception d'un modèle d'analyse multicritère pour déterminer
            les \textbf{50 meilleures régions} pour le lancement du MVP.
    \item{} Obtention du \textbf{1\textsuperscript{er} prix} parmi
            \textbf{80 équipes} face au jury exécutif de l'entreprise.
\end{cvbullets}

\cventry{Mission d'amélioration d'une PME industrielle --- Veganic}
        {Amélioration opérationnelle avec relation client directe, Espagne}
        {fév. -- juin 2025}
        {Valencia, Espagne}
\begin{cvbullets}
    \item{} Conduite d'un diagnostic opérationnel complet
            (visite terrain, cartographie des flux, entretiens à deux niveaux).
    \item{} Identification de \textbf{5 goulots} par cartographie des flux
            et analyse de l'espace de production.
    \item{} Modélisation \textbf{VAN} de l'investissement optimal :
            retour en \textbf{18 mois} et \textbf{+68\,\%} de productivité
            en palettisation.
\end{cvbullets}
```

---

### 8.9 Hobbies (optional section)

Include only if:
- (a) The hobby directly connects to the role or company culture, or
- (b) The CV does not fill the page.

✗ `Centres d'intérêt : musique, voyages, sport.`  
✓ `Voile compétitive — classé régional. Contributeur open source (github.com/…).`

</sections>

---

<final-check>

## 9. Final Verification

### 9.1 6-second scan test

The following 5 elements must be visible in 6 seconds without reading the CV:
name · headline · most recent education · most recent or most relevant experience/project · 2-3 keywords from the posting.

IF any of these is not immediately visible → reorganise the visual hierarchy.

---

<self-check id="full-cv">

### ✅ Full CV checklist

1. Does it fit on 1 A4 page without reducing font size or margins?
2. Do all bullets pass the bullet checklist §5?
3. Do all dates follow `{mon. YYYY -- mon. YYYY}`?
4. Do all `\cventry` commands have 4 arguments with location in `#4`?
5. Are there any em-dashes inside bullets or paragraphs? → remove.
6. Are the 5 elements of the scan test §9.1 immediately visible?
7. Is the active palette in the LaTeX the confirmed one? Are the other three commented out?
8. Does the file include `\pdfgentounicode=1` after `\pagestyle{empty}`?

</self-check>

</final-check>

---

<adjustment>

## 10. Fitting to 1 Page

IF the CV overflows → intervene in this order:

| Step | Action |
|---|---|
| 1 | Columns: right overflows → `\columnratio{0.30}`. Right too empty → `\columnratio{0.36}`. |
| 2 | Merge bullets: combine 2 with "et" or a comma; reduce from 3 to 2 bullets in less critical projects. |
| 3 | Shorten titles: if `#1` of `\cventry` clashes with the date → move context to `#2`. |
| 4 | Spacing: `\titlespacing*{\section}{0pt}{6pt}{3pt}` → `{5pt}{2pt}` · `\vspace{-4pt}` → `\vspace{-5pt}`. |
| 5 | Vie Étudiante: shorten or reduce from 3 to 2 entries. |

**Never:** font < 10pt · margins < 1 cm (top/bottom) or < 1.2 cm (left/right) · `\linespread`.

</adjustment>

