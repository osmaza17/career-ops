---
name: career-ops
description: AI job search command center -- evaluate offers, generate CVs, scan portals, track applications
arguments: mode # Claude Code specific
user-invocable: true
argument-hint: "[scan | pdf | offer-analysis | offers-comparison | apply | batch | tracker | pipeline | contact | interview-prep | onboard | analyze-sources | intel-sweep | linkedin-optimizer]"
license: MIT
---

# career-ops -- Router

## Mode Routing

Determine the mode from `$mode`:

| Input | Mode |
|-------|------|
| (empty / no args) | `discovery` -- Show command menu |
| JD text or URL (no sub-command) | **`auto-pipeline`** |
| `offer-analysis` | `offer-analysis` |
| `offers-comparison` | `offers-comparison` |
| `contact` | `contact` |
| `interview-prep` | `interview-prep` |
| `pdf` | `pdf` |
| `tracker` | `tracker` |
| `pipeline` | `pipeline` |
| `apply` | `apply` |
| `scan` | `scan` |
| `batch` | `batch` |
| `patterns` | `patterns` |
| `followup` | `followup` |
| `onboard` | `onboard` |
| `analyze-sources` | `analyze-sources` |
| `intel-sweep` | `intel-sweep` |
| `linkedin-optimizer` | `linkedin-optimizer` |

**Auto-pipeline detection:** If `$mode` is not a known sub-command AND contains JD text (keywords: "responsibilities", "requirements", "qualifications", "about the role", "we're looking for", company name + role) or a URL to a JD, execute `auto-pipeline`.

If `$mode` is not a sub-command AND doesn't look like a JD, show discovery.

---

## Discovery Mode (no arguments)

Show this menu:

```
career-ops -- Command Center

Available commands:
  /career-ops {JD}      → AUTO-PIPELINE: evaluate + report + PDF + tracker (paste text or URL)
  /career-ops pipeline  → Process pending URLs from inbox (data/pipeline.md)
  /career-ops offer-analysis    → Evaluation only A-F (no auto PDF)
  /career-ops offers-comparison → Compare and rank multiple offers
  /career-ops contact           → LinkedIn power move: find contacts + draft message
  /career-ops interview-prep → Generate company-specific interview prep doc
  /career-ops pdf       → PDF only, ATS-optimized CV
  /career-ops tracker   → Application status overview
  /career-ops apply     → Live application assistant (reads form + generates answers)
  /career-ops scan      → Scan portals and discover new offers
  /career-ops batch     → Batch processing with parallel workers
  /career-ops patterns  → Analyze rejection patterns and improve targeting
  /career-ops followup  → Follow-up cadence tracker: flag overdue, generate drafts
  /career-ops onboard   → Initial profile setup (YAML + CV sections)
  /career-ops analyze-sources → Process single document and add to profile
  /career-ops intel-sweep     → Research 2+ companies in parallel
  /career-ops linkedin-optimizer → Optimize LinkedIn profile

Inbox: add URLs to data/pipeline.md → /career-ops pipeline
Or paste a JD directly to run the full pipeline.
```

---

## Context Loading by Mode

After determining the mode, load the necessary files before executing:

### Modes that require `_shared.md` + their mode file:
Read `modes/_shared.md` + `modes/{mode}.md`

Applies to: `auto-pipeline`, `offer-analysis`, `offers-comparison`, `pdf`, `contact`, `apply`, `pipeline`, `scan`, `batch`, `onboard`, `analyze-sources`

### Standalone modes (only their mode file):
Read `modes/{mode}.md`

Applies to: `tracker`, `interview-prep`, `patterns`, `followup`, `intel-sweep`, `linkedin-optimizer`

### Modes delegated to subagent:
For `scan`, `apply` (with Playwright), `pipeline`, and `auto-pipeline`: always launch one Agent per URL (Agent tool, `run_in_background: true`) with the content of `_shared.md` + `modes/offer-analysis.md` + `modes/pdf.md` injected into the subagent prompt. Never process URLs inline.

```
Agent(
  subagent_type="general-purpose",
  prompt="[content of modes/_shared.md]\n\n[content of modes/{mode}.md]\n\n[invocation-specific data]",
  description="career-ops {mode}"
)
```

Execute the instructions from the loaded mode file.
