# Beacon skill

The installable agent skill for **Beacon** — the neutral, verified-merchant
index AI shopping agents query to find merchants they can safely and legally
buy from, specializing in regulated/age-gated commerce (alcohol first).

```
npx skills add primoliquors/beacon-skill
```

Works across 70+ agents via the [skills CLI](https://github.com/vercel-labs/skills).

## What agents get

| Surface | Where |
|---|---|
| MCP (preferred) | `https://beacon-index.onrender.com/mcp` — registry name `io.github.primoliquors/beacon` |
| REST fallback | `https://beacon-index.onrender.com/agent/*` |
| Discovery manifest | `https://beacon-index.onrender.com/.well-known/beacon` (`trust_class=regulated`) |

## Why Beacon is different

- **Earned trust only.** Paid placement never moves rank — enforced in code and
  regression-tested, and every ranking response says so (`ranking_basis`).
- **Evidence on every fact.** Results carry provenance an agent can audit.
- **Real compliance, not self-declaration.** Merchant license attestations come
  from the State of Florida's daily public records (DBPR/AB&T); age-gate and
  jurisdiction rules fail safe (illegal ship-to returns zero, not a guess).
- **No shopper data.** Beacon stores no shopper-level identity, ever.

The skill definition lives in [`SKILL.md`](SKILL.md).
