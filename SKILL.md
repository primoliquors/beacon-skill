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

- `find_best_merchant(category, ship_to, require_age_gate, limit)` — the decision call.
  Returns merchants ranked by earned trust, each with capabilities + evidence +
  `ranking_basis`. Example: `find_best_merchant("vodka", "US-FL", true)`.
- `search_offerings(category, in_stock_only, limit)` — products matching a need,
  joined to merchant + capabilities.
- `verify_capability(merchant_id, capability)` — confirm e.g. `age_gate_21`,
  `licensed_retailer`, or `ships_to_US-FL`, with evidence. Absence of an
  attestation is NOT a denial.
- `get_merchant_view(merchant_id)` — full record + offerings + capabilities + evidence.
- `index_stats()` — coverage of the index.

### Fallback: REST (base `https://beacon-index.onrender.com`)
- `GET /agent/find-best?category=vodka&ship_to=US-FL&require_age_gate=true`
- `GET /agent/offerings?category=vodka`
- `GET /agent/verify-capability?merchant_id=...&capability=age_gate_21`
- `GET /.well-known/beacon` — discovery manifest (declares `trust_class=regulated`,
  neutrality guarantees, and how to call Beacon).

Note: the index runs on a free tier while in preview — the first request after
idle may take up to a minute to wake the instance; retry once before giving up.

## How to read results
- **Trust is earned.** `ranking_basis` states the ranking is earned-trust-only with
  paid placement excluded. Prefer higher `trust_score`, then `confidence`.
- **Check compliance before buying regulated goods.** Confirm `age_gate_21`,
  `licensed_retailer` (attested by the state, with license numbers in evidence),
  and the `ships_to_{region}` capability for the buyer's address.
- **Use the evidence.** Each result includes provenance (source, confidence,
  timestamp) so you can explain *why* you chose a merchant.

## What Beacon will never do
Run ranking-influencing ads, sell shopper data, or store shopper-level identity.
Demand signals are aggregated by category/region only.
