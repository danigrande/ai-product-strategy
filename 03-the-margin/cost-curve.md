# Cost Curve & Pricing Strategy

## Cost Model

Current MVP (10 users/month)

| Cost Category | Per-User/Month | Notes |
|--------------|----------------|-------|
| Inference (primary model) | 0$  |  Llama-3.1-8B-Instant Free tier on Groq |
| Inference (cascading/triage) | 0$ | Llama-3.1-8B-Instant Free tier  on Groq  |
| Infrastructure | 0$ | Free tier Render |
| Data/storage | 0$ | MongoDB Atlas Free tier |
| Human-in-the-loop | 0$ | Eliminated. Replaced by the automated ChatbotFeedback (Thumbs Up/Down) system inside the app. |
| **Total AI COGS** | 0$ | It's currently at cost 0. |


Real MVP (1000 users/month)
| Cost Category | Per 1000 User/Month | Notes |
|--------------|----------------|-------|
| Inference (primary model) | $15.00  |  Llama 3 8B is incredibly cheap (~$0.05 per 1M tokens). Assuming 1,000 users generate heavy banter and daily summaries, API costs on Groq/Together.ai will be minimal, but not zero due to rate limits. |
| Inference (cascading/triage) | 0$ | HuggingFace free tier handles occasional fallback traffic. |
| Infrastructure | $7.00 - $14.00 | Render "Starter" or "Standard" tier. You must pay for this so WebSockets don't sleep and background RSS cron jobs run reliably 24/7. |
| Data/storage | $9.00$ | MongoDB Atlas M2 cluster (2GB storage, backups enabled). Required to store heavy AILogs, feedback matrices, and group chat histories without crashing. |
| Human-in-the-loop | $0$ | Eliminated. Replaced by the automated ChatbotFeedback (Thumbs Up/Down) system inside the app. |
| **Total AI COGS** | 35$ | Total. (Or roughly $0.035 per user / month).




If went "enterprise" pricing - Assuming 100k users

| Cost Category | Per-User/Month | Notes |
|--------------|----------------|-------|
| Inference (Llama 3 8B via Groq/Together) | ~$1,200 - $1,500  | Token usage scales linearly. If 1k users cost $15, 100k users cost ~$1,500. At this scale, you might actually negotiate a "Provisioned Throughput" tier with a provider to guarantee latency during peak World Cup matches rather than paying per-token. |
| Infrastructure (Node.js/Socket.io Servers) | ~$300 - $400| You can no longer run on a single $7 Render instance. To handle 100,000 concurrent WebSocket connections during a Spain vs. Germany final, you will need a Load Balancer and multiple heavy compute instances (e.g., 4-6 Render 'Pro' instances) auto-scaling based on CPU. |
| Data/Storage (MongoDB Atlas) | ~$400 - $600 | You are no longer paying for storage space; you are paying for IOPS (Read/Write speed). When a match ends, 10,000 groups will query their leaderboards and save new AI Logs simultaneously. You will need a dedicated cluster (e.g., Atlas M30 or M40) to prevent the database from locking up. |
| Human-in-the-loop | 0$ | Eliminated. Replaced by the automated ChatbotFeedback (Thumbs Up/Down) system inside the app. |
| **Total AI COGS** | ~$1,900 - $2,500 | Total. (Or roughly $0.025 per user / month). |

## Cascading Strategy
<!-- Cheap model → frontier model routing logic -->

A Cascading Strategy (or Model Routing) absolutely makes sense for your product, but you need to invert the traditional approach.

Because your app is primarily a real-time social chat interface, speed and cost are vastly more important than deep reasoning for 95% of your interactions.

Here is exactly how a Triage/Cascading model should be implemented for "Agente Mundial":

The Cascading Strategy
Triage (Primary) Model: Llama 3.1 8B (via Groq).
Frontier Model: GPT-4o-mini or Claude 3.5 Haiku. (Do not use heavy models like GPT-4o or Opus; they are too slow and expensive for consumer social apps).
The Routing Rules
You don't need a complex semantic router to determine difficulty. You can route strictly based on the Feature Set (Synchronous vs. Asynchronous):

1. Route to Triage Model (Groq Llama 3):

Target: generateResponse (Real-time group chat banter).
Why: When a user tags the bot in the chat, they expect an immediate response. Groq's 500+ tokens/second speed is a massive UI advantage. A 7B/8B model is perfectly capable of looking at a user's stats and throwing a quick, 3-sentence insult in the voice of Tomás Roncero. You do not need frontier-level reasoning to make a football joke.
2. Route to Frontier Model (GPT-4o-mini / Claude 3.5 Haiku):

Target: generateDailySummary and Data Extraction.
Why: At the end of the day, your cron job passes a JSON leaderboard of 15 players and their exact stats to the LLM and asks it to synthesize a coherent, error-free summary. Smaller models (8B) are notorious for "hallucinating" numbers or failing at complex formatting when given large data arrays. Because this is an asynchronous background task (a cron job), the user doesn't care if it takes 4 seconds to generate. You need high accuracy here.
Expected Cascade Ratio
95% / 5%

95% of Volume (Triage): Chat interactions. In a group of 10 friends, the bot might be triggered 20 times during a 90-minute match.
5% of Volume (Frontier): Summaries and background tasks. The daily summary only runs once per group, per day.
Why this is the ultimate strategic move:
By adopting this 95/5 cascade ratio, you achieve the "Holy Trinity" of AI consumer apps:

Near-zero latency where the user feels it most (chat).
High reasoning and zero hallucinations where it matters most (daily leaderboard summaries).
Blended COGS (Cost of Goods Sold) that remain effectively as cheap as if you were running the entire app on open-source models, because the expensive frontier model is only firing once a day per group.

**Triage model:**
**Frontier model:**
**Routing rule:**
**Expected cascade ratio:**

## Pricing Model

**Current pricing:**
**Proposed AI pricing:**
**Model:** seat-based / usage-based / outcome-based / hybrid

## Stress Tests

| Scenario | Impact on Margin | Response |
|----------|-----------------|----------|
| Inference costs 3x | | |
| Heaviest segment doubles | | |
| Model provider raises prices 50% | | |

## Board One-Pager
<!-- Before/After: Old SaaS revenue vs. AI usage revenue for your product -->

**Before (traditional SaaS):**
**After (AI-enabled):**
**Net margin shift:**
