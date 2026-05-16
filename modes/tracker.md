# Mode: tracker — Application Tracker

<purpose>
Read `data/applications.md` and display the pipeline status with summary statistics. Update rows on request.
</purpose>

<rules>
- Canonical statuses are defined in `states.yml` — never invent new ones.
- NEVER add new rows here; new entries go through `batch/tracker-additions/` + `merge-tracker.mjs`.
- You MAY edit existing rows to update status or notes.
- No markdown bold (`**`) in the status field; no dates in the status field; no extra text (use the notes column).
</rules>

<reference id="tracker-format">
```
| # | Date | Company | Role | Score | Status | PDF | Report | Notes |
```
</reference>

<reference id="status-flow">
`Evaluated` → `Applied` → `Responded` / `Contact` → `Interview` → `Offer` / `Rejected` / `Discarded` / `SKIP`

- `Applied` — candidate sent the application
- `Responded` — recruiter/company reached out; candidate responded (inbound)
- `Contact` — candidate proactively reached out (outbound, e.g. LinkedIn message)
</reference>

<process>

<step id="1" name="read-tracker">
<agent_instruction>
Read `data/applications.md`. If the file is missing, inform the user and offer to create it.
</agent_instruction>
</step>

<step id="2" name="display">
<agent_instruction>
Render the full tracker table, then print the summary block below it.
</agent_instruction>
<format>
**Pipeline summary**
- Total: {N}
- By status: {status: count, …}
- Average score: {X.X}/5
- PDF generated: {X}%
- Report generated: {X}%
</format>
</step>

<step id="3" name="update-on-request">
<agent_instruction>
If the user asks to update a status or note, edit the matching row in `data/applications.md` directly. Use only canonical statuses from `states.yml`. Confirm the change with one line.
</agent_instruction>
<validation>
Status value must exist in `states.yml`. Reject anything else and list valid options.
</validation>
</step>

</process>

<completion>
After displaying the tracker and summary, ask:
<user_prompt>
Want to update any status, add a note, or jump to a specific application?
</user_prompt>
</completion>
