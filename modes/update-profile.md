# Mode — Update Profile: Edit config/profile.md After Initial Setup

## Purpose

Make targeted changes to `config/profile.md` after it was created by `onboard`. Covers two types of update:

- **YAML edits** — target roles, compensation, narrative, languages, awards, contact details
- **Trajectory edits** — adding, revising, or removing entries in the markdown body sections (education, experience, projects, associations, competitions, training)

This mode never rewrites the whole file. It reads the current state, applies only the requested change, shows a preview, and writes on explicit confirmation.

After any update, offer to regenerate `config/cv.md` via `ingest` — but only run it if the user confirms.

---

## Inputs and Output

| | Path |
|---|---|
| Source | `config/profile.md` (read current state first) |
| Output | `config/profile.md` (targeted edit in place) |

**Always read the full file before editing.** Never write a change that was derived from memory or a previous conversation — read the actual current content, then apply the diff.

---

## Detecting What the User Wants to Change

The user may trigger this mode explicitly ("update my profile") or describe a specific change ("change my salary target", "add a new project", "update my headline"). In either case:

1. If the request is specific, go directly to the relevant section below.
2. If the request is vague ("update my profile", "I want to change some things"), show the current state of the main YAML fields first and ask what to change:

```
Here's what your profile currently has:

**Target roles:** [list from target_roles.primary]
**Archetypes:** [list from target_roles.archetypes]
**Headline:** [narrative.headline]
**Compensation:** [compensation.target_range] ([compensation.currency]) · min: [compensation.minimum]
**Location flexibility:** [compensation.location_flexibility]
**Languages:** [list]
**Proof points:** [count] entries

**Trajectory sections:**
- Education: [N entries]
- Skills: [filled / empty]
- Experience: [N entries]
- Associations: [N entries]
- Projects: [N entries]
- Competitions: [N entries]
- Training: [N entries]

What would you like to change?
```

---

## YAML Edits

### Target Roles

Triggered by: "change my target roles", "add a role", "remove a role", "I'm now targeting X", "update archetypes"

1. Read the current `target_roles` block.
2. Show it in full.
3. Ask what to change — new titles, fit level adjustments, level changes, additions, removals.
4. Draft the updated block and show it:

```
Updated target_roles:

---
[full updated YAML block]
---

Confirm to save, or tell me what to adjust.
```

5. Write on explicit confirmation. Update only the `target_roles` keys — do not touch anything else.

**After saving:** ask whether to update `config/portals.yml` title filters to match the new roles.

---

### Narrative (Headline, Exit Story, Superpowers, Proof Points)

Triggered by: "update my headline", "change my story", "add a proof point", "update my differentiators", "rewrite my exit story"

1. Read the current `narrative` block.
2. Show the specific field being changed (not the whole block if only one field is targeted).
3. If the user wants to change the **headline or exit story**: ask for the new text, then draft it with light polish (apply the same rules as `ingest.md` — no first-person, no "passionate about", no em-dashes).
4. If the user wants to **add a proof point**: ask for the achievement in plain language, then format it:

```yaml
- name: "[Short label]"
  hero_metric: "[One sentence — most impressive verifiable outcome, metric-led if possible]"
```

5. If the user wants to **add or edit superpowers**: treat each as a distinct differentiator — concrete and specific, not generic ("strong quantitative background" is fine; "good communicator" is not).
6. Show the full proposed `narrative` block before writing. Write on explicit confirmation.

---

### Compensation

Triggered by: "update my salary", "change my minimum", "I'm now open to remote", "update location flexibility"

1. Read the current `compensation` block and show it.
2. Apply the requested change. For the walk-away minimum, confirm that it will remain private.
3. Show the updated block:

```
Updated compensation:

---
[full updated YAML block]
---

Confirm to save.
```

4. Write on explicit confirmation.

---

### Contact Details and Identity

Triggered by: "update my email", "add a phone number", "change my location", "new LinkedIn URL"

Read the relevant `candidate.*` or `location.*` keys, apply the change, show the affected lines, write on confirmation.

