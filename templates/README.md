# templates/

All base files for career-ops: copy-and-fill templates, canonical data schemas, and reference examples.

## Templates (copy to config/ before use)

| File | Purpose |
|------|---------|
| `profile.template.md` | Base for `config/profile.md` — YAML frontmatter + empty trajectory corpus sections. Copy here to start onboarding. |
| `portals.template.yml` | Base for `config/portals.yml` — 45+ pre-configured company and job board queries. Copy and customise for your target roles. |
| `strategy.template.md` | Reference template for strategy content (archetypes, adaptive framing, negotiation scripts). Content is now merged into `config/profile.md` under the `strategy:` key — use this template as a reference when editing that key. |

## Schemas (read-only, used by scripts)

| File | Purpose |
|------|---------|
| `states.yml` | Canonical application status values. Read by `verify-pipeline.mjs` and `normalize-statuses.mjs`. Do not edit. |

## Reference examples (read-only, not used at runtime)

| File | Demonstrates |
|------|-------------|
| `sample-report.md` | The A–F evaluation report format produced by the pipeline, with all six blocks (Role Summary through Interview Plan). |
