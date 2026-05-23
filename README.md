# Solana Risk Intelligence Agent

> Community-grade on-chain risk scoring for the Solana ecosystem, delivered via Telegram.

---

## The Problem

Chainalysis and Elliptic cost $50k+/yr, lag weeks on new Solana programs, and are built for enterprise compliance teams — not traders, DeFi communities, or indie devs. Meanwhile, Solana moves at memecoin speed: new DEXes, new token launches, new scam vectors every day.

Existing Solana tools fill the monitoring gap (wallet trackers, price alerts) but none provide **risk intelligence**: *why* is this wallet suspicious, *what* on-chain patterns back it up, *how* does this token compare to known rug patterns?

No funded, winning, or production project in the Colosseum ecosystem has solved this at the community layer.

---

## The Idea

An AI agent — accessible via Telegram — that anyone can query for on-chain risk analysis on Solana. Drop a wallet address or token mint, get back a risk score with on-chain evidence, not a sanctions list lookup.

Solana-native: understands pump.fun graduation mechanics, Raydium CLMM LP patterns, Jito MEV, Meteora vault behavior, Jupiter routing risk. Not a generic chain analytics tool ported from EVM.

---

## Architecture Options & Trade-offs

### Option A: Pure Telegram Bot (Consumer)

```
User → Telegram Bot → Helius RPC / webhooks → Risk Scoring Engine → Response
```

**Pros:**
- Zero friction — already where Solana communities live
- Network effects: groups share intel, crowdsource flags
- Fast to ship an MVP
- No new wallet/UI to build

**Cons:**
- Telegram API limits (rate limits, no streaming)
- Hard to monetize — bots feel "free"
- No persistent state for complex queries
- Competitor bots (Concierge, Yokai, Runner) already occupy "tracking" mindset in users' heads

**Best for:** Retail traders, alpha groups, whale watchers

---

### Option B: Risk Oracle / API (B2B Infrastructure)

```
DeFi Protocol → Risk Oracle API → On-chain Data → Score + Attestation → Smart Contract Gate
```

Build the risk scoring primitive that other protocols integrate — similar to what Range Risk Oracle does but open, Solana-native, and community-maintained.

**Pros:**
- High willingness to pay (protocols pay for compliance primitives)
- B2B contracts → predictable revenue
- Can integrate with existing primitives (DFlow Proof, Solana Attestation Service)
- Defensible: data moat compounds over time

**Cons:**
- Long sales cycle
- Needs credibility / audits before protocols trust it
- Developer-facing — hard to build community around it
- Range Risk Oracle already exists (though niche and closed)

**Best for:** DeFi protocols needing compliance gates, institutional investors, dApp devs

---

### Option C: Telegram Bot → Oracle (Hybrid, Two-Stage)

```
Stage 1: Telegram bot (consumer) → build data + reputation
Stage 2: Expose risk scores as on-chain oracle → sell to protocols
```

Start consumer to build the data flywheel and brand, then productize the intelligence layer as B2B infrastructure.

**Pros:**
- Consumer traction validates the scoring model
- Community flags + on-chain data = better oracle than pure algorithmic
- Revenue path: free bot → paid API → on-chain oracle subscription
- Classic bottom-up GTM: win community trust, then sell to enterprises

**Cons:**
- Slower to revenue than pure B2B
- Two products to build and maintain
- Risk of getting stuck in "free Telegram bot" forever
- Requires disciplined sequencing

**Best for:** If you want to build a defensible long-term business, not just a tool

---

### Option D: Fund / Institutional Due Diligence Tool

```
VC / Family Office → Web app → On-chain investigation + AI narrative → Risk report
```

Target sophisticated buyers (VCs, family offices, funds) who need to evaluate Solana wallet counterparties, protocol teams, token treasuries before investing or partnering.

**Pros:**
- Highest willingness to pay ($500–$5k per report or SaaS)
- No competition at all in this segment on Solana
- Clear buyer persona, short sales cycle (direct outreach)
- Doesn't need real-time infra — batch reporting is fine

