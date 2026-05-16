# Skill — CV Formatting and Content in LaTeX

---

<pipeline>

## Pipeline

1. Read `config/cv.md` as source of truth
2. Read the YAML frontmatter of `config/profile.md` (`candidate.*` and `location.*` keys) for candidate identity and contact info
3. Ask the user for the JD if not already in context (text or URL)
4. Extract 15-20 keywords from the JD
5. Detect JD language → CV language (EN default, FR is usual also)
6. Detect role archetype → adapt framing
7. Rewrite Professional Summary injecting JD keywords (NEVER invent skills — see Keyword Injection section)
8. Select top 3-4 most relevant projects for the offer, ordered by relevance to the JD
9. Reorder experience bullets by JD relevance
10. Inject keywords naturally into existing achievements
11. Generate the `.tex` file following the preamble in §3 and the section structure in §7
12. Write to `output/cv-{candidate}-{company}-{YYYY-MM-DD}.tex`
13. Run: `node generate-pdf.mjs output/cv-{candidate}-{company}-{YYYY-MM-DD}.tex output/cv-{candidate}-{company}-{YYYY-MM-DD}.pdf`
14. Report: .tex path, .pdf path, file sizes, section count, keyword coverage %

**Requires:** `tectonic` (preferred — `brew install tectonic`, auto-downloads packages) or `pdflatex` (MiKTeX / TeX Live) on PATH.

</pipeline>

---

<writing-conventions>

## Writing Conventions

All writing style rules (bullet ordering, bold, parentheses, colon, middle-dot, triple-dash, Specificity Standard, Headline, Hobbies, trim signals, vocabulary) are centralized in `modes/writing.md`. Read and apply that file before generating any output.

</writing-conventions>

---

<global-format>

## 1. Global Format Rules

- R1: 1 A4 page mandatory. If it overflows → see §9 (adjustment).
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
\documentclass[a4paper,11pt]{article}

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
\setlength{\emergencystretch}{3em}  % prevents overfull hbox in narrow column

%==================== SECTIONS ===========================
\titleformat{\section}
    {\large\bfseries\color{primary}}
    {}{0em}{}[\vspace{-2pt}{\color{accent}\titlerule[0.8pt]}]
\titlespacing*{\section}{0pt}{6pt}{3pt}

%==================== COMMANDS ===========================

