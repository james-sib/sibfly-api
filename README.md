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
Self-registration, free coverage/cost preflight, idempotency keys, per-key spend caps, scoped browser-safe keys, batch, webhooks, x402 (USDC on Base), test-mode keys, and a hosted **MCP server** with 9 tools: `check_ground_motion`, `check_portfolio`, `get_coverage`, `get_motion_history`, `get_account`, `get_usage`, `ask_inspector`, `register_agent`, `buy_credits`. The full, authoritative contract lives at **https://sibfly.com/llms.txt** and **https://sibfly.com/openapi.json** — always current.

## Connect from an MCP client (Cline, Claude, Cursor, any Streamable-HTTP client)

SibFly is a **hosted (remote)** MCP server — nothing to install or run locally.

1. Get an API key (one call, free credits included, no captcha):
   ```bash
   curl -X POST https://sibfly.com/api/v1/autonomous/register \
     -H "Content-Type: application/json" -d '{"email":"you@example.com"}'
   ```
   Save the returned `api_key` (shown once). Or skip this step: connect without a key and call the `register_agent` tool from inside MCP.
2. Add the server to your MCP client config (Cline: MCP Servers → Remote Servers, or `cline_mcp_settings.json`):
   ```json
   {
     "mcpServers": {
       "sibfly": {
         "type": "streamableHttp",
         "url": "https://sibfly.com/mcp",
         "headers": { "Authorization": "Bearer sf_live_YOUR_KEY" }
       }
     }
   }
   ```
   OAuth 2.1 (Dynamic Client Registration + PKCE) is also supported for clients that prefer a "Connect" flow — discovery at `/.well-known/oauth-authorization-server`.
3. Try it: call `get_coverage` (free) on any US address, then `check_ground_motion` ($0.40, misses free). `dry_run: true` previews coverage, cost, and expected answer quality without billing. Out of credits? The `buy_credits` tool returns a payment link (card or crypto).

Sandbox: register with `{"mode":"test"}` for an `sf_test_` key — $100 fake credits, synthetic data, rehearse the whole flow for $0.

## Use cases
Real-estate due diligence · "is my house sinking?" for home buyers · insurance & mortgage underwriting screening · infrastructure and portfolio subsidence monitoring · geotechnical pre-screening.

---
_A product of SIB Scientific. Screening estimate from satellite radar (NASA OPERA / Sentinel-1 InSAR) — not an engineering, survey, or hazard determination._
