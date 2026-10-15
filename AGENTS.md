# AGENTS.md — .github (org defaults)

Org-wide default files for every repo in the webuildstuffio
GitHub org: community-health files, issue/PR templates,
workflow templates, and the org profile. Changes here affect
many repos at once — treat edits as fleet-wide, not local.

## Contents

- `.github/master-labels.yaml` — Fleet Label Schema v4
  configuration (v4.2 at the time this file was written: 53
  generic labels covering P0–P4 priority, `type:*`, `status:*`,
  themes, `effort:*`, and agent-helper/owner labels). The human
  doc for the schema is `.github/LABELS.md`.
- `.github/LABELS.md` — the rules for using the schema: exactly
  one priority, at least one `type:*`, at most one `status:*`,
  at most two themes; repo-specific labels (`area:*`, `sig-*`,
  product names) live in each repo and never reuse a generic
  name.
- `workflow-templates/` — starter workflows a new repo picks
  from when adding Actions: `bun-ci.yml`, `claude.yml`,
  `deploy-cf.yml`, `qodo-review.yml`, `security-scan.yml`,
  each paired with a
  `.properties.json` describing it in the template picker.
- `.github/ISSUE_TEMPLATE/` — default issue forms:
  `bug_report.yml` (auto-labels `type:bug` + `needs-triage`)
  and `feature_request.yml` (`type:feature` + `needs-triage`);
  `config.yml` leaves blank issues enabled.
- `.github/pull_request_template.md` — the default PR template
  new repos inherit when they have none of their own.
- `profile/README.md` — the public org profile page. This is
  user-facing copy: edit it like marketing material, not docs.
- `README.md` — index of what lives here and how it propagates.

## How changes propagate

- Label definitions: repos created from the org repo templates
  carry the label files with them, and the every-3-day fleet
  labeling job creates any missing definitions in every
  non-archived org repo. The header of `master-labels.yaml`
  names the canonical source file to keep copies identical
  with; check that header before editing the schema, and bump
  the schema version in the same change when labels change.
- Workflow templates are copied, not inherited: editing a
  template here changes only future copies. Existing repos keep
  the version they already have until someone updates them.
- Issue/PR templates only apply to repos without their own;
  many repos override these, so a template edit is not a fleet
  behavior change.

## Working in this repo

- There is no build system, test suite, or package manifest
  here. Nothing runs from this repo; every file is consumed by
  GitHub or copied elsewhere.
- Validate by reading: YAML must parse, template placeholders
  such as `$default-branch` are GitHub template syntax and
  must stay literal, and every workflow template needs its
  matching `.properties.json` next to it.
- Keep the label count and schema version in
  `.github/master-labels.yaml` and `.github/LABELS.md` in sync
  with each other, and say in the commit message what changed
  (labels added/renamed/removed) — downstream repos sync from
  this file.

## Conventions

- Org: webuildstuffio — solo developer; boring, minimal, reversible changes beat clever ones.
- Package manager elsewhere in the org is bun (`bun install --frozen-lockfile`); the workflow templates encode that and pin runtimes exactly — do not float versions when updating them.
