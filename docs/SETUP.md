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

Start a session and the agent will guide you through the full setup interactively using the `onboard` mode — it collects your identity, target roles, narrative, compensation, and languages via a structured interview, then builds the trajectory corpus (education, experience, projects) either from documents in `sources/` or through conversation.

If you prefer to set it up manually:
```bash
cp templates/profile.template.md config/profile.md
```
Then fill in the YAML frontmatter and the markdown body sections.

### 3. Generate your CV

Once `config/profile.md` is complete, it IS your CV — there is no intermediate file. Run directly:

```
/career-ops pdf
```

`config/profile.md` is the single source of truth. The formatted CV sections in its body are read directly by `modes/latex.md` to produce the `.tex` output.

(Optional) Drop raw academic documents (internship reports, project briefs) into `sources/` at any time. Use `analyze-sources` to extract and add formatted CV entries directly to `config/profile.md`.

### 4. Configure portals

```bash
cp templates/portals.template.yml config/portals.yml
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