% Document header
\newcommand{\cvname}[1]{{\Huge\bfseries\color{primary} #1}}
\newcommand{\cvheadline}[1]{{\Large\color{muted} #1}}

% \cventry — Expérience Professionnelle
%   #1 company              → line 1 left, bold UPPERCASE
%   #2 post/domain          → line 1 left, bold (after company)
%   #3 country              → line 2 right, muted small
%   #4 dates                → line 2 left, muted small
\newcommand{\cventry}[4]{%
    \noindent{\normalsize\textbf{\MakeUppercase{#1} · #2}}\\%
    \noindent{\small\textcolor{muted}{#4}}\hfill{\small\textcolor{muted}{#3}}\par\vspace{-4pt}%
}

% \cventryformation — Formation (4-arg)
%   #1 degree title         → line 1 left, bold
%   #2 institution          → line 2 left, italic
%   #3 dates                → line 1 right, muted
%   #4 country              → line 2 right, italic muted
\newcommand{\cventryformation}[4]{%
    \noindent{\normalsize\textbf{#1}}\hfill\textcolor{muted}{\small #3}\\%
    \noindent{\small\itshape #2}\hfill{\small\itshape\textcolor{muted}{#4}}\par\vspace{-4pt}%
}

% \cventryprojet — Projets & Innovation (3-arg, no location)
%   #1 project title        → line 1 left, bold
%   #2 context/subtitle     → line 2 left, italic
%   #3 dates                → line 1 right, muted
\newcommand{\cventryprojet}[3]{%
    \noindent{\normalsize\textbf{#1}}\hfill\textcolor{muted}{\small #3}\\%
    {\small\itshape #2}\par\vspace{-4pt}%
}

% \cventryassociatif — Vie Étudiante (2-arg, no location)
%   #1 role title           → line 1 left, bold
%   #2 dates                → line 2 left, muted (acts as subtitle)
\newcommand{\cventryassociatif}[2]{%
    \noindent{\normalsize\textbf{#1}}\\%
    {\small\textcolor{muted}{#2}}\par\vspace{-4pt}%
}

% \cventrycontest — Concours & Hackathons (3-arg, no location)
%   #1 contest/award title  → line 1 left, bold
%   #2 context/organizer    → line 2 left, italic
%   #3 dates                → line 1 right, muted
\newcommand{\cventrycontest}[3]{%
    \noindent{\normalsize\textbf{#1}}\hfill\textcolor{muted}{\small #3}\\%
    {\small\itshape #2}\par\vspace{-4pt}%
}

% \cvskill — skill row (use inside cvskillsblock): label : values
\newcommand{\cvskill}[2]{\item \textbf{#1~:} #2}

%==================== LANGUAGES ENVIRONMENT ==============
\newenvironment{cvlanguages}{%
    \begin{list}{}{%
        \setlength{\leftmargin}{0pt}\setlength{\rightmargin}{0pt}%
        \setlength{\topsep}{0pt}\setlength{\partopsep}{0pt}%
        \setlength{\itemsep}{0pt}\setlength{\parsep}{0pt}%
    }\small\color{bodytext}}{\end{list}}
\newcommand{\lang}[2]{\item \textbf{#1~:} #2}

%==================== SKILLS ENVIRONMENT =================
\newenvironment{cvskillsblock}{%
    \begin{list}{}{%
        \setlength{\leftmargin}{0pt}\setlength{\rightmargin}{0pt}%
        \setlength{\topsep}{0pt}\setlength{\partopsep}{0pt}%
        \setlength{\itemsep}{0pt}\setlength{\parsep}{0pt}%
    }\small\color{bodytext}}{\end{list}}

%==================== BULLET ENVIRONMENTS ================
%
% Each section uses its own environment with a distinct visual marker.
% They are NOT interchangeable.
%
%   cvbulletsformation  → Formation              ►  (faAngleRight)
%   cvbulletsexp        → Expérience             ►  (faAngleRight)
%   cvbulletsassociatif → Vie Étudiante          ►  (faAngleRight)
%   cvbullets           → Projets & Innovation   ►  (faAngleRight)
%   cvbulletscontest    → Concours & Hackathons  ►  (faAngleRight)

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

\newenvironment{cvbulletscontest}{%
    \begin{itemize}[leftmargin=12pt,topsep=0pt,itemsep=0pt,parsep=0pt,
        label=\color{accent}\scriptsize\faAngleRight]\small\color{bodytext}}
    {\end{itemize}}
```

### 3b. Document Assembly Skeleton

After the preamble, the document body follows this fixed structure. Do not alter the column split or the order of sections.

```latex
\begin{document}

%==================== HEADER (full width, above columns) ====================
% \cvname, \cvheadline, \hrule, \section*{Profil}  → see §7.1
% \vspace{2pt}

%==================== TWO-COLUMN BODY =======================================
\columnratio{0.35}   % default — adjust only per §9
\begin{paracol}{2}

%---------- LEFT COLUMN (35 %) ----------
\RaggedRight
% \section*{Contact}        → §7.2
% \section*{Langues}        → §7.3
% \section*{Expérience Professionnelle} → §7.4
% \section*{Vie Étudiante}  → §7.5
% \section*{Compétences}    → §7.6

\switchcolumn

%---------- RIGHT COLUMN (65 %) ----------
% \section*{Formation}             → §7.7
% \section*{Projets \& Innovation} → §7.8
% \section*{Concours \& Hackathons} → §7.9 (optional)
% \section*{Centres d'intérêt}     → §7.10 (optional)

\end{paracol}

\end{document}
```

> `\columnratio` sets the left-column fraction. Default is `0.35`. Adjust only when the CV overflows (§9); never go below `0.28` or above `0.40`.

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

### 3d. Entry Title Length Limit

`\cventryprojet` and `\cventrycontest` place `#1` and the date on the same line with `\hfill`. If `#1` is too long the date wraps — a visible layout defect.

**`\cventry` is exempt:** its new layout puts COMPANY · Post on line 1 alone (no `\hfill` with the date), so wrapping is acceptable. No char limit applies to `\cventry{#1}`.

**Hard limits for `\cventryprojet` and `\cventrycontest`:**

| Column | Approx. usable width | Max chars for `#1` |
|--------|---------------------|--------------------|
| Right (65%) | ~122 mm | **≤ 45 chars** |

If your title exceeds the limit → shorten `#1` and move the extra context into `#2`. Never reduce the font or the column ratio to work around long titles.

✗ `\cventryprojet{Analyse du bilan carbone de solutions de gestion des biodéchets}{...}{...}`
✓ `\cventryprojet{Analyse bilan carbone des biodéchets}{...}{...}`

---

### 3e. Known-Bad Commands (MiKTeX / pdflatex)

These commands from fontawesome5 or currency packages cause **undefined control sequence** errors on pdflatex without additional packages. Use the safe alternatives — never add extra packages to fix them:

| ❌ Do NOT use | ✅ Use instead | Reason |
|--------------|---------------|--------|
| `\euro{}` or `\euro` | `€` (UTF-8 directly) | Requires eurosym/textcomp — not in preamble; `[utf8]{inputenc}` handles `€` natively |
| `\faMapMarker*` | `\faMapMarker` | Starred (outline) variant requires fontawesome5-regular, which is not installed; solid is always available |
| `\textsubscript{n}` | `$_n$` | Not universally available across MiKTeX versions; inline math is always safe |

</latex-escaping>

---

<bold-usage>

## 4a. Bold Usage

Apply bold rules from `modes/writing.md §3` and `§2.5`. In LaTeX, bold is rendered with `\textbf{}`. Global ceiling: ≤ 8 bold elements across the full CV. Never bold JD keywords injected via §4b.

</bold-usage>

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

<writing-style>

## 4c. Writing Style

Apply all writing style rules from `modes/writing.md` — bullet structure (R0–R9), bold, parentheses, colon, project titles (§19.1), project subtitles (§19.2), Specificity Standard (§8), deverbal nouns for French CVs (§18), and vocabulary bans (§14). The LaTeX-specific rendering note: bold is `\textbf{}`, and client names in `\cventryprojet` titles use `\textbf{Client}`.

For project entry title and subtitle patterns and examples, see `modes/writing.md §19`. For the `§7.8` section below, the title and subtitle patterns in the comments cross-reference writing.md.

</writing-style>

---

<entry-headers>

## 5. Entry Headers — Commands by Section

Each section uses its own dedicated command. They are **not interchangeable**.

| Section | Command | Args | Layout |
|---|---|---|---|
| Expérience Professionnelle | `\cventry` | 4 | line 1: COMPANY — Post · line 2: dates left / country right |
| Formation | `\cventryformation` | 4 | country in `#4` |
| Projets & Innovation | `\cventryprojet` | 3 | none |
| Vie Étudiante | `\cventryassociatif` | 2 | none |
| Concours & Hackathons | `\cventrycontest` | 3 | none |

**`\cventry{company}{post}{country}{dates}`** — Expérience  
Line 1: **COMPANY · Post** (bold, company auto-uppercased). Line 2: dates (left, muted) and country (right, muted).  
- `#1` = company name as written — `\MakeUppercase` is applied automatically.
- `#2` = post and domain. E.g., "Stage en Génie Civil", "Consultant Opérations · Supply Chain".
- `#3` = country only, no city.
- `#4` = dates.

**`\cventryformation{degree}{institution}{dates}{country}`** — Formation  
Line 1: bold degree title + muted date. Line 2: italic institution (left) + italic muted country (right).  
- `#1` = degree name only. Institution is `#2`. Country only in `#4` (no city).

**`\cventryprojet{title}{context}{dates}`** — Projets & Innovation  
Line 1: bold title + muted date. Line 2: italic context/subtitle. No location argument.

**`\cventrycontest{title}{organizer/context}{dates}`** — Concours & Hackathons  
Line 1: bold title + muted date. Line 2: italic organizer/context. No location argument.

**`\cventryassociatif{role}{dates}`** — Vie Étudiante  
Line 1: bold role. Line 2: muted dates (acts as subtitle). No location argument.

✗
```latex
\cventry{Stagiaire Planification}
        {Renault · Supply chain}
        {avr. 2024 -- sept. 2024}
        {France}
```
✓
```latex
\cventry{Renault}
        {Mission Supply Chain}
        {France}
        {avr. 2024 -- sept. 2024}
```

</entry-headers>

---

<dates-and-dashes>

## 6. Dates and Em-dashes

### 6.1 Dates

Apply date format rules from `modes/writing.md §16`. LaTeX rendering: wrap dates in curly braces as the argument to the entry command.

✗ `{09/2023 - 06/2024}` · `{Septembre 2023 -- Juin 2024}` · `{sept. 2023 --- juin 2024}`  
✓ `{sept. 2023 -- juin 2024}`  
✓ `{déc. 2024 -- en cours}`

---

### 6.2 Em-dashes (`---`) and `·`

Apply the triple-dash rule (`modes/writing.md §7`) and the middle-dot prohibition (`modes/writing.md §6`). LaTeX-specific notes:

- `---` renders as an em-dash in LaTeX — permitted only in project titles, project subtitles, and education score/ranking bullets.
- The only `·` permitted is the one hardcoded inside `\cventry` between company and post (`\MakeUppercase{#1} · #2`) — structural, cannot be removed. Do not reproduce it elsewhere.

✗ `\cventryprojet{Diagnostic opérationnel · Veganic}{...}{...}` — use comma  
✗ bullet prose: `Analyse des flux --- identification des goulots.` — em-dash banned in prose  
✗ `\cvheadline{Élève-ingénieur · Recherche Opérationnelle}` — `·` banned in headlines  
✓ `\cventryprojet{Mission de conseil --- \textbf{Air Liquide}}{...}{...}` — structural separator in title  
✓ `\cventryprojet{Diagnostic opérationnel, Veganic}{...}{...}` — comma instead of `·`  
✓ Education bullet: `Moyenne \textbf{8,7/10 --- top 2\,\%} sur 311 étudiants.` — score --- ranking

</dates-and-dashes>

---

<sections>

## 7. CV Sections

Sections are distributed across two columns:

- **Left column (35%):** Contact · Langues · Expérience Professionnelle · Vie Étudiante · Compétences
- **Right column (65%):** Formation · Projets & Innovation · Concours & Hackathons (optional) · Centres d'intérêt (optional)

---

### 7.1 Header and Profil

Apply Headline structure from `modes/writing.md §9` and Summary structure from `modes/writing.md §15`.

```latex
\begin{center}
    \cvname{Óscar Martínez Zamora}\\[3pt]
    \cvheadline{Industrial Engineer specialized in Operations Research \& Risk Analytics}
\end{center}
\vspace{2pt}{\color{accent}\hrule height 0.8pt}\vspace{4pt}

\section*{Profile}
\small
% PROFILE SECTION — 3 sentences (S1 Identity, S2 Track record, S3 Aspiration).
% See modes/writing.md §15 for the full structure and rules.
Engineering student at CentraleSupélec, Université Paris-Saclay,
specializing in Operations Research and Risk Analytics,
seeking an alternance in operations consulting from September 2026.
My background combines quantitative modelling and real client delivery:
fleet sizing for Air Liquide, operational diagnosis of an industrial SME,
and a go-to-market strategy ranked \textbf{1\textsuperscript{st}} among 80 teams.
Seeking to bring this dual competence to operations and supply chain transformation projects.

\vspace{2pt}
```

---

### 7.2 Contact

```latex
\section*{Contact}
\footnotesize
\faMapMarker\ France\\[2pt]
\faPhone\ +33 7 65 50 64 85\\[2pt]
\faPhone\ +34 608 33 78 72\\[2pt]
\faEnvelope\ \href{mailto:oscar.martinez-zamora@student-cs.fr}%
    {oscar.martinez-zamora@student-cs.fr}\\[2pt]
\faLinkedin\ \href{https://www.linkedin.com/in/oscarmartinezzamora/}%
    {oscarmartinezzamora}
```

---

### 7.3 Langues

```latex
\section*{Langues}
\begin{cvlanguages}
    \lang{Espagnol}{natif}
    \lang{Anglais}{Cambridge C1 Advanced}
    \lang{Fran\c{c}ais}{DELF B2}
    \lang{Allemand}{A2}
\end{cvlanguages}
```

---

### 7.4 Expérience Professionnelle

Environment: `cvbulletsexp` (►).

```latex
\section*{Exp\'erience Professionnelle}

\cventry{Bucarest 54}
        {Stage en G\'enie Civil}
        {Espagne}
        {juin -- ao\^ut 2025}
\begin{cvbulletsexp}
    \item{} Con\c{c}u les installations \'electriques, plomberie et CVC
            d'un b\^atiment r\'esidentiel (CYPE, CTE espagnol).
\end{cvbulletsexp}
```

---

### 7.5 Vie Étudiante

Environment: `cvbulletsassociatif` (►). No extra `\vspace` between entries — use the same spacing as Projects and Formation.

```latex
\section*{Vie \'Etudiante}

\cventryassociatif{Secr\'etaire G\'en\'eral, BDI}{f\'ev. 2026 -- f\'ev. 2027}
\begin{cvbulletsassociatif}
    \item{} Coordination d'une association de \textbf{35 membres}
            pour l'int\'egration des \'etudiants internationaux.
    \item{} Pilotage du staffing \'ev\'enementiel
            (World Week, \textbf{200+} participants).
    \item{} Gestion de \textbf{3 partenariats} corporate actifs
            (McKinsey Paris, McKinsey Casablanca, Soci\'et\'e G\'en\'erale).
\end{cvbulletsassociatif}

\cventryassociatif{Tr\'esorier, Club Espagnol}{f\'ev. 2026 -- f\'ev. 2027}
\begin{cvbulletsassociatif}
    \item{} Gestion financi\`ere et n\'egociation de subventions
            devant le CA du BDI (\textbf{300\,€} obtenus).
\end{cvbulletsassociatif}
```

---

### 7.6 Compétences

Skill rows use `\cvskill` inside a `cvskillsblock` environment. The environment hardcodes zero spacing between rows — do not add any `\vspace` between rows.

```latex
\section*{Comp\'etences}
\begin{cvskillsblock}
    \cvskill{Programmation}{Python, SQL, MATLAB}
    \cvskill{Optimisation}{Gurobi, Simul8, MILP, MCDA}
    \cvskill{Analyse \& BI}{Power BI, Excel, AutoCAD, CYPE}
    \cvskill{Outils}{LaTeX, Office Suite}
    \cvskill{IA \& Automation}{Claude Code, n8n, LLM}
\end{cvskillsblock}
```

---

### 7.7 Formation

Environment: `cvbulletsformation` (►).

```latex
\section*{Formation}

\cventryformation{Dipl\^ome Ing\'enieur G\'en\'eraliste}{CentraleSup\'elec, Universit\'e Paris-Saclay}{sept. 2025 -- avr. 2027}{France}
\begin{cvbulletsformation}
    \item{} Sp\'ecialisation \emph{Operations Research and Risk Analytics}.
    \item{} \textbf{Premier \'etudiant} du Master G\'enie Industriel de l'UPV
            \`a int\'egrer ce programme d'\'echange (Universit\'e Paris-Saclay).
\end{cvbulletsformation}

\cventryformation{Master Ing\'enierie Industrielle}{Universitat Polit\`ecnica de Val\`encia}{sept. 2024 -- avr. 2027}{Espagne}
\begin{cvbulletsformation}
    \item{} Moyenne \textbf{7,7/10}, top 10\,\% de la promotion.
\end{cvbulletsformation}

\cventryformation{Licence Ing\'enierie Industrielle}{Universitat Polit\`ecnica de Val\`encia}{sept. 2020 -- juil. 2024}{Espagne}
\begin{cvbulletsformation}
    \item{} Moyenne \textbf{8,7/10}, top 2\,\% sur 311 \'etudiants ;
            Prix Haute Performance Acad\'emique UPV.
    \item{} Semestre Erasmus \`a Politechnika Krakowska (Cracovie).
\end{cvbulletsformation}
```

---

### 7.8 Projets & Innovation

Environment: `cvbullets` (►).

**Title pattern** and **Subtitle pattern**: see `modes/writing.md §19`. No tools in the subtitle; tools go in bullets only.

```latex
\section*{Projets \& Innovation}

\cventryprojet{Simulation supply chain H$_2$, Air Liquide}
              {ST7 Optimisation et gestion de flux, CentraleSup\'elec}
              {avr. 2026}
\begin{cvbullets}
    \item{} Recommandation d'une configuration de flotte
            \textbf{z\'ero-rupture} pour Air Liquide (mobilit\'e H$_2$).
    \item{} Conception d'un algorithme de dispatch propri\'etaire
            et d'une simulation supply chain (Simul8) pour 5~stations
            de livraison.
    \item{} D\'etermination de la configuration minimale viable de
            flotte par analyse de capacit\'e et de sensibilit\'e.
\end{cvbullets}

\cventryprojet{\'Etude carbone des biod\'echets}
              {P\^ole Projet S7, CentraleSup\'elec}
              {sept. 2025 -- f\'ev. 2026}
\begin{cvbullets}
    \item{} Recommandation de la m\'ethanisation face au Bokashi
            pour la gestion des biod\'echets en France.
    \item{} Comparaison de trois solutions de valorisation sur axes
            co\^ut, carbone et r\'eglementaire (loi AGEC 2024).
\end{cvbullets}

\cventryprojet{Diagnostic op\'erationnel, Veganic}
              {Am\'elioration des op\'erations d'une PME agro-chimique}
              {f\'ev. -- juin 2025}
\begin{cvbullets}
    \item{} Proposition de 5~am\'eliorations projetant \textbf{+68\,\%}
            de d\'ebit en palettisation et utilisation des rayonnages
            de 60 \`a 90\,\%.
    \item{} Mod\'elisation du VAN de l'investissement optimal :
            retour en 18~mois \`a 10\,\% d'actualisation.
\end{cvbullets}
```

---

### 7.9 Concours & Hackathons (optional section)

Include only if:
- (a) The contest result directly reinforces a claim in the profile or experience, or
- (b) The CV has space and the award is verifiable (ranking, jury, prize).

Use `\cventrycontest` (same 3-arg structure as `\cventryprojet`) and `cvbulletscontest` environment.

```latex
\section*{Concours \& Hackathons}

\cventrycontest{1\textsuperscript{er} Prix, Programme Akademia}
               {Fondation Bankinter, Hackathon national, 80 \'equipes}
               {f\'ev. 2025}
\begin{cvbulletscontest}
    \item{} Conception d'un mod\`ele MCDA \'evaluant \textbf{36\,000+}
            zones de recensement espagnoles sur 7 crit\`eres
            d\'emographiques, \'economiques et climatiques.
    \item{} \'Elaboration d'un business model B2C complet :
            canal de distribution, \'economies unitaires, roadmap produit.
\end{cvbulletscontest}\vspace{4pt}

\cventrycontest{Finalist, Case Competition Name}
               {Organizer, Category}
               {nov. 2024}
\begin{cvbulletscontest}
    \item{} One-line achievement with a verifiable metric.
\end{cvbulletscontest}
```

---

### 7.10 Hobbies (optional section)

Apply Hobbies inclusion criteria and format from `modes/writing.md §10`.

</sections>

---

<final-check>

## 8. Final Verification

### 8.1 6-second scan test

The following 5 elements must be visible in 6 seconds without reading the CV:
name · headline · most recent education · most recent or most relevant experience/project · 2-3 keywords from the posting.

IF any of these is not immediately visible → reorganise the visual hierarchy.

---

<self-check id="full-cv">

### ✅ Full CV checklist

1. Does it fit on 1 A4 page without reducing font size or margins?
2. Do all dates follow `{mon. YYYY -- mon. YYYY}`?
3. Do all `\cventry` commands have 4 arguments with location in `#4`?
4. Are there any em-dashes (`---`) or `·` anywhere in the document content (titles, bullets, paragraphs)? → replace with a comma or rephrase. The only permitted `·` is the structural one inside `\cventry` between company and post.
5. Are the 5 elements of the scan test §8.1 immediately visible?
6. Is the active palette the confirmed one? Are the other three commented out?
7. Does the file include `\pdfgentounicode=1` after `\pagestyle{empty}`?

</self-check>

</final-check>

---

<adjustment>

## 9. Fitting to 1 Page

IF the CV overflows → intervene in this order:

| Step | Action |
|---|---|
| 1 | Columns: right overflows → `\columnratio{0.30}`. Right too empty → `\columnratio{0.40}`. |
| 2 | Merge bullets: combine 2 with "et" or a comma; reduce from 3 to 2 bullets in less critical projects. |
| 3 | Shorten titles: if `#1` of `\cventry` clashes with the date → move context to `#2`. |
| 4 | Spacing: `\titlespacing*{\section}{0pt}{6pt}{3pt}` → `{5pt}{2pt}` · `\vspace{-4pt}` → `\vspace{-5pt}`. |
| 5 | Vie Étudiante: shorten or reduce from 3 to 2 entries. |

**Never:** font < 10pt · margins < 1 cm (top/bottom) or < 1.2 cm (left/right) · `\linespread`.

</adjustment>

