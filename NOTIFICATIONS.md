# Notifications — quiet by default

Owner policy (set 2026-09-30, re-affirmed 2026-10-16): the owner
watches **nothing** on GitHub. Every repo in the `webuildstuffio`
org and the personal `nichm` account is set to **Ignored**, which
turns off email notifications and web/push notifications for that
repo — including @mentions and review requests. Reversible per
repo at https://github.com/watching.

## Why this is a sweep, not a template setting

Watch state is a per-user GitHub setting. No file in a repo —
and nothing in `repo-template-vite` / `repo-template-nextjs` —
can set it. New repos start with no explicit subscription, so
GitHub defaults (participating + mentions) can still notify until
the repo is explicitly Ignored.

## How the default is enforced

1. **Weekly sweep** (Merlin cron
   `github-notifications-reignore-weekly`): enumerates every org
   + personal repo and sets any non-Ignored subscription to
   Ignored. Silent when nothing changed.
2. **One-time owner setting** (owner-only, no API exists):
   https://github.com/settings/notifications → uncheck
   "Automatically watch repositories" so new repos are not
   auto-watched on creation.
3. **Manual run**, any time:

   ```bash
   for r in $(gh repo list webuildstuffio --limit 200 --json nameWithOwner -q '.[].nameWithOwner') \
            $(gh repo list nichm --limit 200 --json nameWithOwner -q '.[].nameWithOwner') \
            $(gh api --paginate 'user/subscriptions?per_page=100' --jq '.[].full_name'); do
     gh api -X PUT "repos/$r/subscription" -F ignored=true
   done
   ```

   The third source catches any watched repo owned by someone
   else — the two `gh repo list` calls only cover owned
   namespaces. (As of 2026-10-16 the watched list is empty, so
   there are currently no third-party repos to catch.)

## New-repo checklist

- Nothing to add to the repo itself.
- The weekly sweep picks the repo up within 7 days; run the
  manual loop above if it needs to be quiet immediately.
