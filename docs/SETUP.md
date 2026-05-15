# Setup Guide

## Prerequisites

- [Claude Code](https://claude.ai/code) installed and configured
- Node.js 18+ (for PDF generation and utility scripts)
- (Optional) Go 1.21+ (for the dashboard TUI)

## Quick Start (5 steps)

### 1. Clone and install

```bash
git clone https://github.com/osmaza17/career-ops.git
cd career-ops
npm install
npx playwright install chromium   # Required for job liveness checking
```

### 2. Configure your profile

```bash
cp templates/profile.template.md config/profile.md
```

Fill in the YAML frontmatter of `config/profile.md` with your personal details: name, email, target roles, narrative, compensation, languages. The markdown body below the frontmatter is where your trajectory corpus lives — built up over time using `analyze-sources`. Alternatively, start a session and the agent will guide you through the full setup interactively.

### 3. Add your CV

Create `config/cv.md` with your full CV in markdown format. This is the source of truth for all evaluations and PDFs.

(Optional) Drop raw academic documents (project reports, internship descriptions) into `sources/` and run `analyze-sources` to build up the trajectory corpus in `config/profile.md`.

### 4. Configure portals

```bash
cp templates/portals.example.yml config/portals.yml
```

Edit `config/portals.yml`:
- Update `title_filter.positive` with keywords matching your target roles
- Add companies you want to track in `tracked_companies`
- Customize `search_queries` for your preferred job boards

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
