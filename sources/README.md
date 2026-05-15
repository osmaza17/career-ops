# sources/ — Raw Academic Trajectory Documents

This folder holds the source files that feed the `analyze-sources` pipeline step.

**Only `modes/analyze-sources.md` reads from this folder.** No other mode, script, or process accesses it.

---

## What goes here

| Type | Examples |
|------|---------|
| Project reports | Academic consulting studies, technical engineering reports, case study deliverables |
| Internship descriptions | Stage summary docs, end-of-internship reports, mission letters |
| Association activity | Club/association role descriptions, event retrospectives, mandate summaries |
| Competitions | Case competition briefs, hackathon submissions, prize announcements |

Any format works: PDF, DOCX, Markdown, plain text. Paste the raw document — do not pre-edit it.

---

## Naming convention (recommended)

```
YYYY-MM_type_short-description.ext
```

Examples:
- `2024-03_project_air-liquide-h2-logistics.pdf`
- `2025-06_internship_bucarest54-civil-engineering.md`
- `2026-02_association_bdi-secretaire-general.md`
- `2024-11_competition_akademia-bankinter.pdf`

---

## Access policy

> **Only `modes/analyze-sources.md` may read files from this folder.**
>
> No evaluation, scan, PDF generation, or other career-ops mode touches these files. They are personal source documents, not part of the live CV pipeline until explicitly digested.

---

## Privacy note

These files may contain confidential information (company data, unpublished academic work, personal assessments). This folder is gitignored by default — do not commit its contents unless you have cleared it.
