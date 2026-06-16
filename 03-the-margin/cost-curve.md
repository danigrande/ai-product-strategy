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
