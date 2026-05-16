# Mode — Update Profile: Edit config/profile.md After Initial Setup

## Purpose

Make targeted changes to `config/profile.md` after it was created by `onboard`. Covers two types of update:

- **YAML edits** — target roles, compensation, narrative, languages, awards, contact details, strategy
- **Trajectory edits** — adding, revising, or removing entries in the YAML arrays (education, experience, projects, student_life, competitions, skills, additional_training)

This mode never rewrites the whole file. It reads the current state, applies only the requested change, shows a preview, and writes on explicit confirmation.

After any update, summarize what changed. The profile is the CV — changes take effect immediately for the next `pdf` run.

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

**Note on archetype fields:** Each entry in `target_roles.archetypes` also has `thematic_axes` and `value_proposition` keys — these can be updated independently (e.g., to sharpen the positioning for a specific archetype without changing its fit level).

**After saving:** ask whether to update `config/portals.yml` title filters to match the new roles.

---

### Narrative (Headline, Exit Story, Superpowers, Proof Points)

Triggered by: "update my headline", "change my story", "add a proof point", "update my differentiators", "rewrite my exit story"

1. Read the current `narrative` block.
2. Show the specific field being changed (not the whole block if only one field is targeted).
3. If the user wants to change the **headline or exit story** (`exit_story` for the short version used in the PDF, `exit_story_long` for the extended LinkedIn/interview version): ask for the new text, then draft it with light polish (no first-person, no "passionate about", no em-dashes). Also check whether `exit_story_usage` (the usage notes for pdf, star, and draft_answers) needs updating.
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

1. Read the current `compensation` block and show it. The block also includes `market_context` (market rate notes) and `approach` (negotiation guidance) — these can be updated too if the user requests it.
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

Triggered by: "update my email", "add a phone number", "change my location", "new LinkedIn URL", "update my location policy"

Read the relevant `candidate.*` or `location.*` keys, apply the change, show the affected lines, write on confirmation. The `location` block also contains `in_forms` (text to use when filling application forms) and `scoring_rules` (how location constraints affect offer scoring) — these can be updated if the user's situation changes (e.g., moving cities, changing remote preference).

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

## Trajectory Edits (YAML Arrays)

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

In both cases: append to the correct YAML array (`education`, `experience`, `projects`, `student_life`, `competitions`, `skills`, `additional_training`), in correct chronological position. Never append blindly to the end of the array without checking order.

---

### Revising an Existing Entry

Triggered by: "update my Air Liquide entry", "the project description is wrong", "add a metric to X", "rewrite the bullets for Y"

1. Read the full current entry from `config/profile.md` (find it by title/company name).
2. Show it verbatim.
3. Apply the requested change — targeted edit only. If the user asks to add a metric, add it; don't rewrite the whole entry.
4. Show the full revised entry side by side with the current one if the change is significant, or just show the revised entry if it is a minor edit.
5. Write on explicit confirmation. Locate the matching YAML object in the array by its `title:` or `company:` key. Replace only that object. Never touch other objects in the same array.

---

### Removing an Entry

Triggered by: "remove the X entry", "delete my internship at Y", "take out the Z project"

1. Find and show the entry in full.
2. Ask for explicit confirmation:
   > "This will permanently remove the [Title] entry from profile.md. Confirm?"
3. Remove only that YAML object from its array on confirmation. Never remove other objects in the same array.

---

### Strategy

Triggered by: "update my negotiation script", "add a deal breaker", "change my framing for consulting roles", "update my fit signals", "refine my cross-cutting advantage", "change my AI positioning"

The `strategy` key lives inside `config/profile.md` (not a separate file). It contains:

- `adaptive_framing` — per role-type emphasis and proof sources. Add, update, or remove a role-type entry.
- `cross_cutting_advantage` — free-text block describing the candidate's unique positioning. Update if the narrative evolves.
- `ai_positioning` — how to frame AI tool use honestly. Update if new tools or projects change the story.
- `portfolio` — public URL and notes. Update when a portfolio goes live.
- `negotiation_scripts` — named scripts for salary discussions. Add new scripts or update existing ones.
- `deal_breakers` — hard constraints the user would reject an offer over. Add or remove items.
- `fit_signals` — what energizes vs. drains the user. Add or remove items.

Procedure for any strategy change:
1. Read the relevant sub-key and show it.
2. Apply the requested change.
3. Show the full updated sub-key before writing.
4. Write on explicit confirmation.

**After saving:** check whether the change affects `config/portals.yml` scoring logic (e.g., a new deal-breaker may warrant adding a negative filter).

---

## Rules

- **Read before writing** — always read the current file state before making any edit.
- **Targeted edits only** — never rewrite the whole file. Change only the specific key or entry block the user asked about.
- **No writes without confirmation** — show the proposed change in full and wait for explicit approval at every step.
- **Preserve structure** — do not alter section headers, separators (`---`), or any content outside the targeted block.
- **No fabrication** — if the user provides a vague update ("my salary is now higher"), ask for the specific number rather than inferring.
- **After any YAML edit**, check whether `config/portals.yml` should also be updated (e.g., a role change may affect portal search filters or archetype scoring in `strategy.adaptive_framing`).

---

## After Every Update

Once all changes in the session are saved, summarize what changed:

> "Done — [brief description of what changed / list of changes if multiple].
>
> Your profile is updated. Run `/career-ops pdf` with a job description to generate a tailored CV."
