# SibFly — measured ground subsidence API (mm/year) for any US address

**Is the ground under an address sinking?** SibFly returns **measured** vertical ground motion — subsidence and uplift, in **millimeters per year and inches per year** — for any US address, from **NASA OPERA Sentinel-1 InSAR** satellite radar. Measured, not modeled.

- **Live API:** https://sibfly.com
- **Docs (human + agent):** https://sibfly.com/docs
- **Agent docs:** https://sibfly.com/llms.txt · **OpenAPI:** https://sibfly.com/openapi.json · **Machine schema:** https://sibfly.com/api/v1/schema
- **MCP server:** `https://sibfly.com/mcp` (Streamable HTTP)

## Quickstart
```bash
# 1) an AI agent can register itself — no human, no captcha
curl -X POST https://sibfly.com/api/v1/autonomous/register \
  -H "Content-Type: application/json" -d '{"email":"YOUR_EMAIL"}'

# 2) measure the ground under an address
curl "https://sibfly.com/api/v1/motion?address=1100+Congress+Ave+Austin+TX" \
  -H "Authorization: Bearer sf_live_..."
```
Returns `velocity_vertical_mm_yr` / `_in_yr`, an uncertainty range, `assessment_code`, confidence, provenance, and cost. **Flat $0.40 per covered report; misses are free; free coverage/cost preflight via `?dry_run=1`.**

## What it is
- Data: **NASA OPERA DISP-S1** (Copernicus Sentinel-1 C-band InSAR), 30 m grid, 2016–present.
- Vertical **mm/year of ground movement** (not elevation), measured, per exact 30 m cell, with uncertainty.
- Coverage: US (expanding). Not real-time (source refreshes ~every 12 days); a screening estimate, not survey-grade.

## For AI agents
Self-registration, free coverage/cost preflight, idempotency keys, per-key spend caps, scoped browser-safe keys, batch, and a hosted **MCP server** (tools: `check_ground_motion`, `check_portfolio`, `get_coverage`, `get_motion_history`, `get_account`). The full, authoritative contract lives at **https://sibfly.com/llms.txt** and **https://sibfly.com/openapi.json** — always current.

## Use cases
Real-estate due diligence · "is my house sinking?" for home buyers · insurance & mortgage underwriting screening · infrastructure and portfolio subsidence monitoring · geotechnical pre-screening.

---
_A product of SIB Scientific. Screening estimate from satellite radar (NASA OPERA / Sentinel-1 InSAR) — not an engineering, survey, or hazard determination._
