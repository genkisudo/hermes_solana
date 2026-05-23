# Personal Job Search Agent — Telegram Bot
*Research & Brainstorm — May 23, 2026*
*Runtime: Hermes + Anthropic Claude Sonnet (Pro)*

---

## The Idea

A personal Telegram bot that acts as your AI job search agent. It knows your resume, skills, and socials. It communicates with other bots (recruiter bots, company bots, other candidate bots) to surface short-term contractor matches autonomously — without you having to manually apply anywhere.

Not a marketplace. Not a platform. A personal agent that works for you.

---

## What Makes This Different from Prior Art

Every prior attempt at this problem (Chainhire, GigHubLabs, Web3Inn, OnChainCV, VeriPort, deopp — all Colosseum hackathon projects, **none won prizes**) built platforms: centralized matching services with dashboards, fees, and user accounts.

This is the inverse: **a bot that represents you**, negotiates on your behalf, and connects peer-to-peer with other bots. The platform is Telegram. The runtime is already built (Hermes).

---

## Prior Art (Colosseum Research)

| Project | Hackathon | Winner | Approach | Likely Gap (inferred) |
|---|---|---|---|---|
| Chainhire | Radar 2024 | No | Decentralized hiring platform, on-chain credentials | Platform model needs two-sided liquidity |
| GigHubLabs | Renaissance | No | Web3 marketplace for freelancers | Generic, no differentiation from Web2 |
| Web3Inn | Renaissance | No | Job matching + guaranteed payments | Platform cold-start problem |
| OnChainCV | Breakout 2025 | No | On-chain verified portfolio/resume | Tool without a distribution channel |
| VeriPort | Cypherpunk 2025 | No | On-chain career wallet, verifiable credentials | Dev-facing, no consumer UX |
| deopp | Renaissance | No | Skill-salary matching with on-chain proof | Niche scope, no clear GTM |
| OddJobs | Breakout 2025 | No | Local gigs marketplace, crypto payments | Hyper-local, limited scale |

**Gap:** Nobody built a personal agent. Everyone built platforms. Platforms need two-sided liquidity to work — they all died on that cold start problem.

### Agent-to-Agent Protocols (Emerging, Relevant)

| Project | Hackathon | What it does |
|---|---|---|
| AI Economy Protocol (AEP) | Cypherpunk 2025 | Autonomous AI agent marketplace: service discovery, negotiation, automated payments on Solana |
| XAAM | Breakout 2025 | Decentralized marketplace for AI agents to share capabilities |
| MCPay | Cypherpunk 2025 | Monetize MCP tools + AI agent capabilities via x402 on Solana |
| Model Context Swap | Cypherpunk 2025 | AI-native DEX for autonomous agents using MCP |

These are infrastructure plays on Solana — none targeted job search specifically. They validate that agent-to-agent service discovery and autonomous payments are active research areas. MCP as a protocol layer for agents is gaining traction across the ecosystem.

### Archive Signals

- **a16z Crypto** — "Tourists in the bazaar": agents will need B2B payments; stablecoins get there first. Describes agents visiting vendors autonomously — directly maps to bot-to-bot job matching.
- **Galaxy Research** — "Agentic Payments and Crypto's Emerging Role": MCP + A2A (Agent-to-Agent) Protocol named as foundational primitives. Agents paying each other for services is real now.
- **Superteam Blog** — Superteam Earn: on-chain job listings (IPFS), two-way reputation, talent matching. Closest working precedent for on-chain gig work on Solana.
- **Superteam Blog** — Streamflow: streaming payments on Solana for ongoing work. Relevant for contractor pay-as-you-work model.

---

## Architecture Options & Trade-offs

### Option A: Single Personal Bot, Pull-Based

```
You → Telegram → Your Hermes Bot
                     ↓ (on schedule or on demand)
              Scrapes job boards / Telegram groups / LinkedIn
                     ↓
              Filters against your resume context
                     ↓
              Sends you matched opportunities
```

