# Ai Product Strategy

> The "AI product" is a gamified, multi-personality social chatbot called "Agente Mundial" (World Agent). It acts as a fun, dramatic, and highly contextual commentator for groups of friends competing in a 2026 World Cup Prediction Pool.

---

## Strategy at a Glance

| Component | Module | Status | Key Artifact |
|-----------|--------|--------|-------------|
| **The Bet** | M1 | [x] | `01-the-bet/` |
| **The Moat** | M2 | [x] | `02-the-moat/` |
| **The Margin** | M3 | [x] | `03-the-margin/` |
| **The Contract** | M4 | [x] | `04-the-contract/` |
| **The Guardrails** | M5 | [x] | `05-the-guardrails/` |
| **The Pitch** | M6 | [x] | `06-the-pitch/` |

---

## The Bet (M1)

**What we're building, for whom, why now.**

- **Product:**
- **AI Value Archetype:** Copilot
- **Vulnerability Scores:** _(add: Moat _/5 · Data _/5 · Platform _/5)_
- **Top Risk:** The app faces five distinct vulnerabilities, ordered by severity.
- **Confidence:** M
- **Prototype:** https://testflight.apple.com/join/dm43b6bp
- **Kill Criteria:** If the finantial loss exceeds 2000 euros and there are no evidences of growth revenue.

→ Details: [`01-the-bet/`](01-the-bet/)

---

## The Moat (M2)

**Why this won't get copied in 6 months.**

- **Data Flywheel Score:** 10/20
- **Weakest Loop:** Domain Context + Network (tied at 2/5)
- **Top Encroachment Threat:** Attacker: Meta (WhatsApp) / Apple (Apple Intelligence) Vector: Native, multimodal AI agents embedded directly into the OS or default group chats. Vertical Competitor
Attacker: Sleeper (or ESPN / Yahoo Fantasy) Vector: The incumbent in social fantasy sports. Adjacent Expansion
Attacker: Telegram (via Mini-App / Bot Developers) Vector: Frictionless distribution. An indie developer builds your product as a "Telegram Mini App."
- **Encroachment Defense:**
1.Platform Encroachment (Meta/Apple/Telegram): Dedicated UI + culturally unhinged personalities that brand-safe horizontal platforms literally cannot replicate due to RLHF constraints.
2. Vertical Competitor (Sleeper/ESPN/Yahoo): Your audience is Spanish-speaking football fans in WhatsApp groups, not the American multi-sport Sleeper user — and your personality anchors (Roncero, Pedrerol, Andrés Montes) are culturally specific to this audience in ways a general fantasy platform cannot replicate without building the same 7 personalities from scratch.
3. Adjacent Expansion (Telegram indie dev): The scoring engine (bracket resolution, tiebreakers, exact hit calculation) plus the transcreation pipeline for 14 languages plus the 7 personality anchors is a multi-month build even for a skilled indie dev — and by the time they ship it, your golden dataset has already compounded beyond what a fresh clone can match.
- **Vendor Portability:** 85% Ready

→ Details: [`02-the-moat/`](02-the-moat/)

---

## The Margin (M3)

**Will this make money or bleed it?**

- **Gross Margin (current):** 0% (free product, $0 revenue, $0 COGS on free tiers)
- **Gross Margin (AI-adjusted):** 90-94% on pass revenue; ~55-65% blended with ads at 10% conversion
- **Pricing Model:** Group-based (admin-sponsored $4.99 pass per group per tournament), though labeled seat-based in the doc
- **Pricing Today → Tomorrow:** Free → $4.99 group pass + $0.75 CPM banner ads
- **Total AI COGS / unit:** $0.30-0.50 per group per tournament ($0.03-0.05 per user)
- **Cascading Strategy:** None today (100% 70B on Groq free) → Self-hosted 8B GPU (96% traffic) + GPT-4o-mini (4% async summaries)
- **Net Margin Shift:** 0% (free un-monetizable utility) → 90-94% (premium entertainment with structural cost protection)
- **Break-even at:** ~5,400 paid groups ($27k CapEx ÷ $4.99 gross pass rev). At 10% conversion, ~54,000 total groups needed. But the self-hosted OpEx ($900-1,700/mo) is covered by ~180-340 paid groups per month on pass revenue alone. The first tournament (40 days) likely breaks even on OpEx but does not recover the full CapEx — that requires 2-3 tournaments or hitting 100k users.

→ Details: [`03-the-margin/`](03-the-margin/)

---

## The Contract (M4)

**Why users will trust a probabilistic system.**

- **Reliability Target:**
- **Golden Dataset:** 13 rows, 5 adversarial
- **Confidence UX:** **Current state: Not implemented.** No confidence indicator exists in the user-facing UI. The Judge scores are stored in AILog and visible in the dev dashboard, but never shown to users.
- **HITL Architecture:** **Current implementation (as coded):**
- **Failure Mode Coverage:** *What failure mode did your partner find that you missed?*

→ Details: [`04-the-contract/`](04-the-contract/)

---

## The Guardrails (M5)

**What breaks when this scales — and what compounds.**

- **Compounding System:** | Loop | Iteration 1 → Iteration n | Compounds? | Status | Correction vs Original | |------|--------------------------|-----------|--------|----------------------| | **Quality Gate** | Judge gives feedback → response cor…
- **Governance Posture:** AI features in Agente Mundial — personality-driven match commentary, web search (Tavily) for factual queries, transcreation to 8 non-Latin scripts, daily AI summaries, RSS news rebroadcasts.…
- **Autonomy Boundaries:** | OK Solo | Needs Human |
- **Escalation Triggers:**
- **Audit Cadence:** | Cadence | Activity | Owner | Status |
- **Shadow AI Audit (user-side):**
- **Agent Boundaries:**
- **Regulatory Exposure:** | Regime | Applies? | Risk Tier | Key Gaps |

→ Details: [`05-the-guardrails/`](05-the-guardrails/)

---

## The Pitch (M6)

**How you get this funded, shipped, and adopted.**

- **Horizon 1 (Now):** Per-personality eval thresholds (closes SILO #5) · Leaderboard history with position delta + sparkline · Add FR/PT/IT/DE as direct-generation languages · Match reminder notifications + ICS calendar export
- **Horizon 2 (Next):** "What-if" simulation + bracket playback · Prediction strategy personality mode · Fix RAG embeddings (use vector search in `retrieveContextForPlayer`) · Native "what-if" sim + shareable leaderboard cards
- **Horizon 3 (Bet):** Generic league support (La Liga, Premier League) · Custom personality creator / per-group anchor overrides · Cross-group leaderboard / global rankings
- **Board Narrative:** Personality-driven AI commentary turns a static World Cup prediction pool into a daily social ritual — and the 50-day tournament generates enough data to validate a year-round product before the next major event.
- **Ask:** $27k CapEx for monetization infrastructure + $5k/month OpEx for self-hosted GPU (3-month minimum = $15k). Total: **$42k**.
- **Key Strategic Change:** Dropped fine-tuning from all horizons. Reclassified Data Flywheel score from 5/5 to 2/5. Fine-tuning is Year 2 — it improves the next event, not this one.…

→ Details: [`06-the-pitch/`](06-the-pitch/)
