# Mode: parallel-sources — Parallel Profile Enrichment from sources/

<purpose>
Batch-process multiple source documents simultaneously. Each worker agent reads one file, applies the recruiter lens from `modes/analyze-sources.md`, and returns a draft profile entry. The conductor presents drafts for sequential user review; nothing is written until the user approves each entry individually.
</purpose>

<rules>
- Workers are read-only — they NEVER write to `config/profile.md`
- The conductor writes ONLY on explicit user approval, one entry at a time
- All entry text must be in English
- Workers are stateless and safe to re-run if any fail
- Extraction rules, YAML templates, recruiter lens, and quality checks all live in `modes/analyze-sources.md` — reference, do not repeat
</rules>

---

## Conductor Steps

### Step 1 — Inventory and Deduplicate

<process>
<step id="1" name="Inventory and deduplicate">
<agent_instruction>
List all files in `sources/` (excluding `README.md`). Read `config/profile.md` and collect every `title:` and `company:` value from all CV arrays (`experience`, `student_life`, `projects`, `competitions`, `additional_training`).

Classify each source file:
- **NEW** — no matching title/company → spawn an agent
- **ALREADY ANALYZED** — matching title or company found → skip
- **PROBABLE MATCH** — partial/fuzzy match → flag for user confirmation before spawning
</agent_instruction>

<user_prompt>
Sources in sources/:

Will process (NEW — {n} files):
  1. {filename} — probable type: {Project|Experience|Association|Competition|Training}
  2. {filename} — probable type: ...

Already analyzed (skipping):
  - {filename} → existing entry: "{title/company}" in [{yaml_key}]

Confirm to spawn one reader agent per new file?
</user_prompt>

<agent_instruction>
For probable matches, ask separately before including.
</agent_instruction>
</step>

### Step 2 — Spawn Worker Agents

<step id="2" name="Spawn one reader agent per confirmed new file">
<agent_instruction>
Spawn all workers in parallel (background). Workers read and draft only — they do NOT write to any file.

Worker prompt (fill in `{filename}` before spawning):
</agent_instruction>

```
You are a profile enrichment agent. Read one source document and produce a draft profile entry.
DO NOT write to any file. Return only a JSON result.

SOURCE FILE: sources/{filename}

READ THESE FILES FIRST:
  config/profile.md         — existing entries (for language and YAML structure)
  modes/analyze-sources.md  — extraction rules, recruiter lens, YAML entry templates

TASK:
1. Read sources/{filename} using the reading strategy in modes/analyze-sources.md §Step 2.5.
   (Short docs: read in full. Long docs: cover → summary → conclusions → results → intro.)
2. Classify the document type: Project | Experience | Association | Competition | Training | Other
3. Determine the target YAML array per the type table in modes/analyze-sources.md §Step 2.
4. Apply the recruiter lens from §Step 3 — extract only what a non-specialist hiring manager finds useful. Suppress all technical minutiae.
5. Extract facts per §Step 4 using the template for the detected type.
6. Draft the YAML entry object using the correct template from §Step 6 of modes/analyze-sources.md.
7. Identify gaps using the question bank from §Step 5:
   - BLOCKING: facts absent from the document (entry cannot be finalized without them)
   - ENHANCING: optional improvements (entry is usable without them)

Return this JSON (no other output):
{
  "filename": "{filename}",
  "type": "{type}",
  "target_section": "{yaml_key}",
  "draft": "{full YAML entry object — escape newlines as \\n}",
  "blocking_gaps": ["{question1}", "{question2}"],
  "enhancing_questions": ["{q1}", "{q2}"],
  "confidence": "{high|medium|low|failed}",
  "notes": "{uncertain classification, corrupted PDF, thin contribution, etc.}"
}

Confidence:
  high   = complete and usable with no user input needed
  medium = usable but enhancing questions would strengthen it
  low    = has blocking gaps — user must answer before finalizing
  failed = unreadable or contribution too thin for an entry

DO NOT write to config/profile.md.
```
</step>

### Step 3 — Collect and Sort Drafts

<step id="3" name="Collect and sort">
<agent_instruction>
Wait for all workers to complete. Sort results:
1. `high` — ready for immediate review
2. `medium` — usable draft, optional questions
3. `low` — blocking gaps must be answered first
4. `failed` — handle separately at the end (Step 5)
</agent_instruction>
</step>

### Step 4 — Sequential Review Loop

<step id="4" name="Review and write one entry at a time">
<agent_instruction>
Present drafts one at a time in sorted order.
</agent_instruction>

<user_prompt>
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
</user_prompt>

<agent_instruction>
Apply user edits and show the full revised entry each round (never just the diff). Wait for explicit approval or skip.

On approval:
1. Run the Quality Check from `modes/analyze-sources.md §Quality Check Before Appending`.
2. Append the approved YAML object to the correct array in `config/profile.md`.
3. Confirm: `Added to profile.md → [{target_section}] array.`
4. Check if a metadata update is warranted (strong proof point or new award) — follow `modes/analyze-sources.md §Profile Metadata Updates`.
</agent_instruction>
</step>

### Step 5 — Handle Failed Workers

<step id="5" name="Failed files">
<user_prompt>
{n} file(s) could not be processed automatically:
  - {filename}: {reason}

Options:
  1) Paste the key facts from this document and I'll draft the entry manually.
  2) Skip — process it later with analyze-sources.
</user_prompt>
</step>

### Step 6 — Final Summary

<step id="6" name="Summary">
<completion>
Done.
  {n} entries added to profile.md
  {m} entries skipped
  {k} files failed (listed above)

Your profile is updated. Run `/career-ops pdf` with a job description to generate a tailored CV.
</completion>
</step>

</process>

<output>
- Approved YAML entries appended to the correct arrays in `config/profile.md`
- Per-entry confirmation messages during the review loop
- Final summary of added / skipped / failed files
</output>
