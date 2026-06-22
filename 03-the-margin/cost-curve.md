# Cost Curve & Pricing Strategy

## Cost Model



### MVP (10 users/month)

| Cost Category | Per-User/Month | Notes |
|---|---|---|
| Inference (primary model) | **$0** | **Primary: `llama-3.3-70b-versatile`**. Free on Groq tier |
| Inference (judge/quality gate) | **$0** | `llama-3.1-8b-instant` — free on Groq. **2 calls per response** (primary + judge), up to 3 retries |
| Inference (daily summaries) | **$0** | Separate 70B call per group at 08:45 daily |
| Infrastructure | **$0** | Render Free + Vercel Hobby |
| Data/storage | **$0** | MongoDB Atlas M0 free tier |
| Web Search (Tavily) | **$0** | 1,000 free searches/mo — sufficient at 10 users |
| Email (Brevo) | **$0** | 300 free emails/day — sufficient at 10 users |
| Push Notifications | **$0** | Expo Push — free tier |
| Human-in-the-loop | **$0** | "Done by Developer" but still present in code (`HumanReview` model, 20 reviews/day cap). Not billed |
| **Total AI COGS** | **$0** |  All services fit within free tiers |

---

### Real MVP (1,000 users/month)

| Cost Category | Per-User/Month | Notes |
|---|---|---|
| Inference (primary 70B) | **$25-35** | Groq free tier has ~30 req/min, 500K tokens/day — **will throttle at 1,000 users**. Partial move to paid Together.ai (Llama-3.3-70B: $0.59/M input, $0.79/M output). Not "$15 for 8B". |
| Inference (judge 8B) | **$5-10** | Judge runs on every response (not sampling). 2x the call volume of primary. Still on Groq free can help, but rate limits bite here too. |
| Inference (daily summaries) | **$5** | 1 summary/group/day × 70B model. Assuming ~50-100 groups at 1k users. |
| Transcreation (8 distant languages) | **$0-5** | Only if non-Latin users are present. Extra 70B call per response in ko/th/ar/ja/ru/zh/vi/hi |
| Web Search (Tavily) | **$7** | 1,000 free searches/mo exceeded. Paid starter: ~$7/1K searches |
| Email (Brevo) | **$0-10** | 300 free/day = 9,000/mo — may suffice if only password resets. Brevo Starter: $25/mo |
| Infrastructure | **$7-14** | Render Standard ($14/mo) — correct. Free tier sleeps; WebSockets + cron jobs require paid plan. |
| Data/storage | **$9** | MongoDB Atlas M2 ($9/mo, 2GB) — reasonable for 1k users. Add **~$3/mo for data transfer egress** |
| Human-in-the-loop | **$0** | Auto-queued enforcement. Adds operational overhead (reviewing 20/day) but no direct API cost |
| **Total AI COGS** | **$55-95/mo ($0.055-0.095/user)** | 70B model pricing is the dominant factor. |

---

### Enterprise (100,000 users/month)

