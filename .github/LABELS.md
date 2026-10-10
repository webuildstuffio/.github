# Fleet Issue Labels (v3) - simple standard for every repo

One system for the whole org. No scoring formulas, no numeric axes - the old
Formula 6 / emoji-priority systems are retired (too complicated). Canonical
source: `gh-actions-and-skills/.github/setup/master-labels.yaml` (this file's
copies in shell-config + both repo templates must stay identical). The
every-3-day labeling job creates these definitions in every non-archived
org repo - including brand-new repos - and applies them using repo tracker
tier/health plus issue content.

Max ~5 labels per issue: 1 priority + 1-3 type + at most 1 status + at most
2 themes (+ effort/workflow only when true).

## Priority - exactly one
`P0` critical/drop everything - `P1` this week - `P2` queued - `P3` low -
`P4` backlog, someday/maybe, no commitment.

## Type - at least one
`type:bug` `type:feature` `type:refactor` `type:security` `type:ci`
`type:test` `type:docs` `type:question` `type:duplicate`

## State - at most one (none = ready)
`status:paused` parked, no date - `status:delayed` slipped, still wanted -
`status:blocked` external blocker - `status:needs-info` waiting on owner.
Sweeps skip all status:* issues.

## Themes - the common lanes of work (at most two, only when clear)
`seo` `traffic` `analytics` `performance` `content` `revenue` `ux` `data`
`infra` `cost` `pwa` `mcp`

## Effort - optional, only when known
`effort:xs` <2h - `effort:s` ~half day - `effort:m` 1-2 days -
`effort:l` 3+ days - `effort:xl` epic, split it. Never auto-guessed.

## Workflow + agent
`epic`, claude-working / claude-complete / claude-failed, `dependencies`,
`github-actions`, `bun`, `monthly-update`, `good first issue`,
`help wanted`, `invalid`, `wontfix`, plus agent helpers: `needs-triage`,
`claude-ready` (clear <30-min slice for @claude batches), `human-only`
(owner judgment, never delegate), `regression`.

## Owner - blocked on the owner (the owner's personal queue)
`owner-decision` purple - a direction/judgment call only the owner can
make. `owner-mustdo` crimson - an action only the owner can perform
(account, console, dashboard, sign-in, API key, purchase). An issue gets
one of these only when it is truly blocked on the owner; agents surface
them, the owner clears them. Filter: `label:owner-decision` /
`label:owner-mustdo` org-wide.

## Repo specialties
Repos may add their own labels (`area:*`, `sig-*`, product/domain names) -
untouched by fleet sync. One rule: never reuse a generic name or meaning.

## New repos
Create the repo (from a template where possible), then either wait for the
next labeling-job run (auto-enrolled, definitions created) or run the fleet
sync in gh-actions-and-skills against the new repo. Do not hand-invent
priority words - P0-P4 only.
