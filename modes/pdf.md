# Mode: pdf — CV PDF Generation

<purpose>
Generate a tailored PDF CV for a target role. Defaults to LaTeX (ATS-compliant, 1-page A4); offers Canva when a design ID is configured.
</purpose>

<rules>
- NEVER invent metrics, keywords, or experience not present in `config/profile.md`
- Canva text replacements MUST stay within ±15% of the original character count (fixed-size text boxes)
- NEVER commit a Canva transaction without explicit user approval
- Download the export URL immediately — pre-signed S3 links expire in ~2 hours
- After generation, update the tracker: flip PDF column from ❌ to ✅ if the offer is already registered
</rules>

---

## LaTeX Flow (default)

<agent_instruction>
Use this path unless the user explicitly requests Canva or `cv.canva_resume_design_id` is set in `config/profile.md`.
</agent_instruction>

### LaTeX Rules

- R1: 1 A4 page mandatory. Overflow → §9.
- R2: Font ≥ 10pt; margins ≥ 1 cm top/bottom, ≥ 1.2 cm left/right. Never reduce either.
- R3: No photo.
- R4: CV language = language of the job posting (EN default, FR common).
- R5: `\pdfgentounicode=1` must appear after `\pagestyle{empty}` in every file.
- R6: NEVER add skills the candidate doesn't have — only reformulate existing experience using JD vocabulary.
- R7: Never bold JD-injected keywords. Global bold ceiling: ≤ 8 bold elements across the full CV.
- R8: `datetext` (`#777777`) is used exclusively for dates in all `\cventry*` commands — never `\textcolor{muted}` for dates.
- R9: `columnratio` default is `0.35`; never go below `0.28` or above `0.40`.

---

### Pipeline

<process>

<step id="1" name="read-profile">
Read `config/profile.md` as a single YAML document. CV content keys: `summary`, `education`, `experience`, `student_life`, `projects`, `competitions`, `skills`, `hobbies`. Identity/contact in `candidate.*`; location in `location.*`; languages in top-level `languages` array.
</step>

<step id="2" name="get-jd">
<agent_instruction>If the JD is not already in context, ask the user for it (text or URL).</agent_instruction>
</step>

<step id="3" name="jd-analysis">
Extract 15–20 keywords from the JD. Detect JD language → set CV language. Detect role archetype → adapt framing.
</step>

<step id="4" name="tailor-content">
- Rewrite Professional Summary injecting JD keywords (see §4b and `modes/writing.md §15`).
- Select top 3–4 most relevant projects ordered by JD relevance.
- Reorder experience bullets by JD relevance.
- Inject keywords naturally into existing achievements.
</step>

<step id="5" name="generate-tex">
Generate the `.tex` file using the preamble in §3 and section structure in §7. Write to:
`output/cv-{candidate}-{company}-{YYYY-MM-DD}.tex`
</step>

<step id="6" name="compile">
Run: `node generate-pdf.mjs output/cv-{candidate}-{company}-{YYYY-MM-DD}.tex output/cv-{candidate}-{company}-{YYYY-MM-DD}.pdf`

**Requires:** `tectonic` (preferred — `brew install tectonic`) or `pdflatex` (MiKTeX / TeX Live) on PATH.
</step>

<step id="7" name="report">
Report: `.tex` path, `.pdf` path, file sizes, section count, keyword coverage %.
</step>

</process>

---

### Writing Conventions

<agent_instruction>
Read `modes/writing.md` before generating any output. All bullet rules (R0–R9), bold, parentheses, colon, middle-dot, triple-dash, Specificity Standard, Headline, Hobbies, trim signals, vocabulary bans, deverbal nouns (§18), date format (§16), and project title/subtitle patterns (§19) are defined there. LaTeX-specific note: bold renders as `\textbf{}`; client names in `\cventryprojet` titles use `\textbf{Client}`.
</agent_instruction>

---

### 1. ATS Rules

- Two-column layout via `paracol` — ATS parsers read left column first, then right.
- Section headers use standard names in the posting language (e.g. "Formation", "Expérience Professionnelle" for FR).
- Keywords distributed: first bullet of each experience entry + skills section.
- No images, no graphics, no body-text color — color only in structural elements (section rules, bullet markers).
- UTF-8 enforced by `[utf8]{inputenc}` + `[T1]{fontenc}`.

---

### 2. Color Palettes

<reference id="color-palettes">
Only one palette active (uncommented in the `COLORS` block); the other three commented out. Choose based on sector/tone.

