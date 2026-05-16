# Skill — CV Formatting and Content in LaTeX

---

<pipeline>

## Pipeline

1. Read `config/profile.md` as a single YAML document. CV content lives in the top-level keys: `summary`, `education`, `experience`, `student_life`, `projects`, `competitions`, `skills`, `hobbies`. Identity and contact are in `candidate.*`. Languages are in the top-level `languages` array.
2. Read `candidate.*` for identity and contact info; read `location.*` for location data
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
`datetext` → `#777777` fixed — used exclusively for dates in all entry commands. Do not modify. Mandatory: every date argument in every `\cventry*` command must use `\textcolor{datetext}`, never `\textcolor{muted}`.

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
\definecolor{datetext}{HTML}{777777}  % mandatory — dates only; always lighter than muted

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
    \noindent{\small\textcolor{datetext}{#4}}\hfill{\small\textcolor{muted}{#3}}\par\vspace{-4pt}%
}

% \cventryformation — Formation (4-arg)
%   #1 degree title         → line 1 left, bold
%   #2 institution          → line 2 left, italic
%   #3 dates                → line 1 right, datetext
%   #4 country              → line 2 right, italic muted
\newcommand{\cventryformation}[4]{%
    \noindent{\normalsize\textbf{#1}}\hfill\textcolor{datetext}{\small #3}\\%
    \noindent{\small\itshape #2}\hfill{\small\itshape\textcolor{muted}{#4}}\par\vspace{-4pt}%
}

% \cventryprojet — Projets & Innovation (3-arg, no location)
%   #1 project title        → line 1 left, bold
%   #2 context/subtitle     → line 2 left, italic
%   #3 dates                → line 1 right, datetext
\newcommand{\cventryprojet}[3]{%
    \noindent{\normalsize\textbf{#1}}\hfill\textcolor{datetext}{\small #3}\\%
    {\small\itshape #2}\par\vspace{-4pt}%
}

% \cventryassociatif — Vie Étudiante (2-arg, no location)
%   #1 role title           → line 1 left, bold
%   #2 dates                → line 2 left, datetext (acts as subtitle)
\newcommand{\cventryassociatif}[2]{%
    \noindent{\normalsize\textbf{#1}}\\%
    {\small\textcolor{datetext}{#2}}\par\vspace{-4pt}%
}

% \cventrycontest — Concours & Hackathons (3-arg, no location)
%   #1 contest/award title  → line 1 left, bold
%   #2 context/organizer    → line 2 left, italic
%   #3 dates                → line 1 right, datetext
\newcommand{\cventrycontest}[3]{%
    \noindent{\normalsize\textbf{#1}}\hfill\textcolor{datetext}{\small #3}\\%
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

**Data source:** read from `config/profile.md` YAML keys:
- `candidate.full_name` → `\cvname{}`
- `narrative.headline` → `\cvheadline{}` (adapt to CV language and JD archetype)
- `summary` (top-level string) → body of the `\section*{Profile}` paragraph, rewritten with JD keywords per §4b

```latex
\begin{center}
    \cvname{<candidate.full_name>}\\[3pt]
    \cvheadline{<narrative.headline — adapted to CV language and role archetype>}
\end{center}
\vspace{2pt}{\color{accent}\hrule height 0.8pt}\vspace{4pt}

\section*{Profile}
\small
% PROFILE SECTION — 3 sentences (S1 Identity, S2 Track record, S3 Aspiration).
% Source: summary: string from config/profile.md, rewritten with JD keywords.
% See modes/writing.md §15 for the full structure and rules.
<rewritten summary paragraph — 3 sentences injecting JD keywords, no invented claims>

\vspace{2pt}
```

---

### 7.2 Contact

**Data source:** read from `config/profile.md` YAML keys:
- `location.country` → country label after `\faMapMarker`
- `candidate.phone_fr` → first phone line (omit if null)
- `candidate.phone_es` → second phone line (omit if null)
- `candidate.email` → mailto href and display text
- `candidate.linkedin` → full URL; display text is the URL path segment after `/in/` (strip trailing slash)

Phone format: keep digits as-is from YAML but insert spaces for readability (e.g., `+33 7 65 50 64 85`). Omit any phone field that is null or empty.

```latex
\section*{Contact}
\footnotesize
\faMapMarker\ <location.country>\\[2pt]
\faPhone\ <candidate.phone_fr formatted>\\[2pt]
\faPhone\ <candidate.phone_es formatted>\\[2pt]
\faEnvelope\ \href{mailto:<candidate.email>}%
    {<candidate.email — escape underscores with \_>}\\[2pt]
\faLinkedin\ \href{<candidate.linkedin>}%
    {<linkedin path segment — e.g. oscarmartinezzamora>}
```

---

### 7.3 Langues

**Data source:** iterate the `languages:` array in `config/profile.md`. Each item has:
- `language` → language name (translate to CV language if FR posting)
- `level` → level description

Emit one `\lang{}{}` line per item, in the order they appear in the array. Translate language names to match CV language (e.g., for a French CV: "Spanish" → "Espagnol", "English" → "Anglais", "French" → "Français", "German" → "Allemand").

```latex
\section*{Langues}
\begin{cvlanguages}
    % For each item in languages[]:
    \lang{<language — translated to CV language>}{<level>}
\end{cvlanguages}
```

---

### 7.4 Expérience Professionnelle

Environment: `cvbulletsexp` (►).

**Data source:** iterate the `experience:` array in `config/profile.md`. Each item has:
- `company` → `\cventry` arg 1 (company name; `\MakeUppercase` applied automatically)
- `post` → `\cventry` arg 2 (role title; translate to CV language)
- `country` → `\cventry` arg 3
- `date` → `\cventry` arg 4
- `bullets` → list of bullet strings; reorder by JD relevance; inject keywords per §4b

Emit one `\cventry` + `\begin{cvbulletsexp}...\end{cvbulletsexp}` block per item. Escape all user text per §3c.

```latex
\section*{Exp\'erience Professionnelle}

% For each item in experience[]:
\cventry{<company>}
        {<post — translated to CV language>}
        {<country>}
        {<date>}
\begin{cvbulletsexp}
    % For each bullet in bullets[]:
    \item{} <bullet — translated, keyword-injected, escaped>
\end{cvbulletsexp}
```

---

### 7.5 Vie Étudiante

Environment: `cvbulletsassociatif` (►). No extra `\vspace` between entries — use the same spacing as Projects and Formation.

**Data source:** iterate the `student_life:` array in `config/profile.md`. Each item has:
- `title` → `\cventryassociatif` arg 1 (role title; translate to CV language)
- `date` → `\cventryassociatif` arg 2
- `bullets` → list of bullet strings; select the 1–3 most JD-relevant; translate and escape

Emit one `\cventryassociatif` + `\begin{cvbulletsassociatif}...\end{cvbulletsassociatif}` block per item included. Select 2–3 entries maximum to respect the 1-page constraint.

```latex
\section*{Vie \'Etudiante}

% For each item in student_life[] (select 2–3 most relevant):
\cventryassociatif{<title — translated to CV language>}{<date>}
\begin{cvbulletsassociatif}
    % For each selected bullet in bullets[]:
    \item{} <bullet — translated, keyword-injected, escaped>
\end{cvbulletsassociatif}
```

---

### 7.6 Compétences

Skill rows use `\cvskill` inside a `cvskillsblock` environment. The environment hardcodes zero spacing between rows — do not add any `\vspace` between rows.

**Data source:** iterate the `skills:` array in `config/profile.md`. Each item has:
- `category` → `\cvskill` arg 1 (label; translate to CV language)
- `items` → `\cvskill` arg 2 (comma-separated tool/skill list; escape `&` as `\&`)

Emit one `\cvskill{}{}` line per item in the array order. Prioritise or reorder categories to surface JD-relevant skills first.

```latex
\section*{Comp\'etences}
\begin{cvskillsblock}
    % For each item in skills[]:
    \cvskill{<category — translated to CV language>}{<items — escape & as \&>}
\end{cvskillsblock}
```

---

### 7.7 Formation

Environment: `cvbulletsformation` (►).

**Data source:** iterate the `education:` array in `config/profile.md`. Each item has:
- `title` → `\cventryformation` arg 1 (degree title; translate to CV language)
- `subtitle` → `\cventryformation` arg 2 (institution name)
- `date` → `\cventryformation` arg 3
- `country` → `\cventryformation` arg 4
- `bullets` → list of bullet strings; select 1–2 most relevant per entry; translate and escape

Emit one `\cventryformation` + `\begin{cvbulletsformation}...\end{cvbulletsformation}` block per item, in the order they appear in the array.

```latex
\section*{Formation}

% For each item in education[]:
\cventryformation{<title — translated to CV language>}
                 {<subtitle — institution name>}
                 {<date>}
                 {<country — translated to CV language>}
\begin{cvbulletsformation}
    % For each selected bullet in bullets[] (1–2 per entry):
    \item{} <bullet — translated, keyword-injected, escaped>
\end{cvbulletsformation}
```

---

### 7.8 Projets & Innovation

Environment: `cvbullets` (►).

**Title pattern** and **Subtitle pattern**: see `modes/writing.md §19`. No tools in the subtitle; tools go in bullets only.

**Data source:** iterate the `projects:` array in `config/profile.md`. Each item has:
- `title` → `\cventryprojet` arg 1 (≤ 45 chars; see §3d; translate to CV language; escape per §3c)
- `subtitle` → `\cventryprojet` arg 2 (context line; translate to CV language; no tools here — move tools to bullets)
- `date` → `\cventryprojet` arg 3
- `bullets` → list of bullet strings; select the top 3-4 most JD-relevant projects (step 8 of pipeline); within each project select 2–3 bullets ordered by JD relevance; translate, inject keywords, escape

```latex
\section*{Projets \& Innovation}

% For each selected project in projects[] (top 3–4 by JD relevance):
\cventryprojet{<title — ≤ 45 chars, translated, escaped>}
              {<subtitle — translated, escaped, no tool names>}
              {<date>}
\begin{cvbullets}
    % For each selected bullet (2–3 per project):
    \item{} <bullet — translated, keyword-injected, escaped>
\end{cvbullets}
```

---

### 7.9 Concours & Hackathons (optional section)

Include only if:
- (a) The contest result directly reinforces a claim in the profile or experience, or
- (b) The CV has space and the award is verifiable (ranking, jury, prize).

Use `\cventrycontest` (same 3-arg structure as `\cventryprojet`) and `cvbulletscontest` environment.

**Data source:** iterate the `competitions:` array in `config/profile.md`. Each item has:
- `title` → `\cventrycontest` arg 1 (≤ 45 chars; see §3d; translate to CV language; escape per §3c)
- `subtitle` → `\cventrycontest` arg 2 (organizer/context; translate to CV language)
- `date` → `\cventrycontest` arg 3
- `bullets` → list of bullet strings; select 1–2 most JD-relevant; translate, inject keywords, escape

```latex
\section*{Concours \& Hackathons}

% For each item in competitions[] that meets inclusion criteria (a) or (b):
\cventrycontest{<title — ≤ 45 chars, translated, escaped>}
               {<subtitle — organizer/context, translated, escaped>}
               {<date>}
\begin{cvbulletscontest}
    % For each selected bullet (1–2 per entry):
    \item{} <bullet — translated, keyword-injected, escaped>
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

