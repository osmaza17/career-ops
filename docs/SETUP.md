# Setup Guide

## Prerequisites

- [Claude Code](https://claude.ai/code) installed and configured
- Node.js 18+ (for PDF generation and utility scripts)
- LaTeX compiler for PDF generation — install one of:
  - `cargo install tectonic` (preferred — cross-platform, auto-downloads packages)
  - MiKTeX (Windows): https://miktex.org/download
  - TeX Live (Linux/macOS): `sudo apt install texlive-full` or `brew install --cask mactex`
- (Optional) Go 1.21+ (for the dashboard TUI)

## Quick Start (5 steps)

### 1. Clone and install

```bash
git clone https://github.com/osmaza17/career-ops.git
cd career-ops
npm install
npx playwright install chromium   # Required for job liveness checking
```

### 2. Run onboarding

Start a Claude Code session in the project directory and run:

```
/career-ops onboard
```

(OpenCode users: `/career-ops-onboard`)

The onboard mode guides you through a structured interview to build `config/profile.md` — your identity, target roles, narrative, compensation, education, experience, projects, and skills. It also creates `data/applications.md` and offers to set up the portal scanner.

(Optional) Drop raw academic documents (internship reports, project briefs) into `sources/` before running onboard — it will extract and format CV entries from them automatically. Additional documents can be added later via `/career-ops analyze-sources`.

### 3. Configure portals

Run `/career-ops scan` to set up the job portal scanner. It will create `config/portals.yml` with:
- `title_filter.positive` keywords matching your target roles
- Companies you want to track in `tracked_companies`
- Customizable `search_queries` for your preferred job boards

### 4. Generate your CV

Once `config/profile.md` is complete (after onboarding), it IS your CV — no intermediate file:

```
/career-ops pdf
```

### 5. Start using

Open Claude Code in this directory:

```bash
claude
```

Then paste a job offer URL or description. Career-ops will automatically evaluate it, generate a report, create a tailored PDF, and track it.

## Available Commands

| Action | How |
|--------|-----|
| Evaluate an offer | Paste a URL or JD text |
| Search for offers | `/career-ops scan` |
| Process pending URLs | `/career-ops pipeline` |
| Generate a PDF | `/career-ops pdf` |
| Batch evaluate | `/career-ops batch` |
| Check tracker status | `/career-ops tracker` |
| Fill application form | `/career-ops apply` |

## Verify Setup

```bash
node cv-sync-check.mjs      # Check configuration
node verify-pipeline.mjs     # Check pipeline integrity
```

## Build Dashboard (Optional)

```bash
cd dashboard
go build -o career-dashboard .
./career-dashboard --path ..  # Opens TUI pipeline viewer
```
