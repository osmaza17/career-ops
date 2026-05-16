# Mode — Update Profile

<purpose>
Make targeted changes to `config/profile.md` after initial setup by `onboard`. Covers two update types:
- **YAML edits** — target roles, compensation, narrative, languages, awards, contact details, strategy
- **Trajectory edits** — adding, revising, or removing entries in YAML arrays (education, experience, projects, student_life, competitions, skills, additional_training)

This mode never rewrites the whole file. It reads current state, applies only the requested change, shows a preview, and writes on explicit confirmation. Changes take effect immediately for the next `pdf` run.
</purpose>

<rules>
- **Read before writing** — always read the full current file before any edit. Never derive a change from memory or a prior conversation.
- **Targeted edits only** — change only the specific key or entry block requested. Never rewrite the whole file.
- **No writes without confirmation** — show the proposed change in full and wait for explicit approval at every step.
- **Preserve structure** — do not alter section headers, separators (`---`), or content outside the targeted block.
- **No fabrication** — if the request is vague ("my salary is now higher"), ask for the specific value rather than inferring.
- **After any YAML edit** — check whether `config/portals.yml` also needs updating (e.g., role change → portal filters; deal-breaker → negative filter).
</rules>

---

## Inputs and Output

| | Path |
|---|---|
| Source | `config/profile.md` (read first) |
| Output | `config/profile.md` (targeted edit in place) |

---

## Detecting What the User Wants

<process>

<step id="1" name="Route the request">
<agent_instruction>
If the request is specific, go directly to the relevant section. If vague ("update my profile", "I want to change some things"), read config/profile.md and show the overview below, then ask what to change.
</agent_instruction>

<user_prompt>
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
</user_prompt>
</step>

</process>

---

## YAML Edits

### Target Roles

<section name="target-roles">

Triggered by: "change my target roles", "add/remove a role", "I'm now targeting X", "update archetypes"

<process>
<step id="1" name="Read and show">Read the current `target_roles` block in full.</step>
<step id="2" name="Collect changes">Ask what to change — new titles, fit levels, level changes, additions, removals. Note: each archetype entry also has `thematic_axes` and `value_proposition` keys that can be updated independently.</step>
<step id="3" name="Draft and preview">
<user_prompt>
Updated target_roles:

---
[full updated YAML block]
---

Confirm to save, or tell me what to adjust.
</user_prompt>
</step>
<step id="4" name="Write">
<validation>Explicit confirmation received.</validation>
<mapping>Write only `target_roles` keys in `config/profile.md`. Touch nothing else.</mapping>
</step>
<step id="5" name="Post-save check">
<agent_instruction>Ask whether to update `config/portals.yml` title filters to match the new roles.</agent_instruction>
</step>
</process>

</section>

---

### Narrative

<section name="narrative">

Triggered by: "update my headline", "change my story", "add a proof point", "update my differentiators", "rewrite my exit story"

<process>
<step id="1" name="Read">Read the current `narrative` block. Show only the specific field being changed unless the user targets the whole block.</step>
<step id="2" name="Apply change by type">

- **Headline / exit story** (`exit_story` for PDF, `exit_story_long` for LinkedIn/interview): ask for new text, draft with light polish (no first-person, no "passionate about", no em-dashes). Check whether `exit_story_usage` also needs updating.
- **Proof point addition**: ask for the achievement in plain language, then format as:

```yaml
- name: "[Short label]"
  hero_metric: "[One sentence — most impressive verifiable outcome, metric-led if possible]"
```

- **Superpowers**: treat each as a distinct, concrete differentiator — not generic ("strong quantitative background" ✓; "good communicator" ✗).

</step>
<step id="3" name="Preview">Show the full proposed `narrative` block before writing.</step>
<step id="4" name="Write">
<validation>Explicit confirmation received.</validation>
<mapping>Write only `narrative` keys in `config/profile.md`.</mapping>
</step>
</process>

</section>

---

### Compensation

<section name="compensation">

Triggered by: "update my salary", "change my minimum", "I'm now open to remote", "update location flexibility"

<process>
<step id="1" name="Read and show">Read and display the current `compensation` block. Note: `market_context` and `approach` sub-keys can also be updated on request.</step>
<step id="2" name="Apply change">Apply the requested change. For walk-away minimum, confirm it will remain private.</step>
<step id="3" name="Preview">
<user_prompt>
Updated compensation:

---
[full updated YAML block]
---

Confirm to save.
</user_prompt>
</step>
<step id="4" name="Write">
<validation>Explicit confirmation received.</validation>
<mapping>Write only `compensation` keys in `config/profile.md`.</mapping>
</step>
</process>

</section>

---

### Contact Details and Identity

<section name="contact">

Triggered by: "update my email", "add a phone number", "change my location", "new LinkedIn URL", "update my location policy"

<process>
<step id="1" name="Read">Read the relevant `candidate.*` or `location.*` keys. Note: `location.in_forms` (text for application forms) and `location.scoring_rules` (offer-scoring constraints) can also be updated if the user's situation changes.</step>
<step id="2" name="Apply and preview">Show the affected lines with the change applied.</step>
<step id="3" name="Write">
<validation>Explicit confirmation received.</validation>
<mapping>Write only the targeted keys in `config/profile.md`.</mapping>
</step>
</process>

