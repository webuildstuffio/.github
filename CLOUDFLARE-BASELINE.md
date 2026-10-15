# Cloudflare edge baseline — public sites

The standard Cloudflare configuration every public site in the
webuildstuffio fleet is held to. Established 2026-10-05/06 (traffic
floor), extended 2026-10-14 (DNSSEC + HSTS fleet-wide). Checked and
enforced by the `cloudflare-fleet-hardening` skill
(`cf_fleet_baseline.py audit|fix|verify`); this document is the
human-readable contract so the settings can also be applied by hand
when a new site launches.

## Settings

| Setting | Value | Why |
|---|---|---|
| TLS 1.3 | `zrt` (1.3 + 0-RTT) | fastest handshake, CWV/ranking signal |
| Early Hints | on** | 103 hints preload critical assets — on wherever the site ships `Link` preload/preconnect headers (fleet standard below); off only on `EARLY_HINTS_OFF` zones |
| Minimum TLS version | 1.2 | floor without cutting old crawlers/agents |
| Always Use HTTPS | on | no HTTP serving |
| Automatic HTTPS Rewrites | on | fixes mixed-content URLs at the edge |
| HTTP/3, Brotli, WebSockets | on | modern transport defaults |
| Always Online | off | never serve stale archive.org snapshots to crawlers |
| Development mode | off | cache bypass is for debugging only |
| Bot Fight Mode | on* | blocks junk automation; verified bots still pass |
| AI bot protections (`ai_bots_protection`, `ai_training`) | disabled | ALL crawlers allowed, incl. AI training — training inclusion feeds AI-answer citations (owner policy 2026-10-05) |
| DNSSEC | enabled | signed DNS answers. Cloudflare-Registrar domains complete automatically (Cloudflare publishes the DS; expect hours of `pending`). External registrars: paste the DS the zone provides |
| HSTS | on, max-age ≥ 31536000 (1y) | browsers refuse HTTP after first visit. `includeSubDomains` only after every web hostname in the zone verifies HTTPS-clean; otherwise apex-only. Preload-list submission is owner-only |
| URL normalization | scope `both`, type `cloudflare` | canonicalizes incoming + origin-bound URLs so rules/cache see one form; never redirects and does NOT touch trailing slashes (slash policy = redirect rules / origin / worker `html_handling`) |
| SSL mode | full / strict where the origin serves a valid cert | `flexible` is load-bearing on origins with no TLS (bankstatementsaver.com, statementsaver.com, comicgreeting.com, talkedit.ai, unlimited.land, findtracks.app) and normal on worker-served landings — never raise without verifying the origin cert first |

\* **Exception: `webuildstuff.dev` — Bot Fight Mode stays OFF.** It
serves `ingest.webuildstuff.dev`, the token-authed CI ingest API; BFM
challenges runner POSTs and silently breaks fleet ingest (2026-10-05
incident). All its other baseline settings apply normally.

\*\* **Fleet standard (2026-10-15): Early Hints ON ⟺ the site
ships `Link` preload/preconnect headers** — derived at build time
from the built HTML; ≤6 entries; raw `crossorigin` preserved;
CR/LF-fail-closed. All 16 canonical sites graduated 2026-10-15:
the three pilots (upgrade.tools PR #605, powerloss.tech PR #674,
webuildstuff.io PR #225) measured median LCP gains of −7.3% to
−21.8%, and the Phase 2 rollout shipped the same build-time
generator pattern to the remaining 13 sites. Zones whose sites
ship no `Link` headers belong in `EARLY_HINTS_OFF` in the
baseline script (**currently EMPTY** — all 16 canonical sites
graduated 2026-10-15); adding a zone to `EARLY_HINTS_OFF` is the
deliberate off-switch, and the monthly drift audit treats member
zones as compliant-off (the script's audit treats `off` as
compliant on member zones, and its `fix` mode sets `off`, never
`on`). Removing a zone from the set is the deliberate way to
re-enable hints once it ships Link headers.

History — why hints were off (2026-10-14): `termlimits.ca`,
`upgrade.tools`, `goldoutput.com`, `webuildstuff.io`, `dealt.win`
and `powerloss.tech` had Early Hints turned off in the fleet
speed-audit fix because none of them emitted `Link` headers with
`preload` or `preconnect` rel types (the two Cloudflare turns
into 103s), so the 103 machinery earned nothing — and
Cloudflare's Early Hints cache-probe subrequests
(`requestSource=earlyHintsCache`, `originResponseStatus` 0)
showed up in analytics as phantom 504s that polluted the speed
audit (full write-up: termlimits.ca repo,
`docs/early-hints-phantom-504s.md`). The `EARLY_HINTS_OFF`
off-switch was coded into the baseline script on 2026-10-15;
before it was coded, one `fix` run flipped `early_hints` back on
(upgrade.tools, 2026-10-14).

## New-site launch checklist

1. Zone on the Cloudflare account, nameservers live, apex + www
   proxied.
2. Confirm the site ships `Link` headers with `rel=preload` or
   `rel=preconnect` — the only relation types Cloudflare turns
   into 103s (a `Link:` header carrying other rel types, e.g.
   canonical, does not count). If it ships none, add the zone to
   `EARLY_HINTS_OFF` in the baseline script BEFORE applying the
   table — hints on a headerless site earn nothing and re-create
   the phantom-504 noise below.
3. Apply the table above (dashboard or the baseline script's `fix`
   mode).
4. Enable DNSSEC; if the registrar is external, paste the DS record
   the zone generates.
5. Verify: settings re-read at target values, apex serves the
   `Strict-Transport-Security` header, and the DS resolves at the
   parent (`dig DS <domain>`) once DNSSEC flips `active`.

## Canonical URL form (new sites)

- **House form: slashless.** New builds serve canonical URLs without
  a trailing slash (`/guide`, not `/guide/`). Frameworks that default
  to slashless (Next.js) need nothing in the app.
- A new slashless zone gets two edge redirect rules at launch (the
  baseline script's `new-site` mode creates them): www -> apex 301
  (path + query preserved), and a trailing-slash strip 301 for
  GET/HEAD requests.
- A zone's www -> apex redirect lives in exactly ONE place: the
  dynamic redirect rule. A legacy www Page Rule duplicating it is
  drift -- the baseline audit flags it and `fix` removes the Page
  Rule (first case: upgrade.tools, 2026-10-14).
- **Exception:** slash-serving builds -- e.g. Workers static assets
  with `html_handling: auto-trailing-slash` -- keep their slash form
  and get NO strip rule (it would loop against the asset handler).
- Otherwise, existing zones' redirect rules stay untouched by the
  baseline and the rest of this section governs new launches; the
  duplicate-www cleanup above is the one fleet-wide exception.

## Held / owner-only

- HSTS preload-list submission (browser-hardcoded, effectively
  irreversible).
- SSL raises without a verified origin cert.
- `security_level` changes (high on termlimits.ca + webuildstuff.dev
  is an owner hold).
- Redirect rules, DNS record changes, Access apps — out of scope for
  baseline work.
