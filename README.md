# webuildstuffio org defaults

Default community-health + automation files for every repo in the org.

## Contents

- `CLOUDFLARE-BASELINE.md` — the fleet Cloudflare edge baseline (TLS, Bot Fight Mode gating, SSL strict, DNSSEC, HSTS) every public zone is held to.

- **Analytics standard** (repo-review#291): Web Analytics + Zaraz + GA4 setup for new sites lives in the repo templates — `docs/ANALYTICS.md` in [repo-template-nextjs](https://github.com/webuildstuffio/repo-template-nextjs/blob/main/docs/ANALYTICS.md) and [repo-template-vite](https://github.com/webuildstuffio/repo-template-vite/blob/main/docs/ANALYTICS.md). WA beacon via a deploy-time repo variable (never committed; auto-install does not inject on Worker-assets sites), the Zaraz GA4 tool + activation rule, and the `settings/zaraz` API routes. Follow the template matching the site's stack.

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
- `NOTIFICATIONS.md` — quiet-by-default policy: every repo is
  Ignored for the owner (no email or push notifications), and
  how that default is enforced for new repos.

New repos get the label definitions automatically: repos created
from repo-template-vite / repo-template-nextjs carry the files,
and the every-3-day fleet labeling job creates any missing
definitions in every non-archived repo. Repo-specific labels
(area:*, sig-*, product names) still live in each repo.