| Cost Category | Per-User/Month | Notes |
|---|---|---|
| Inference (primary 70B) | **$3,000-4,500** | **~3x your estimate.** Llama-3.3-70B at $0.59/M in + $0.79/M out. Linear scaling from 1k users at 70B pricing, not 8B. Negotiated throughput helps but doesn't 10x the price. |
| Inference (judge 8B) | **$500-800** | Judge 8B call per response + retries. Bulk pricing could reduce this, but still significant. |
| Inference (daily summaries) | **$200-400** | ~500-1,000 groups × daily 70B calls. Could batch or use smaller model. |
| Transcreation | **$200-500** | If serving global markets. Each non-Latin response requires an extra 70B call. |
| Web Search (Tavily) | **$150-300** | Scale plan for 100k users querying football news. |
| Email (Brevo) | **$40-100** | Brevo Enterprise or similar. Password resets, notifications, marketing. |
| Infrastructure | **$300-500** | 4-6 Render Pro instances + load balancer + Redis for Socket.IO scaling. Add **~$200-400 for bandwidth egress** (WebSocket messages, file uploads). |
| Data/storage | **$600-900** | MongoDB Atlas M30/M40 is ~$350-700/mo + **$150-250 for data transfer + backups**. Your range is slightly low. |
| RAG / Embeddings | **$200-500** | Vector embeddings at scale require dedicated endpoints (not free HF). Atlas Search add-on costs apply. |
| Human-in-the-loop | **$5,000-15,000** | At 100k users, auto-reviewing 20/day is useless. You need a real HITL team to review bot responses, feedback, and edge cases. Even a single part-time moderator is $500-1,000/mo. A small team of 3-5 in a low-cost market: $5,000-15,000/mo. |
| Monitoring & Observability | **$300-800** | Datadog/Grafana, Sentry, uptime monitoring, alerting at 100k users is mandatory. |
| **Total AI COGS** | **$10,500-24,300/mo ($0.105-0.243/user)** |  HITL alone dominates at true enterprise scale. |

---

**Root causes of cost scale:**
1. **70B ≠ 8B** — primary model is `llama-3.3-70b-versatile`, not the 8B version. ~12x cost per token on paid tiers.
2. **Double inference** — every response has 2 LLM calls (primary + judge), plus retries up to 3x.
4. **Missing HITL at scale** — Thumbs up/down replaces *technical* infrastructure but not the *human* cost of moderation at 100k users.
5. **Transcreation** — 8 non-Latin languages require extra 70B calls per response.



## Cascading Strategy
<!-- Cheap model → frontier model routing logic -->

## Cascading Strategy — Before vs After

### Current (10 users/month)

At this scale, everything fits on Groq free tier. The cascade is about **latency and architecture**, not cost.

| Field | Value |
|---|---|
| **Triage model** | `llama-3.1-8b-instant` via Groq — faster throughput, higher free-tier rate limits, sufficient for 3-sentence jokes with player stats |
| **Frontier model** | `llama-3.3-70b-versatile` via Groq — only for async summaries where number accuracy + personality depth matter |
| **Routing rule** | Feature-based: `generateResponse` (chat, sync) → **8B triage**; `generateDailySummary` / `generatePersonalitySummary` (cron, async) → **70B frontier** |
| **Expected cascade ratio** | ~95% triage / ~5% frontier (20-30 chat calls per group per match day vs 1 summary) |
| **Transcreation** | Not needed at 10 users (unlikely to have non-Latin users in the initial group), but architecture should point to triage model when it is |