---

### Languages

Triggered by: "I passed my DELF", "add a language", "update my English level", "remove German"

1. Read the `languages` array.
2. Add, update, or remove the specified entry.
3. Show the full updated `languages` block before writing.
4. Write on confirmation.

---

### Awards and Competitions (YAML)

Triggered by: "I won a competition", "add an award", "update my prizes"

1. Read the `awards_and_competitions` block.
2. Collect the new entry details: name, category, date, result, context.
3. Draft the new YAML entry and show it:

```yaml
- name: "[Prize name — Organization]"
  category: "hackathon / research_competition / case_competition / other"
  date: "YYYY-MM"
  result: "[Ranking or prize description]"
  context: "[1 sentence: format, number of teams, your role]"
```

4. Add it to the block and show the full updated `awards_and_competitions` section before writing. Write on confirmation.

---

## Trajectory Edits (Markdown Body)

### Adding a New Entry

Triggered by: "I just finished an internship", "add a new project", "I joined a new club", "add a course"

For new trajectory content, two paths depending on what the user provides:

**If the user has a document** (mentions a file in `sources/`):
- Process it using the full extraction and review logic from `modes/analyze-sources.md`
- Draft the entry, enter the review loop, write on approval
- Offer a YAML update (proof point or award) if warranted

**If the user describes it in conversation**:
- Collect the necessary fields via structured questions (use the appropriate question set from `modes/onboard.md` Phase 2, Step 3, for the relevant section)
- Draft the entry using the appropriate template from `modes/analyze-sources.md`
- Enter the review loop, write on approval

In both cases: write to the correct section (`SECTION 1`, `SECTION 4`, etc.), in correct chronological order. Never append blindly to the end of the file.

---

### Revising an Existing Entry

Triggered by: "update my Air Liquide entry", "the project description is wrong", "add a metric to X", "rewrite the bullets for Y"

1. Read the full current entry from `config/profile.md` (find it by title/company name).
2. Show it verbatim.
3. Apply the requested change — targeted edit only. If the user asks to add a metric, add it; don't rewrite the whole entry.
4. Show the full revised entry side by side with the current one if the change is significant, or just show the revised entry if it is a minor edit.
5. Write on explicit confirmation. Replace only the entry block — from its `### [Title]` heading to the next `---` separator. Never touch anything outside that block.

---

### Removing an Entry

Triggered by: "remove the X entry", "delete my internship at Y", "take out the Z project"

1. Find and show the entry in full.
2. Ask for explicit confirmation:
   > "This will permanently remove the [Title] entry from profile.md. Confirm?"
3. Remove only that entry block on confirmation. Never remove section headers or adjacent entries.

---

### Updating a META-INSTRUCCIÓN

Triggered by: "change the pairing rule for X", "update the framing for Y entry", "this entry should always appear with Z"

1. Find the entry and show its current `[META-INSTRUCCIÓN]` block.
2. Apply the change — this is a behavioral directive for `ingest`, not visible in the CV.
3. Show the updated META-INSTRUCCIÓN and confirm before writing.

---

## Rules

- **Read before writing** — always read the current file state before making any edit.
- **Targeted edits only** — never rewrite the whole file. Change only the specific key or entry block the user asked about.
- **No writes without confirmation** — show the proposed change in full and wait for explicit approval at every step.
- **Preserve structure** — do not alter section headers, separators (`---`), or any content outside the targeted block.
- **No fabrication** — if the user provides a vague update ("my salary is now higher"), ask for the specific number rather than inferring.
- **After any YAML edit**, check whether `config/portals.yml` or `config/strategy.md` should also be updated (e.g., a role change may affect portal search filters or archetype scoring).

---

## After Every Update

Once all changes in the session are saved, summarize what changed and offer to regenerate the CV:

> "Done — [brief description of what changed / list of changes if multiple].
>
> Want me to regenerate your CV now with the updated profile?"

If the user confirms, run `modes/ingest.md`. If they decline or don't respond clearly, do nothing — they can trigger it later with `/career-ops ingest`.
