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
| Early Hints | on | 103 hints preload critical assets |
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

## New-site launch checklist

1. Zone on the Cloudflare account, nameservers live, apex + www
   proxied.
2. Apply the table above (dashboard or the baseline script's `fix`
   mode).
3. Enable DNSSEC; if the registrar is external, paste the DS record
   the zone generates.
4. Verify: settings re-read at target values, apex serves the
   `Strict-Transport-Security` header, and the DS resolves at the
   parent (`dig DS <domain>`) once DNSSEC flips `active`.

## Held / owner-only

- HSTS preload-list submission (browser-hardcoded, effectively
  irreversible).
- SSL raises without a verified origin cert.
- `security_level` changes (high on termlimits.ca + webuildstuff.dev
  is an owner hold).
- Redirect rules, DNS record changes, Access apps — out of scope for
  baseline work.