**Cons:**
- Small TAM (fewer buyers)
- Manual-heavy initially — hard to fully automate nuanced reports
- No community network effects
- Not Telegram-native

**Best for:** Fastest path to revenue with least infra

---

## Key Design Decisions

### 1. On-chain data backbone

| Option | Speed | Cost | Solana-native depth |
|---|---|---|---|
| **Helius** (webhooks + RPC) | Real-time | ~$50-500/mo | Excellent — parses all programs |
| **Bitquery** | Near real-time | Pay-per-query | Good but EVM-first |
| **Dune Analytics** | Batch / minutes | Free tier + credits | Limited Solana support |
| **Direct RPC** | Real-time | Node cost | Full control, high complexity |

**Recommendation:** Helius as primary — best Solana program parsing, webhook support for real-time alerts, reasonable cost at MVP scale.

---

### 2. Risk scoring model

| Approach | Accuracy | Speed | Explainability |
|---|---|---|---|
| **Rule-based heuristics** | Medium | Instant | High — easy to explain to users |
| **ML model (graph neural net)** | High | Slow to train | Low — black box |
| **LLM over on-chain data** | Medium-high | Fast at inference | High — generates natural language |
| **Hybrid: rules + LLM narrative** | High | Fast | High |

**Recommendation:** Rule-based scoring (deterministic, auditable) + LLM-generated explanation. Scores users trust; narratives users understand.

---

### 3. Data moat strategy

The scoring model is only as good as its training signal. Three possible moats:

- **Community flags** — Telegram users report scams → labeled dataset grows over time. Strong network effect but vulnerable to manipulation.
- **Historical pattern matching** — Index all known Solana rug pulls, scams, exploits → compare new wallets/tokens against fingerprints. Durable, hard to game.
- **On-chain graph analysis** — Wallet-to-wallet transaction graph → cluster detection, mixer hops, wash trading patterns. Most powerful but highest complexity.

---

### 4. Monetization

| Model | Fits which option | Risk |
|---|---|---|
| Freemium bot + paid API keys | A, C | "Why pay when basic is free?" |
| Per-query pricing | B, D | High friction for casual users |
| Protocol subscription (monthly) | B, C | Needs B2B sales motion |
| Report-as-a-service | D | Manual, hard to scale |
| Token-gated premium features | A, C | Adds crypto complexity, aligns incentives |

---

## Recommended Starting Point

**Option C, Stage 1: Telegram Bot MVP**

1. Build a Telegram bot that accepts wallet address or token mint
2. Pull on-chain data via Helius
3. Score against a set of rule-based heuristics (liquidity concentration, dev wallet behavior, holder distribution, mint authority status, transaction velocity)
4. Generate a natural language risk summary via LLM
5. Let groups add it — watch what queries come in
6. Iterate scoring model on real community usage

**Non-goals for MVP:**
- No on-chain oracle (Stage 2)
- No ZK attestations (CompliFi territory)
- No institutional reports (Option D, later)
- No token (yet)

---

## Prior Art Summary

| Project | Status | Gap |
|---|---|---|
| Chainalysis / Elliptic | Enterprise, expensive, slow on Solana | No community access, not Solana-native |
| Concierge Solana Bot | Radar 2024, no prize, monitoring only | Zero risk intelligence |
| CompliFi | Cypherpunk 2025, no prize, dev-facing | No consumer layer |
| Range Risk Oracle | Niche B2B primitive | Closed, no community |
| DFlow Proof | Embedded in DFlow stack | Not standalone, not consumer |
| SmartAI / Solscout | Hackathon attempts, no traction | Generic, no Telegram |

**Conclusion:** The community-grade, Telegram-native, Solana-ecosystem-aware risk intelligence layer does not exist. Build it.

---

## Research Files

- `solana_risk_research.md` — Full Colosseum Copilot research dump with all prior art
