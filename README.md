# renovate-config

The single Renovate policy shared by every project. **Change a rule here, once —
all projects follow it on their next Renovate run.**

Consumed by:

| project | owner |
|---|---|
| autamoshub | Autamos-LLC |
| bookingsystem | czisso |
| jkplumbing | czisso |
| brand-dashboard | HENSOLDT-Marcom |

Each of those repos has a three-line `renovate.json`:

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["local>czisso/renovate-config"]
}
```

## Why this repo exists

The policy used to live as four byte-identical copies, re-copied into each
project by `~/scripts/sync-app-config.sh`. Copies drift, and one already had:
`brand-dashboard` formats at a different Prettier width, so every sync wrote
`renovate.json` in a shape its own CI then rejected. Three of the four also sat
on a stale copy whose comments described the wrong kind of access token.

A shared preset removes the copies, so there is nothing left to drift. This is
Renovate's own documented approach — see
<https://docs.renovatebot.com/config-presets/>.

## Notes

- The file **must** be named `default.json` at the repo root. That is the name
  `local>czisso/renovate-config` resolves to; a differently-named file is only
  reachable as `local>czisso/renovate-config:name`.
- This repo **must stay public**. Renovate does not read presets with a
  cross-owner PAT: there is no `RENOVATE_TOKEN` on any of the four consumers,
  and none on this repo either. Renovate authenticates as a GitHub App whose
  installation token is scoped to the single repository it is running against,
  so it reaches this repo only as an anonymous reader. Public is what makes
  that read succeed. Making this repo private would break Renovate on
  `autamoshub` (Autamos-LLC) and `brand-dashboard` (HENSOLDT-Marcom).
- `local>` is preferred over `github>`: it resolves via the configured platform
  rather than hardcoding github.com.
- Validate changes before pushing:
  `npx --yes --package renovate@latest renovate-config-validator default.json`
  Pin `renovate@latest` — a cached older `npx renovate` falsely rejects current
  fields such as `managerFilePatterns`.