**Cost consequence:** All $0. Same as today, but you get faster chat responses (8B is ~800 tokens/s vs 70B's ~500 tokens/s) and preserve 70B rate limits for when they matter.

### Future (100k users/month)

At this scale, Groq free tier is unusable. The cascade becomes a **pricing architecture**.

| Field | Value |
|---|---|
| **Triage model** | **Self-hosted fine-tuned 8B** (recommend Qwen2.5-7B — already the HF fallback, best multilingual support, LoRA fine-tunable on one GPU). Handles chat + transcreation + Judge on a single GPU instance. |
| **Frontier model** | `GPT-4o-mini` via API — async summaries where number hallucination must be avoided and latency doesn't matter. Only ~4% of traffic. |
| **Routing rule** | Feature-based: <br>• `generateResponse` (chat, sync) → **self-hosted 8B**<br>• transcreation pipeline (sync, triggered per-response for non-Latin languages) → **same self-hosted 8B** (fine-tuned)<br>• `generateDailySummary` / `generatePersonalitySummary` (cron, async) → **GPT-4o-mini** |
| **Expected cascade ratio** | ~96% self-hosted 8B / ~4% GPT-4o-mini <br>(150k chat calls + 30k transcreation calls vs 10k summaries + 5k personality summaries per day) |
| **Cost consequence** | Self-hosted 8B: ~**$500-1,000/mo** (single GPU). GPT-4o-mini: ~**$400-700/mo** (4% of 200k daily calls). No per-token pricing for the bulk of traffic. |

---

### Summary table

| | 10 users/mo | 100k users/mo |
|---|---|---|
| **Triage** | Llama 3.1 8B (Groq free) | Self-hosted fine-tuned 8B (Qwen2.5-7B) |
| **Frontier** | Llama 3.3 70B (Groq free) | GPT-4o-mini (API) |
| **Routing rule** | Feature-based (sync/async) | Feature-based (sync/async) |
| **Cascade ratio** | 95/5 | 96/4 |
| **Transcreation** | Not active (no setup needed) | Integrated into triage (fine-tuned) |
| **Total AI COGS** | **$0/mo** | **~$900-1,700/mo** |

The jump from 10 to 100k users changes the **hosting** (API → self-hosted), not the **strategy** (triage/frontier split remains feature-based). The fine-tuned 8B is the key enabler — it keeps 96% of traffic off per-token APIs entirely.


## Pricing Model


1. Current Pricing
Right now, your app appears to be 100% Free (or pre-monetization). You are absorbing all the API inference and database costs to build the user base. This is standard for an MVP, but it will burn cash rapidly as you scale to thousands of active groups during the World Cup.

2. Which Pricing Model Should You Use?
Let's evaluate the standard SaaS models against your consumer social app:

Seat-based (e.g., $1 per user/month): FAIL. 
If a group of 10 friends wants to play, and 1 friend refuses to pay $1, the entire group will abandon your app and go back to a free Excel sheet on WhatsApp. You cannot have individual paywalls in a multiplayer social game.

Usage-based (e.g., Buy "AI Tokens"): FAIL. 
Consumers hate micro-transactions for basic chat. Nobody wants to calculate if they have enough credits left to ask the bot a question.
Outcome-based: N/A.
Unless you pivot to real-money sports betting (which has massive legal/compliance barriers), this doesn't apply.

3. The Proposed AI Pricing: The "Admin-Sponsored" Hybrid Model
You should use a Freemium + Admin-Sponsored Group Pass model.

Here is exactly how it works:

The Free Tier (The Core Game): Any user can create a group, invite friends, enter predictions, and see the basic leaderboard for free. This guarantees zero friction for user acquisition and viral growth.
The "Agente Mundial" Group Pass ($4.99 One-Time Fee per Tournament): The Group Admin (the "Alpha" friend who organized the pool) pays a single $4.99 fee.
What it unlocks: This single payment unlocks the AI features for the entire group for the duration of the 30-day tournament. It activates the AI Personalities (Tomás Roncero, Darth Vader), the Daily Summaries, and the Breaking News RSS broadcasts in their group chat.
Why this is the ultimate pricing strategy for your app:
Zero Friction for the Masses: 9 out of 10 users never hit a paywall. They just click the invite link and play.
Targeting the Highest Intent User: The "Group Admin" is already the most invested person in the friend group. They took the time to organize the game. They are highly likely to drop $5 (the price of a beer) to make their month-long group chat hilarious and engaging for their friends.
Phenomenal Margins: As we calculated earlier, the actual AI COGS for a group of 10 people for the entire month is roughly $0.35. If you charge the Admin $4.99, you are operating at a 93% Gross Margin while looking like a hero to the other 9 friends who get to use the AI for free.





**Current pricing:**
**Proposed AI pricing:**
**Model:** seat-based / usage-based / outcome-based / hybrid

## Stress Tests

| Scenario | Impact on Margin | Response |
|----------|-----------------|----------|
| Inference costs 3x | | |
| Heaviest segment doubles | | |
| Model provider raises prices 50% | | |



Here is the Stress Test Audit based on the "Admin-Sponsored Group Pass" pricing model ($4.99 per group) and your current AI architecture.

Because your baseline AI COGS (Cost of Goods Sold) are incredibly low (roughly ~$0.35 total per group per month), your margins are highly resilient to pricing shocks.

Scenario	Impact on Margin	Response
1. Inference usage (tokens) goes 3x

(Users trigger the bot way more often than projected).	Low Impact.
Inference cost per group jumps from ~$0.15 to ~$0.45. Total COGS increases to ~$0.65. Gross margin drops slightly from 93% to 87%. You are still highly profitable.	Implement a Humor Cooldown: Do not block the users with an ugly "Rate Limit" error. Have the bot reply: "¡Me voy al banquillo a beber agua!" (I'm going to the bench to drink water!) and automatically ignore prompts for the next 15 minutes to curb runaway token spam gracefully.
2. Heaviest segment doubles

(The top 10% of "power-user" groups chat 2x more than average).	Medium Impact (Infrastructure).
Margin impact is minimal, but the real threat is database strain. If power-users double their WebSocket and MongoDB read/writes during a live match, your servers might crash for everyone.	Aggressive RAG Caching: For the heaviest groups, cache the chatContext locally in Redis or memory for 60 seconds rather than querying MongoDB on every single message. Rate-limit DB writes during peak match minutes.
3. Model provider raises prices 50%

(Groq suddenly hikes API prices).	Zero Impact.
Inference cost increases from ~$0.15 to ~$0.22. Margin impact is less than 2%. It is virtually undetectable on your bottom line.	Silent Swap: Because you use open-source weights (Llama 3 / Qwen) and standard OpenAI-style message arrays, you simply change your API key and Base URL to a competitor (e.g., Together.ai, Fireworks, or DeepNova) hosting the exact same weights. The users won't notice a thing.
The Ultimate Conclusion on Pricing Risk:
Your product is computationally cheap but emotionally high-value. Because you are monetizing the fun of the game rather than selling raw AI utility, you have enormous buffer room. You could suffer a 10x spike in API costs and still be a profitable business. Your main focus should remain entirely on server stability (MongoDB/Websockets) during live match spikes, not on LLM API costs.



## Board One-Pager
<!-- Before/After: Old SaaS revenue vs. AI usage revenue for your product -->

**Before (traditional SaaS):**
**After (AI-enabled):**
**Net margin shift:**

Executive Summary: The AI Margin Expansion (Agente Mundial)
📉 Before (Traditional Web2 / SaaS Model)
The Product: A static utility. A digital spreadsheet where users input predictions and view a math-based leaderboard.
The Engagement Loop: Entirely dependent on the users. If the friend group is quiet or busy, the app feels dead. Engagement only spikes exactly when a match ends.
The Retention Problem: Users who perform poorly (last place) churn immediately because checking a leaderboard where they are losing is not fun.
Monetization: Extremely difficult. Users refuse to pay a subscription fee just to see a basic math leaderboard they could replicate in Excel.
📈 After (AI-Enabled Entertainment Platform)
The Product: A dynamic, stateful game. The leaderboard is now commentated in real-time by unhinged, culturally tailored AI personas (Montes, Vader, Romano).
The Engagement Loop: Automated and continuous. The AI proactively generates personalized content (Daily Summaries, direct roasts, breaking news broadcasts), creating notifications that pull users back into the app even when there are no live matches.
The Retention Solution: Entertainment supersedes winning. Users in last place stick around simply because the AI's personalized roasting is funny and the group chat is active.
Monetization: Unlocks the "Premium Group Pass." Users won't pay for math, but Group Admins will gladly pay $4.99 to unlock a hilariously aggressive AI referee to entertain their friends for a month.
💰 Net Margin Shift
Customer Acquisition Cost (CAC): Decreases. The AI's funny summaries are highly shareable screenshots, driving organic, viral top-of-funnel growth.
Cost of Goods Sold (COGS): Marginal increase. API inference (Llama 3 via Groq) adds roughly $0.35 per group, per month.
Net Margin Impact: Massive Expansion. By introducing a $4.99 one-time Admin Pass against a $0.35 AI cost, the business transforms from a free, un-monetizable utility into a premium entertainment product operating at >90% Gross Margins.