| # | Name | `primary` | `accent` | When to use |
|---|---|---|---|---|
| 1 | Corporate Blue | `#1F3A5F` | `#2E86AB` | Large industrial, aerospace, automotive, energy |
| 2 | Teal / Forest Green | `#1A5F7A` | `#2E7D6B` | Sustainability, renewables, environmental engineering |
| 3 | Burgundy / Wine | `#6B2D3E` | `#9B3A52` | Strategic consulting, banking, management |
| 4 | Slate + Copper | `#3C3C3C` | `#B87333` | Tech, product, startups, data/AI |

Fixed across all palettes — do not modify:
- `muted` / `bodytext` → `#555555`
- `datetext` → `#777777` (dates only; always lighter than muted)
</reference>

---

### 3. LaTeX Preamble

<format id="preamble">

Copy the preamble in full; only modify the active palette.

```latex
\documentclass[a4paper,11pt]{article}

%==================== PACKAGES ============================
\usepackage[utf8]{inputenc}
\usepackage[T1]{fontenc}
\usepackage{lmodern}
\usepackage[a4paper,top=1cm,bottom=1cm,left=1.2cm,right=1.2cm]{geometry}
\usepackage{paracol}
\usepackage{xcolor}
\usepackage{fontawesome5}
\usepackage[hidelinks]{hyperref}
\usepackage{titlesec}
\usepackage{enumitem}
\usepackage{parskip}
\usepackage{ragged2e}
\usepackage{microtype}
\usepackage{xurl}

%==================== COLORS =============================
% Uncomment ONE palette:

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
\definecolor{datetext}{HTML}{777777}

%==================== HYPERLINKS =========================
\hypersetup{colorlinks=true, urlcolor=accent, linkcolor=accent}

%==================== PAGE ================================
\pagestyle{empty}
\pdfgentounicode=1
\setlength{\columnsep}{0.7cm}
\setlength{\emergencystretch}{3em}

%==================== SECTIONS ===========================
\titleformat{\section}
    {\large\bfseries\color{primary}}
    {}{0em}{}[\vspace{-2pt}{\color{accent}\titlerule[0.8pt]}]
\titlespacing*{\section}{0pt}{6pt}{3pt}

%==================== COMMANDS ===========================

\newcommand{\cvname}[1]{{\Huge\bfseries\color{primary} #1}}
\newcommand{\cvheadline}[1]{{\Large\color{muted} #1}}

% \cventry{company}{post}{country}{dates}
\newcommand{\cventry}[4]{%
    \noindent{\normalsize\textbf{\MakeUppercase{#1} · #2}}\\%
    \noindent{\small\textcolor{datetext}{#4}}\hfill{\small\textcolor{muted}{#3}}\par\vspace{-4pt}%
}

% \cventryformation{degree}{institution}{dates}{country}
\newcommand{\cventryformation}[4]{%
    \noindent{\normalsize\textbf{#1}}\hfill\textcolor{datetext}{\small #3}\\%
    \noindent{\small\itshape #2}\hfill{\small\itshape\textcolor{muted}{#4}}\par\vspace{-4pt}%
}

% \cventryprojet{title}{context}{dates}
\newcommand{\cventryprojet}[3]{%
    \noindent{\normalsize\textbf{#1}}\hfill\textcolor{datetext}{\small #3}\\%
    {\small\itshape #2}\par\vspace{-4pt}%
}

% \cventryassociatif{role}{dates}
\newcommand{\cventryassociatif}[2]{%
    \noindent{\normalsize\textbf{#1}}\\%
    {\small\textcolor{datetext}{#2}}\par\vspace{-4pt}%
}

% \cventrycontest{title}{organizer/context}{dates}
\newcommand{\cventrycontest}[3]{%
    \noindent{\normalsize\textbf{#1}}\hfill\textcolor{datetext}{\small #3}\\%
    {\small\itshape #2}\par\vspace{-4pt}%
}

% \cvskill{label}{values} — use inside cvskillsblock
\newcommand{\cvskill}[2]{\item \textbf{#1~:} #2}

%==================== ENVIRONMENTS =======================

\newenvironment{cvlanguages}{%
    \begin{list}{}{\setlength{\leftmargin}{0pt}\setlength{\rightmargin}{0pt}%
        \setlength{\topsep}{0pt}\setlength{\partopsep}{0pt}%
        \setlength{\itemsep}{0pt}\setlength{\parsep}{0pt}%
    }\small\color{bodytext}}{\end{list}}
\newcommand{\lang}[2]{\item \textbf{#1~:} #2}

\newenvironment{cvskillsblock}{%
    \begin{list}{}{\setlength{\leftmargin}{0pt}\setlength{\rightmargin}{0pt}%
        \setlength{\topsep}{0pt}\setlength{\partopsep}{0pt}%
        \setlength{\itemsep}{0pt}\setlength{\parsep}{0pt}%
    }\small\color{bodytext}}{\end{list}}

% Bullet environments — NOT interchangeable; each section uses its own.
% All use faAngleRight (►) marker.
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

After the preamble, use this fixed body structure. Do not alter column split or section order.

```latex
\begin{document}

