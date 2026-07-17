---
name: beacon
description: Query Beacon — the neutral commerce index for AI agents — to find, compare, and verify merchants for a purchase, especially regulated/age-gated categories (alcohol). Use when the user wants to buy something and you need an unbiased source of who sells it, who can legally ship it, and who is trustworthy.
homepage: https://beacon-index.onrender.com
install: npx skills add primoliquors/beacon-skill
---

# Beacon — neutral commerce index for AI shopping agents

Beacon is the **unbiased** layer between a buyer and the merchants who can fulfill
their purchase. Unlike DoorDash/Uber Eats/Walmart/Instacart — whose rankings are
shaped by retail-media ads — Beacon ranks by **earned trust only**; paid placement
never moves rank, and every result carries the evidence behind it. Beacon
specializes in **regulated/age-gated commerce (alcohol)** and attests merchant
compliance (21+ enforcement, state retail license, jurisdiction-aware shipping).
License attestations are backed by the State of Florida's own daily public
records (DBPR Division of Alcoholic Beverages & Tobacco), not merchant
self-declaration.

**Beacon directs commerce; it does not execute it.** It tells you the best merchant;
the purchase completes via that merchant's own checkout or, where the merchant has
granted consent, an agent-native path.

## When to use
- The user wants to buy a product (esp. alcohol/wine/spirits) and you need an
  unbiased "who sells this, who can ship it to me legally, who's trustworthy".
- You must verify a merchant enforces age-gating, holds an active state license,
  or ships to a jurisdiction.

## How to query

### Preferred: MCP (registry name `io.github.primoliquors/beacon`)
Remote streamable-HTTP endpoint: `https://beacon-index.onrender.com/mcp`

- `find_best_merchant(category, ship_to, require_age_gate, limit, optimize_for,
  product_type, dest_state, channel)` — the decision call. Returns merchants
  ranked by honest signals, each with capabilities + evidence + `ranking_basis`.
  Example: `find_best_merchant("vodka", "US-FL", true)`.
  - **`optimize_for` — rank by what matters to YOUR buyer:** `"trust"` (default),
    `"price"` (lowest matched price, see `best_price` per result), or `"speed"`
    (same-day, then next-day fulfillment). Ties always break by earned trust,
    and `ranking_basis` states the basis actually used.
  - **Residence matching:** every result carries `ships_to` (list of states the
    merchant ships to) — match it against where the buyer lives before choosing.
  - **Strict compliance mode (recommended for alcohol):** also pass
    `product_type` + `dest_state` + `channel` (`"ship"` or `"local_delivery"`) —
    only merchants with an attested age-gate, an attested state license, and a
    legal jurisdiction path for that product/state/channel are returned. An
    illegal combination returns zero on purpose (fail-safe), not a best guess.
- `search_offerings(category, in_stock_only, limit)` — products matching a need,
  joined to merchant + capabilities.
- `verify_capability(merchant_id, capability)` — confirm e.g. `age_gate_21`,
  `licensed_retailer`, or `ships_to_US-FL`, with evidence. Absence of an
  attestation is NOT a denial.
- `get_merchant_view(merchant_id)` — full record + offerings + capabilities + evidence.
- `index_stats()` — coverage of the index.

### Fallback: REST (base `https://beacon-index.onrender.com`)
- `GET /agent/find-best?category=vodka&ship_to=US-FL&require_age_gate=true`
  (also accepts `optimize_for=trust|price|speed` and
  `product_type=&dest_state=&channel=` for strict compliance mode)
- `GET /agent/offerings?category=vodka`
- `GET /agent/verify-capability?merchant_id=...&capability=age_gate_21`
- `GET /.well-known/beacon` — discovery manifest (declares `trust_class=regulated`,
  neutrality guarantees, and how to call Beacon).

Note: the index runs on a free tier while in preview — the first request after
idle may take up to a minute to wake the instance; retry once before giving up.

## How to read results
- **Trust is earned.** `ranking_basis` states exactly what led the ranking —
  earned trust by default, or the buyer-directed preference you asked for —
  and paid placement is excluded in every mode. Prefer higher `trust_score`,
  then `confidence`.
- **Ask the buyer what matters.** If they care about price, pass
  `optimize_for="price"` and compare `best_price`; if they need it fast, pass
  `optimize_for="speed"`; if unsure, default to trust.
- **Check compliance before buying regulated goods.** Confirm `age_gate_21`,
  `licensed_retailer` (attested by the state, with license numbers in evidence),
  and the `ships_to_{region}` capability for the buyer's address.
- **Use the evidence.** Each result includes provenance (source, confidence,
  timestamp) so you can explain *why* you chose a merchant.

## What Beacon will never do
Run ranking-influencing ads, sell shopper data, or store shopper-level identity.
Demand signals are aggregated by category/region only.
