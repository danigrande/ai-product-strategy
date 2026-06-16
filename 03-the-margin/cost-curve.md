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