%==================== HEADER (full width) ====================
% \cvname, \cvheadline, \hrule, \section*{Profil}  → §7.1

%==================== TWO-COLUMN BODY ========================
\columnratio{0.35}
\begin{paracol}{2}

%---------- LEFT COLUMN (35%) ----------
\RaggedRight
% \section*{Contact}                         → §7.2
% \section*{Langues}                         → §7.3
% \section*{Expérience Professionnelle}      → §7.4
% \section*{Vie Étudiante}                   → §7.5
% \section*{Compétences}                     → §7.6

\switchcolumn

%---------- RIGHT COLUMN (65%) ----------
% \section*{Formation}                       → §7.7
% \section*{Projets \& Innovation}           → §7.8
% \section*{Concours \& Hackathons}          → §7.9 (optional)
% \section*{Centres d'intérêt}              → §7.10 (optional)

\end{paracol}
\end{document}
```

</format>

---

### 3c. LaTeX Escaping

<rules id="escaping">

Escape all user-supplied text before insertion. Do NOT escape LaTeX commands.

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

**URLs:** Do NOT escape inside `\href{URL}{...}` first argument. Escape display text only:
```latex
\href{https://example.com/path_with_underscores}{Example\_Display}
```

### 3d. Entry Title Length Limit

`\cventryprojet` and `\cventrycontest` place `#1` and the date on the same line with `\hfill`. If `#1` is too long the date wraps — a visible layout defect.

**`\cventry` is exempt** (company and post are on line 1 alone, no `\hfill` with the date).

| Column | Max chars for `#1` |
|--------|-------------------|
| Right (65%) | **≤ 45 chars** |

If the title exceeds the limit → shorten `#1` and move extra context into `#2`. Never reduce font or column ratio to work around long titles.

✗ `\cventryprojet{Analyse du bilan carbone de solutions de gestion des biodéchets}{...}{...}`  
✓ `\cventryprojet{Analyse bilan carbone des biodéchets}{...}{...}`

### 3e. Known-Bad Commands

| Do NOT use | Use instead | Reason |
|------------|-------------|--------|
| `\euro{}` or `\euro` | `€` (UTF-8 directly) | Requires eurosym/textcomp — not in preamble |
| `\faMapMarker*` | `\faMapMarker` | Starred variant requires fontawesome5-regular (not installed) |
| `\textsubscript{n}` | `$_n$` | Not universally available across MiKTeX versions |

</rules>

---

### 4b. Keyword Injection Strategy

<rules id="keyword-injection">

- NEVER add skills the candidate doesn't have.
- Only reformulate existing experience using JD vocabulary. Examples:
  - JD "gestion de projet" → reword "coordination d'équipe" to "gestion de projet transverse"
  - JD "data-driven" → reword "analyse des résultats" to "décision data-driven"
  - JD "supply chain" → reword "flux logistiques" to "chaîne logistique"
- Inject into: first bullet of each experience entry, profile sentence 2, skills section.
- Target: ≥ 60% of JD's top 15 keywords present in the final CV.

</rules>

---

### 5. Entry Headers — Commands by Section

<reference id="entry-commands">

Each section uses its own dedicated command — they are NOT interchangeable.

| Section | Command | Args |
|---|---|---|
| Expérience Professionnelle | `\cventry{company}{post}{country}{dates}` | 4 |
| Formation | `\cventryformation{degree}{institution}{dates}{country}` | 4 |
| Projets & Innovation | `\cventryprojet{title}{context}{dates}` | 3 |
| Vie Étudiante | `\cventryassociatif{role}{dates}` | 2 |
| Concours & Hackathons | `\cventrycontest{title}{organizer/context}{dates}` | 3 |

**`\cventry` argument notes:**
- `#1` = company name — `\MakeUppercase` applied automatically.
- `#2` = post and domain (e.g., "Stage en Génie Civil", "Consultant Opérations · Supply Chain").
- `#3` = country only, no city.
- `#4` = dates.

✗
```latex
\cventry{Stagiaire Planification}{Renault · Supply chain}{avr. 2024 -- sept. 2024}{France}
```
✓
```latex
\cventry{Renault}{Mission Supply Chain}{France}{avr. 2024 -- sept. 2024}
```

</reference>

---

### 6. Dates and Em-dashes

<rules id="dates-dashes">

**Dates:** apply format rules from `modes/writing.md §16`.

✗ `{09/2023 - 06/2024}` · `{Septembre 2023 -- Juin 2024}`  
✓ `{sept. 2023 -- juin 2024}` · `{déc. 2024 -- en cours}`

**Em-dashes (`---`) and `·`:** apply rules from `modes/writing.md §6–7`. LaTeX-specific:
- `---` permitted only in project titles, project subtitles, and education score/ranking bullets.
- The only `·` permitted is the one hardcoded inside `\cventry` between company and post — do not reproduce it elsewhere.

✗ `\cventryprojet{Diagnostic opérationnel · Veganic}{...}{...}` — use comma  
✗ `\cvheadline{Élève-ingénieur · Recherche Opérationnelle}` — `·` banned in headlines  
✓ `\cventryprojet{Mission de conseil --- \textbf{Air Liquide}}{...}{...}` — structural separator  
✓ `Education bullet: Moyenne \textbf{8,7/10 --- top 2\,\%} sur 311 étudiants.`

</rules>

---

### 7. CV Sections

**Left column (35%):** Contact · Langues · Expérience Professionnelle · Vie Étudiante · Compétences  
**Right column (65%):** Formation · Projets & Innovation · Concours & Hackathons (opt.) · Centres d'intérêt (opt.)

---

#### 7.1 Header and Profil

<agent_instruction>
Data source — `config/profile.md`:
- `candidate.full_name` → `\cvname{}`
- `narrative.headline` → `\cvheadline{}` (adapt to CV language and JD archetype; apply `modes/writing.md §9`)
- `summary` → body of `\section*{Profile}`, rewritten with JD keywords per §4b (3 sentences: S1 Identity, S2 Track record, S3 Aspiration; see `modes/writing.md §15`)
</agent_instruction>

<format id="section-header">

```latex
\begin{center}
    \cvname{<candidate.full_name>}\\[3pt]
    \cvheadline{<headline — adapted to CV language and role archetype>}
\end{center}
\vspace{2pt}{\color{accent}\hrule height 0.8pt}\vspace{4pt}

\section*{Profile}
\small
<rewritten summary — 3 sentences injecting JD keywords, no invented claims>

\vspace{2pt}
```

</format>

---

#### 7.2 Contact

<agent_instruction>
Data source — `config/profile.md`:
- `location.country` → after `\faMapMarker`
- `candidate.phone_fr` / `candidate.phone_es` → phone lines (omit if null); insert spaces for readability
- `candidate.email` → mailto href and display text
- `candidate.linkedin` → full URL; display text = path segment after `/in/` (strip trailing slash)
</agent_instruction>

<format id="section-contact">

```latex
\section*{Contact}
\footnotesize
\faMapMarker\ <location.country>\\[2pt]
\faPhone\ <phone_fr formatted>\\[2pt]
\faPhone\ <phone_es formatted>\\[2pt]
\faEnvelope\ \href{mailto:<email>}{<email — escape \_>}\\[2pt]
\faLinkedin\ \href{<linkedin URL>}{<linkedin path segment>}
```

</format>

---

#### 7.3 Langues

<agent_instruction>
Data source: `languages[]` array in `config/profile.md` (keys: `language`, `level`). Translate language names to CV language (e.g., FR CV: "Spanish" → "Espagnol"). Emit one `\lang` per item in array order.
</agent_instruction>

<format id="section-languages">

```latex
\section*{Langues}
\begin{cvlanguages}
    \lang{<language — translated>}{<level>}
    % repeat for each item
\end{cvlanguages}
```

</format>

---

#### 7.4 Expérience Professionnelle

<agent_instruction>
Data source: `experience[]` array (keys: `company`, `post`, `country`, `date`, `bullets`). Reorder bullets by JD relevance; inject keywords per §4b. Escape all text per §3c.
</agent_instruction>

<format id="section-experience">

```latex
\section*{Exp\'erience Professionnelle}

\cventry{<company>}{<post — translated>}{<country>}{<date>}
\begin{cvbulletsexp}
    \item{} <bullet — translated, keyword-injected, escaped>
    % repeat per bullet; repeat \cventry block per entry
\end{cvbulletsexp}
```

</format>

---

#### 7.5 Vie Étudiante

<agent_instruction>
Data source: `student_life[]` array (keys: `title`, `date`, `bullets`). Select 2–3 entries max (1-page constraint). Per entry: select 1–3 most JD-relevant bullets. No extra `\vspace` between entries.
</agent_instruction>

<format id="section-student-life">

```latex
\section*{Vie \'Etudiante}

\cventryassociatif{<title — translated>}{<date>}
\begin{cvbulletsassociatif}
    \item{} <bullet — translated, keyword-injected, escaped>
    % repeat per bullet; repeat block per entry (max 2–3 entries)
\end{cvbulletsassociatif}
```

</format>

---

#### 7.6 Compétences

<agent_instruction>
Data source: `skills[]` array (keys: `category`, `items`). Prioritise JD-relevant categories first. Escape `&` as `\&`.
</agent_instruction>

<format id="section-skills">

```latex
\section*{Comp\'etences}
\begin{cvskillsblock}
    \cvskill{<category — translated>}{<items — escape \&>}
    % repeat per item; zero \vspace between rows
\end{cvskillsblock}
```

</format>

---

#### 7.7 Formation

<agent_instruction>
Data source: `education[]` array (keys: `title`, `subtitle`, `date`, `country`, `bullets`). Per entry: select 1–2 most relevant bullets. Emit in array order.
</agent_instruction>

<format id="section-education">

```latex
\section*{Formation}

\cventryformation{<title — translated>}{<subtitle — institution>}{<date>}{<country — translated>}
\begin{cvbulletsformation}
    \item{} <bullet — translated, keyword-injected, escaped>
    % 1–2 bullets per entry; repeat block per entry
\end{cvbulletsformation}
```

</format>

---

#### 7.8 Projets & Innovation

<agent_instruction>
Data source: `projects[]` array (keys: `title`, `subtitle`, `date`, `bullets`). Select top 3–4 projects by JD relevance. Title ≤ 45 chars (§3d). No tool names in subtitle — tools go in bullets only. Per project: select 2–3 bullets ordered by JD relevance. Title/subtitle patterns: `modes/writing.md §19`.
</agent_instruction>

<format id="section-projects">

```latex
\section*{Projets \& Innovation}

\cventryprojet{<title — ≤45 chars, translated, escaped>}
              {<subtitle — translated, escaped, no tool names>}
              {<date>}
\begin{cvbullets}
    \item{} <bullet — translated, keyword-injected, escaped>
    % 2–3 bullets per project; repeat block per project (top 3–4)
\end{cvbullets}
```

</format>

---

#### 7.9 Concours & Hackathons (optional)

<agent_instruction>
Include only if: (a) contest result directly reinforces a profile/experience claim, OR (b) CV has space and the award is verifiable. Data source: `competitions[]` array (keys: `title`, `subtitle`, `date`, `bullets`). Title ≤ 45 chars (§3d). Select 1–2 most JD-relevant bullets per entry.
</agent_instruction>

<format id="section-competitions">

```latex
\section*{Concours \& Hackathons}

\cventrycontest{<title — ≤45 chars, translated, escaped>}
               {<subtitle — organizer/context, translated, escaped>}
               {<date>}
\begin{cvbulletscontest}
    \item{} <bullet — translated, keyword-injected, escaped>
    % 1–2 bullets per entry
\end{cvbulletscontest}
```

</format>

---

#### 7.10 Hobbies (optional)

<agent_instruction>Apply inclusion criteria and format from `modes/writing.md §10`.</agent_instruction>

---

### 8. Final Verification

<rules id="final-check">

#### 8.1 6-second scan test

The following 5 elements must be visible in 6 seconds without reading the CV:
name · headline · most recent education · most recent or most relevant experience/project · 2–3 keywords from the posting.

If any is not immediately visible → reorganise the visual hierarchy.

#### 8.2 Full CV checklist

1. Does it fit on 1 A4 page without reducing font size or margins?
2. Do all dates follow `{mon. YYYY -- mon. YYYY}`?
3. Do all `\cventry` commands have 4 arguments with location in `#3`?
4. Are there any `---` or `·` in document content outside permitted uses? → replace with comma or rephrase.
5. Are the 5 scan-test elements (§8.1) immediately visible?
6. Is the active palette the confirmed one? Are the other three commented out?
7. Does the file include `\pdfgentounicode=1` after `\pagestyle{empty}`?

</rules>

---

### 9. Fitting to 1 Page

<process id="overflow-fix">

If the CV overflows, intervene in this order:

| Step | Action |
|---|---|
| 1 | Columns: right overflows → `\columnratio{0.30}`; right too empty → `\columnratio{0.40}`. |
| 2 | Bullets: combine 2 with "et" or comma; reduce from 3 to 2 bullets in less critical projects. |
| 3 | Titles: if `#1` of `\cventry` clashes with the date → move context to `#2`. |
| 4 | Spacing: `\titlespacing*{\section}{0pt}{6pt}{3pt}` → `{5pt}{2pt}`; `\vspace{-4pt}` → `\vspace{-5pt}`. |
| 5 | Vie Étudiante: shorten or reduce from 3 to 2 entries. |

**Never:** font < 10pt · margins < 1 cm top/bottom or < 1.2 cm left/right · `\linespread`.

</process>

---

## Canva Flow (optional)

<agent_instruction>
Check `config/profile.md` for `cv.canva_resume_design_id`. If absent, skip to LaTeX. If present, ask:
</agent_instruction>

<user_prompt>
Which CV format would you like?
1. LaTeX/PDF — fast, ATS-optimized (default)
2. Canva CV — visual, design-preserving
</user_prompt>

<process>

<step id="1" name="Duplicate base design">

- `export-design` the base design (`cv.canva_resume_design_id`) as PDF → get download URL
- `import-design-from-url` with that URL → creates editable duplicate
- Record the new `design_id`

<validation>
If `import-design-from-url` fails → fall back to LaTeX pipeline and notify user.
</validation>
</step>

<step id="2" name="Map design structure">

- `get-design-content` on the duplicate → returns all richtext elements
- Map elements to CV sections by content matching:
  - Candidate name → header
  - "Summary" / "Professional Summary" → summary
  - Company names from `experience[].company` → experience sections
  - Degree/school names → education
  - Skill keywords → skills
- Record character count for each element (needed for budget enforcement in Step 3)

<validation>
If mapping fails → show user what was found and ask for manual guidance.
</validation>
</step>

<step id="3" name="Generate tailored content">

<agent_instruction>
Apply the same tailoring logic as the LaTeX flow:
- Rewrite Professional Summary with JD keywords + exit narrative
- Reorder experience bullets by JD relevance
- Select top competencies matching JD requirements
- Inject keywords naturally

Enforce character budget: each replacement MUST be within ±15% of the original element's character count (recorded in Step 2). Condense if over budget.
</agent_instruction>
</step>

<step id="4" name="Apply edits and verify layout">

a. `start-editing-transaction` on the duplicate
b. `perform-editing-operations` — `find_and_replace_text` for each section (summary, experience bullets, skills, projects)
c. **Reflow layout** after replacements:
   - Read updated element positions/dimensions from the operation response
   - For each experience section top-to-bottom: `end_y = top + height`
   - Move next section's elements to `end_y + original_gap` (typically ~30px) using `position_element`
d. **Verify layout** — `get-design-thumbnail` (page_index=1); check for overlapping text, uneven spacing, cut-off text; fix with `position_element` / `resize_element` / `format_text` until clean

<validation>
Show the user the final preview thumbnail.
</validation>

<user_prompt>
Here's the preview. Does this look good? I'll save it once you confirm.
</user_prompt>

e. `commit-editing-transaction` — ONLY after user approval

<validation>
If `find_and_replace_text` finds no matches → try broader substring matching. Always provide the Canva design URL so the user can edit manually if auto-edit fails.
</validation>
</step>

<step id="5" name="Export and download PDF">

a. `export-design` the duplicate as PDF (format: `a4` or `letter` based on JD location)
b. Download immediately:

```bash
curl -sL -o "output/cv-{candidate}-{company}-canva-{YYYY-MM-DD}.pdf" "{download_url}"
```

c. Verify:

```bash
file output/cv-{candidate}-{company}-canva-{YYYY-MM-DD}.pdf
```

Must show "PDF document". If XML/HTML → URL expired; re-export and retry.

</step>

</process>

<output>
- `output/cv-{candidate}-{company}-canva-{YYYY-MM-DD}.pdf`
- Canva design URL (for manual tweaking)
- File size confirmation
</output>

<completion>
PDF saved to `output/cv-{candidate}-{company}-canva-{YYYY-MM-DD}.pdf` ({size}). Canva design: {url} — open it anytime for manual adjustments.
</completion>
