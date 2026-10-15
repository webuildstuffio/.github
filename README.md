# webuildstuffio org defaults

Default community-health + automation files for every repo in the org.

## Contents

- `.github/master-labels.yaml` — canonical Fleet Label Schema v4
  (49 generic labels: P0-P4 priority, type:*, status:*, themes,
  effort:*, agent helpers). Mirrored in gh-actions-and-skills
  (source of truth) and both repo templates.
- `.github/LABELS.md` — the simple human doc for the schema.
- `.github/workflow-templates/` — org-wide workflow templates
  (Bun CI, Claude Code, Qodo Review, Security Scan, Deploy to
  Cloudflare). New repos
  pick these when adding workflows; bun-only, ubicloud-standard-2.
- `.github/ISSUE_TEMPLATE/` — default issue forms (auto-label
  type:* + needs-triage).
- `.github/pull_request_template.md` — default PR template.

New repos get the label definitions automatically: repos created
from repo-template-vite / repo-template-nextjs carry the files,
and the every-3-day fleet labeling job creates any missing
definitions in every non-archived repo. Repo-specific labels
(area:*, sig-*, product names) still live in each repo.