**Pros:**
- Simplest to build — no bot-to-bot protocol needed
- Hermes already does this (cron jobs, web search, Telegram delivery)
- Works today, no ecosystem dependency
- Full privacy — your data never leaves your bot

**Cons:**
- One-directional — you still apply manually
- No negotiation, no autonomous outreach
- Relies on scraping (fragile, may violate ToS)
- No network effects — doesn't get smarter over time

**Complexity:** Low. Could ship in a day with Hermes cron + web search skill.

---

### Option B: Bot-to-Bot Peer Discovery (Push-Based)

```
Your Bot ←→ Recruiter Bot    (direct Telegram bot API messaging)
Your Bot ←→ Company Bot
Your Bot ←→ Other Candidate Bots
```

Each bot exposes a simple "match" interface. Your bot broadcasts a capability card (hashed/privacy-filtered resume summary), other bots respond with opportunities. Match → your bot surfaces it to you.

**Pros:**
- Truly autonomous — bot handles outreach and pre-filtering
- No platform dependency — P2P
- Novel: nobody has done this at the Telegram layer
- Hermes is perfect runtime for this (already Telegram-native, MCP-aware)

**Cons:**
- Requires a bot discovery protocol — how do bots find each other?
- Hard Telegram constraint: bots cannot initiate DMs to users or other bots unless the target started a conversation first. Outreach must go through groups or webhooks.
- Trust problem: how do you know a recruiter bot is legit?
- Spam risk: mass group posting gets accounts banned
- Other bots need to exist and support the protocol (cold start)

**Complexity:** High. Requires protocol design + ecosystem bootstrapping.

---

### Option C: Hybrid — Personal Bot + Curated Group Network

```
Your Bot → joins curated Telegram groups (job boards, DAOs, communities)
         → monitors for relevant posts (NLP matching)
         → auto-drafts reply / intro on your behalf
         → you approve before sending
```

Groups become the discovery layer. Your bot is the filter + drafter. No new protocol needed — leverages existing Telegram infrastructure.

**Pros:**
- Works with existing ecosystem (Superteam Earn, Web3 job groups, etc.)
- Hermes can already monitor groups and draft replies
- Human-in-the-loop approval avoids spam/trust issues
- Immediate utility — no cold start

**Cons:**
- Still semi-manual (you approve each)
- Group-dependent — quality varies wildly
- Not truly autonomous
- Doesn't scale beyond what you can manually review

**Complexity:** Medium. Hermes group monitoring + LLM drafting + approval flow.

---

### Option D: Federated Bot Network with Shared Protocol (Long-Term Vision)

```
Protocol layer: bots advertise capability cards to a shared registry
                (Telegram group, on-chain account, or simple API)

Your Bot → reads registry → finds relevant recruiter bots
         → initiates handshake → shares filtered profile
         → negotiates terms (rate, duration, timeline)
         → you confirm → payment via Solana stablecoin stream
```

Think: LinkedIn + Upwork replaced by a bot network where each participant controls their own agent.

**Pros:**
- Genuinely new category — agent-native labor market
- Aligns with A2A protocol trends (Galaxy Research, a16z)
- Solana payments fit naturally (Streamflow for streaming, SPL stablecoins for settlement)
- Each bot is sovereign — no platform extracting fees
- Could become infrastructure (other people run their own bots)

**Cons:**
- Requires ecosystem buy-in — chicken-and-egg
- Protocol design is hard to get right
- Trust, identity, and anti-spam are unsolved at this layer
- Way out of scope for a personal tool

**Complexity:** Very high. Multi-quarter effort minimum.

---

## Recommended Build Path

**Start with Option C, design for Option B.**

### Phase 1 — Personal Utility (Ship in days)
Build it selfish first. Make it useful for you specifically:
- Feed Hermes your resume (markdown), LinkedIn URL, GitHub, Twitter/X
- Set up a cron job: monitor Superteam Earn, relevant Telegram groups, LinkedIn (via scrape), Twitter job posts
- On match: Hermes drafts a tailored intro message using your context
- You approve → it sends (or you copy-paste)
- Log all opportunities to a simple Notion/Obsidian note

