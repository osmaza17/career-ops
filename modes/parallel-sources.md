# Mode: parallel-sources — Parallel Profile Enrichment from sources/

## Purpose

Process multiple source documents simultaneously. Each agent reads one document, applies the recruiter lens from `modes/analyze-sources.md`, and produces a draft `profile.md` entry. The conductor then presents all drafts to the user for sequential review and approval. Nothing is written until the user approves each entry individually.

**The split:** agents do the slow work (reading, classifying, drafting) in parallel. Writes happen one at a time, under user control.

## When to use

User has multiple new files in `sources/` to process. Running `analyze-sources` one document at a time would take a long time — use this to batch the reading phase.

---

## Conductor Steps

### Step 1 — Inventory and deduplicate

List all files in `sources/` (excluding `README.md`).

Read `config/profile.md`. Collect every `title:` and `company:` value from each CV array (`experience`, `student_life`, `projects`, `competitions`, `additional_training`).

Classify each source file:
- **NEW** — no matching title/company in any array → spawn an agent
- **ALREADY ANALYZED** — matching title or company found → skip (list at the end)
- **PROBABLE MATCH** — partial or fuzzy match → flag, ask user to confirm before spawning

Present inventory:

```
Sources in sources/:

Will process (NEW — {n} files):
  1. {filename} — probable type: {Project|Experience|Association|Competition|Training}
  2. {filename} — probable type: ...

Already analyzed (skipping):
  - {filename} → existing entry: "{title/company}" in [{yaml_key}]

Confirm to spawn one reader agent per new file?
```

For probable matches, ask separately before including.

### Step 2 — Spawn one reader agent per new file

For each confirmed new file, spawn a background agent. Workers read and draft — they do NOT write to any file.

Worker prompt (fill in filename before spawning):

```
You are a profile enrichment agent. Read one source document and produce a draft profile entry.
DO NOT write to any file. Return only a JSON result.

SOURCE FILE: sources/{filename}

READ THESE FILES FIRST:
  config/profile.md         existing entries (to understand language and YAML structure)
  modes/analyze-sources.md  extraction rules, recruiter lens, YAML entry templates

TASK:
1. Read sources/{filename} using the reading strategy in modes/analyze-sources.md §Step 2.5.
   (Short docs: read in full. Long docs: cover → summary → conclusions → results → intro.)
2. Classify the document type: Project | Experience | Association | Competition | Training | Other
3. Determine the target YAML array (experience / student_life / projects / competitions / additional_training) per the type table in modes/analyze-sources.md §Step 2.
4. Apply the recruiter lens from §Step 3 throughout — extract only what a non-specialist hiring
   manager would find useful. Suppress all technical minutiae.
5. Extract facts per §Step 4 using the template for the detected type.
6. Draft the YAML entry object using the correct template from §Step 6 of modes/analyze-sources.md.
7. Identify gaps:
   - BLOCKING: facts needed but absent from the document (entry cannot be finalized without them)
   - ENHANCING: optional improvements (entry is usable without them)
   Use the question bank from §Step 5 of modes/analyze-sources.md.

Return this JSON (no other output):
{
  "filename": "{filename}",
  "type": "{type}",
  "target_section": "{yaml_key — e.g. experience, projects, student_life, competitions, additional_training}",
  "draft": "{full YAML entry object as it would be appended to the target array — escape newlines as \\n}",
  "blocking_gaps": ["{question1}", "{question2}"],
  "enhancing_questions": ["{q1}", "{q2}"],
  "confidence": "{high|medium|low|failed}",
  "notes": "{any observations — uncertain classification, corrupted PDF, thin contribution, etc.}"
}

Confidence levels:
  high   = entry is complete and usable without any user input
  medium = entry is usable but has enhancing questions that would strengthen it
  low    = entry has blocking gaps — user must answer before it can be finalized
  failed = document unreadable or contribution too thin for an entry

DO NOT write to config/profile.md.
```

Spawn all workers in parallel (background).

### Step 3 — Collect and sort drafts

Wait for all workers to complete.

Sort results in this order:
1. `high` confidence first — ready for immediate review, no questions needed
2. `medium` — usable draft, optional questions
3. `low` — blocking gaps must be answered first
4. `failed` — handle separately at the end

### Step 4 — Sequential review loop

Present drafts one at a time, in sorted order. For each:

```
## Draft {n}/{total}: {filename} → {type} → {target_section}
Confidence: {high|medium|low}

{if blocking_gaps:}
⛔ Before we can finalize this entry, I need your input:
{numbered list of blocking gaps}

{if enhancing_questions:}
Optional (skip any):
{numbered list of enhancing questions}

{if notes:}
Note: {notes}

Draft entry:
---
{full draft entry}
---

Tell me what to change, or say "add it" to write to profile.md.
Say "skip" to move on without writing this entry.
```

Apply user edits, show the full revised entry each round (never just the diff). Wait for explicit approval or skip.

On approval:
1. Run the Quality Check from `modes/analyze-sources.md §Quality Check Before Appending`.
2. Append the approved YAML object to the correct array in `config/profile.md`.
3. Confirm: `Added to profile.md → [{target_section}] array.`
4. Check if a metadata update is warranted (strong proof point or new award) — follow `modes/analyze-sources.md §Profile Metadata Updates`.

### Step 5 — Handle failed workers

After all successful drafts are reviewed, present failed files:

```
{n} file(s) could not be processed automatically:
  - {filename}: {reason}

Options:
  A) Paste the key facts from this document and I'll draft the entry manually.
  B) Skip — process it later with analyze-sources.
```

### Step 6 — Final summary

```
Done.
  {n} entries added to profile.md
  {m} entries skipped
  {k} files failed (listed above)

Your profile is updated. Run `/career-ops pdf` with a job description to generate a tailored CV.
```

---

## Notes

- Workers apply all rules from `modes/analyze-sources.md` — recruiter lens, formatted CV entry templates, minor contribution handling.
- Workers NEVER write to `config/profile.md`. The conductor writes only on explicit user approval per entry.
- If a worker detects a thin contribution ("one team member among fifteen, no defined deliverable"), it returns `confidence: low` and surfaces the options from `modes/analyze-sources.md §Minor contribution handling` as part of its notes.
- Language rule: entries are always written in English.
- Workers are read-only and stateless — safe to re-run if any fail.