</section>

---

### Languages

<section name="languages">

Triggered by: "I passed my DELF", "add a language", "update my English level", "remove German"

<process>
<step id="1" name="Read">Read the `languages` array.</step>
<step id="2" name="Apply">Add, update, or remove the specified entry.</step>
<step id="3" name="Preview">Show the full updated `languages` block.</step>
<step id="4" name="Write">
<validation>Explicit confirmation received.</validation>
<mapping>Write only `languages` in `config/profile.md`.</mapping>
</step>
</process>

</section>

---

### Awards and Competitions (YAML)

<section name="awards">

Triggered by: "I won a competition", "add an award", "update my prizes"

<process>
<step id="1" name="Read">Read the `awards_and_competitions` block.</step>
<step id="2" name="Collect">Gather: name, category, date, result, context. Draft the new entry:

```yaml
- name: "[Prize name — Organization]"
  category: "hackathon / research_competition / case_competition / other"
  date: "YYYY-MM"
  result: "[Ranking or prize description]"
  context: "[1 sentence: format, number of teams, your role]"
```
</step>
<step id="3" name="Preview">Show the full updated `awards_and_competitions` section.</step>
<step id="4" name="Write">
<validation>Explicit confirmation received.</validation>
<mapping>Write only `awards_and_competitions` in `config/profile.md`.</mapping>
</step>
</process>

</section>

---

## Trajectory Edits (YAML Arrays)

### Adding a New Entry

<section name="add-entry">

Triggered by: "I just finished an internship", "add a new project", "I joined a new club", "add a course"

<process>
<step id="1" name="Determine source">

- **User has a document** (file in `sources/`): process using extraction and review logic from `modes/analyze-sources.md`. Draft the entry, enter the review loop, write on approval.
- **User describes in conversation**: collect fields via structured questions (use question sets from `modes/onboard.md` Phase 2 for the relevant section). Draft using templates from `modes/analyze-sources.md`. Enter review loop, write on approval.

</step>
<step id="2" name="Write">
<validation>Review loop approved.</validation>
<mapping>Append to the correct YAML array in `config/profile.md` in correct chronological order. Never append blindly to the end without checking order.</mapping>
</step>
</process>

</section>

---

### Revising an Existing Entry

<section name="revise-entry">

Triggered by: "update my Air Liquide entry", "the project description is wrong", "add a metric to X", "rewrite the bullets for Y"

<process>
<step id="1" name="Find and show">Read the full current entry from `config/profile.md` by title/company name. Show it verbatim.</step>
<step id="2" name="Apply targeted change">Edit only what was requested — do not rewrite the whole entry for a minor ask.</step>
<step id="3" name="Preview">Show the full revised entry. If the change is significant, show it alongside the original.</step>
<step id="4" name="Write">
<validation>Explicit confirmation received.</validation>
<mapping>Locate the matching YAML object by its `title:` or `company:` key. Replace only that object. Never touch other objects in the same array.</mapping>
</step>
</process>

</section>

---

### Removing an Entry

<section name="remove-entry">

Triggered by: "remove the X entry", "delete my internship at Y", "take out the Z project"

<process>
<step id="1" name="Find and show">Read and display the full entry.</step>
<step id="2" name="Confirm">
<user_prompt>This will permanently remove the [Title] entry from profile.md. Confirm?</user_prompt>
</step>
<step id="3" name="Write">
<validation>Explicit confirmation received.</validation>
<mapping>Remove only that YAML object from its array. Never remove other objects in the same array.</mapping>
</step>
</process>

</section>

---

### Strategy

<section name="strategy">

Triggered by: "update my negotiation script", "add a deal breaker", "change my framing for consulting roles", "update my fit signals", "refine my cross-cutting advantage", "change my AI positioning"

Sub-keys under `strategy` in `config/profile.md`:
- `adaptive_framing` — per role-type emphasis and proof sources
- `cross_cutting_advantage` — free-text unique positioning block
- `ai_positioning` — how to frame AI tool use honestly
- `portfolio` — public URL and notes
- `negotiation_scripts` — named scripts for salary discussions
- `deal_breakers` — hard constraints; reject an offer if triggered
- `fit_signals` — what energizes vs. drains the user

<process>
<step id="1" name="Read">Read the relevant sub-key and display it.</step>
<step id="2" name="Apply">Apply the requested change.</step>
<step id="3" name="Preview">Show the full updated sub-key.</step>
<step id="4" name="Write">
<validation>Explicit confirmation received.</validation>
<mapping>Write only the targeted `strategy` sub-key in `config/profile.md`.</mapping>
</step>
<step id="5" name="Post-save check">
<agent_instruction>Check whether the change affects `config/portals.yml` scoring logic (e.g., new deal-breaker may warrant a negative filter).</agent_instruction>
</step>
</process>

</section>

---

## After Every Update

<completion>
Once all changes in the session are saved:

> "Done — [brief description of what changed / list of changes if multiple].
>
> Your profile is updated. Run `/career-ops pdf` with a job description to generate a tailored CV."
</completion>
