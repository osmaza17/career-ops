# Mode: followup -- Follow-up Cadence Tracker

<purpose>
Track follow-up cadence for active applications. Flag overdue follow-ups, extract contacts from notes, and generate tailored follow-up email/LinkedIn drafts using report context.
</purpose>

<rules>
- Never record a follow-up draft as sent — only record ones the user confirms they sent.
- Never generate another follow-up for cold entries (followupCount >= 2); suggest closing instead.
- Never use "just checking in", "just following up", "touching base", or "circling back" in any draft.
- Lead with value, not with the ask. Keep all email drafts under 150 words.
- Read candidate name, proof points, and identity from `config/profile.md` at draft time — never hardcode.
</rules>

<reference id="inputs">

| File | Purpose |
|------|---------|
| `data/applications.md` | Application tracker |
| `data/follow-ups.md` | Follow-up history (created on first use) |
| `output/reports/` | Evaluation reports (for company context in drafts) |
| `config/profile.md` | Identity, proof points, CV sections |

</reference>

<reference id="cadence-rules">

| Status | First follow-up | Subsequent | Max attempts |
|--------|----------------|------------|-------------|
| Applied | 7 days after application | Every 7 days | 2 (then mark cold) |
| Responded | 1 day (urgent reply) | Every 3 days | No limit |
| Interview | 1 day after (thank-you) | Every 3 days | No limit |

Defaults can be overridden via `node followup-cadence.mjs --applied-days N`.

</reference>

<reference id="urgency-indicators">

| Label | Meaning |
|-------|---------|
| **URGENT** | Company replied — respond within 24 hours |
| **OVERDUE** | Follow-up is past due |
| **waiting (X days)** | On track, follow-up scheduled |
| **COLD** | 2+ follow-ups sent, suggest closing |

</reference>

---

## Process

<process>

<step id="1" name="Run Cadence Script">

<agent_instruction>
Execute `node followup-cadence.mjs` and parse the JSON output.

Key fields in the output:
- `metadata` — analysis date, total tracked, actionable/overdue/urgent/cold/waiting counts
- `entries` — per-application: company, role, status, days since application, followup count, urgency, next follow-up date, extracted contacts, report path
- `cadenceConfig` — active cadence rules
</agent_instruction>

<agent_instruction>
If no actionable entries exist, stop and show:
</agent_instruction>

<user_prompt>
No active applications to follow up on. Apply to some roles first with `/career-ops` and come back when they're aging.
</user_prompt>

</step>

<step id="2" name="Display Dashboard">

<agent_instruction>
Show a cadence dashboard sorted by urgency (urgent > overdue > waiting > cold). Use urgency indicators from `<reference id="urgency-indicators">`.
</agent_instruction>

<format>
```
Follow-up Cadence Dashboard — {date}
{N} applications tracked, {N} actionable

| # | Company | Role | Status | Days | Follow-ups | Next | Urgency | Contact |
```
</format>

</step>

<step id="3" name="Generate Follow-up Drafts">

<agent_instruction>
Generate drafts only for **overdue** or **urgent** entries. For each, read the linked report (`reportPath`) for company context and `config/profile.md` for proof points and candidate identity.
</agent_instruction>

<section name="First follow-up (followupCount == 0)">

<agent_instruction>
Generate a 3–4 sentence email:
1. Reference the specific role + application date.
2. One concrete value-add from the report's Block B match or a proof point from `config/profile.md`. Quantify if possible.
3. Soft ask + availability (offer a specific time window).
4. (Optional) Brief mention of a relevant recent project or achievement.

Also reference something specific to that company from report Block A.
</agent_instruction>

<format>
```
Subject: Re: {Role} — {Company}

Hi [contact name or "team"],

[Sentence 1: role + application date]
[Sentence 2: specific value-add with quantification]
[Sentence 3: soft ask + time window]
[Optional Sentence 4: recent project/achievement]

Best,
[Name from config/profile.md]
```
</format>

</section>

<section name="LinkedIn follow-up (no email contact found)">

<agent_instruction>
3 sentences, 300-character max. Structure: hook specific to company → proof point → soft ask.
Suggest the user run `/career-ops contact {company}` to find the right person first.
</agent_instruction>

</section>

<section name="Second follow-up (followupCount == 1)">

<agent_instruction>
Shorter than the first (2–3 sentences). Take a new angle: share a relevant insight, article, or project update. Do not repeat the first follow-up's content. Still reference the role specifically.
</agent_instruction>

</section>

<section name="Cold application (followupCount >= 2)">

<agent_instruction>
Do not generate another follow-up draft. Show this message instead:
</agent_instruction>

<user_prompt>
This application has had {N} follow-ups with no response. Consider:
- Updating status to `Discarded` if the role seems filled
- Trying a different contact via `/career-ops contact`
- Keeping in `Applied` status but deprioritizing
</user_prompt>

</section>

</step>

<step id="4" name="Present Drafts">

<format>
```
## Follow-up: {Company} — {Role} (#{num})

**To:** {email or "No contact found — run `/career-ops contact` first"}
**Subject:** {subject line}
**Days since application:** {N}
**Follow-ups sent:** {N}
**Channel:** Email / LinkedIn

{draft text}
```
</format>

</step>

<step id="5" name="Record Follow-ups">

<agent_instruction>
Only record follow-ups the user confirms they actually sent.

If `data/follow-ups.md` doesn't exist, create it:
</agent_instruction>

<format>
```markdown
# Follow-up History

| # | App# | Date | Company | Role | Channel | Contact | Notes |
|---|------|------|---------|------|---------|---------|-------|
```
</format>

<agent_instruction>
Append a row with: next sequential `#`, application number, today's date, company, role, channel (Email / LinkedIn / Other), contact name, and a brief note (e.g., "First follow-up, referenced H₂ supply chain project").

Optionally update the Notes column in `data/applications.md` with "Follow-up {N} sent {YYYY-MM-DD}".
</agent_instruction>

</step>

<step id="6" name="Summary">

<completion>
**Follow-up Dashboard** ({date})
- {N} applications being tracked
- {N} overdue — drafts generated above
- {N} urgent — respond today
- {N} waiting — next follow-up dates shown
- {N} cold — consider closing

Review the drafts above and tell me which ones you've sent so I can record them.
</completion>

</step>

</process>

<output>
- Cadence dashboard table (Step 2)
- Follow-up email/LinkedIn drafts for overdue and urgent entries (Steps 3–4)
- Updated `data/follow-ups.md` row(s) after user confirmation (Step 5)
- Optional update to `data/applications.md` notes column (Step 5)
</output>