**Stack:** Hermes + Claude Sonnet + existing skills (google-workspace, xurl, obsidian, telegram)

### Phase 2 — Bot-to-Bot Handshake (weeks)
Define a minimal capability card format:
```json
{
  "type": "candidate",
  "skills": ["solana", "rust", "python", "ai-agents"],
  "availability": "contract, immediate",
  "rate": "negotiable",
  "timezone": "JST",
  "contact": "@yourtelegrambot"
}
```
Any bot that wants to participate publishes the equivalent recruiter card. Discovery via a shared Telegram group (pinned messages or a bot registry bot). Your bot polls, filters, initiates DM.

**Stack:** Hermes + simple JSON protocol + Telegram Bot API

### Phase 3 — Payments & Trust (months)
Once matches happen, add:
- On-chain payment via Streamflow (streaming SOL/USDC for ongoing work)
- Reputation via Solana Attestation Service (past contracts, verified completions)
- Optional: on-chain capability card as SBT (references VeriPort / OnChainCV approach but lighter)

---

## Key Design Decisions

### 1. How much context does your bot share?

| Approach | Privacy | Matching quality |
|---|---|---|
| Full resume shared with all bots | Low | High |
| Hashed/summarised capability card | High | Medium |
| Share only on explicit match request | High | High (but slower) |
| ZK-proved skills (no raw data) | Very high | Medium |

**Recommendation:** Capability card first (skills + availability + rate range + contact). Share full resume only after human approval.

### 2. Bot discovery protocol

| Option | Complexity | Ecosystem dependency |
|---|---|---|
| Shared Telegram group as registry | Low | Low — anyone can join |
| On-chain registry (Solana account) | Medium | Requires Solana wallet per bot |
| Centralised API registry | Low | High — someone runs it |
| MCP server as matchmaker | Medium | Hermes-native — good fit |

**Recommendation:** Telegram group registry to start (zero infra), MCP server as it scales.

### 3. Trust & anti-spam

Cold problem. Mitigations:
- Human-in-the-loop approval for all outbound contact (Phase 1-2)
- Rate-limit outreach to N bots/day
- Require reciprocal capability card before engaging
- Build reputation score from completed contracts over time

### 4. Hermes-specific advantages

- Already Telegram-native — no new bot framework needed
- Claude Sonnet handles resume parsing, job description matching, intro drafting natively
- Cron jobs handle scheduled monitoring without infra
- MCP-aware — can call external tools (Superteam Earn API, LinkedIn scraper, etc.)
- Skills system means reusable workflows across multiple projects
- Existing skills cover most of Phase 1: xurl (Twitter), google-workspace (Gmail/Docs), obsidian (notes), web search

---

## Honest Risk Assessment

| Risk | Severity | Mitigation |
|---|---|---|
| Bot-to-bot protocol has no adoption | High | Start with Option C (no protocol needed), build protocol only if Option C validates demand |
| Telegram bans automated outreach bots | Medium | Keep volume low, human-in-the-loop, avoid mass DM patterns |
| Resume context leaks via bot | Medium | Capability card abstraction, explicit approval before sharing full profile |
| LLM intro messages feel generic | Low | Claude Sonnet with rich context produces strong personalised drafts |
| Opportunity quality in Telegram groups is low | Medium | Curate groups carefully; add LinkedIn/Superteam Earn as primary sources |

---

## What Does Not Exist (Confirmed Gap)

- A personal AI job agent that lives in your Telegram and works for you autonomously
- Bot-to-bot job matching protocol at the Telegram layer
- Hermes-powered personal career agent (nobody has built a skill for this)

All prior art built platforms. Nobody built the sovereign personal agent. That is the gap.

---

## Sources

- Colosseum Copilot API research (projects: Chainhire, GigHubLabs, VeriPort, OnChainCV, deopp, AI Economy Protocol, XAAM, MCPay, Model Context Swap)
- Archives: a16z_crypto ("Tourists in the bazaar", "AI needs crypto"), Galaxy Research ("Agentic Payments"), Superteam Blog (Earn, Streamflow)
