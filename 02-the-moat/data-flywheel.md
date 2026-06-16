# Data Flywheel Map

> Score each loop 1-5. Your weakest loop is where competitors attack first.
> The four loops below are the M2 starting point - adapt if your product has 2 or 6 loops instead of 4.

## Flywheel Loops

| Loop | What It Measures | Score 1 | Score 5 | Score |
|------|------------------|---------|---------|-------|
| **Correction** | Do users fix AI outputs? Is that signal captured and reused? | No capture | Automated retraining | _3_/5 |
| **Preference** | Does the product learn individual / team preferences over time? | Stateless | Deep personalization | _4_/5 |
| **Domain Context** | Does usage in one area improve quality in adjacent areas? | Siloed | Cross-domain transfer | _3_/5 |
| **Network** | Does each new user / team make the product better for everyone? | Isolated | Strong network effects | _2_/5 |

### Correction Loop - _3_/5


Based on the current architecture of your codebase (specifically chat.tsx, AILog.js, and ChatbotFeedback.js), here is the honest assessment of your feedback loop:

Do users "fix" AI outputs?
No. Users do not have the ability to directly edit or rewrite the AI's output to show it what it should have said. They can only provide a binary rating (👍 or 👎) and optionally type a reason via the ChatbotFeedbackSheet in chat.tsx.

Is that signal captured and reused?
Captured: Yes. Reused: No (not automatically). Every time a user votes, the signal is securely saved to your MongoDB ChatbotFeedback collection, linked directly to the message, the user, and the original prompt (via AILog). However, the AI Engine (groqEngine.js) does not currently look up past "upvoted" interactions to improve its future answers (few-shot prompting), nor is there an automated script that fine-tunes the model on this data.

Score: 3 out of 5
I would rate your current system a 3 (Captured for Manual Review).

Here is what the 1-to-5 scale looks like, and where you stand:

1 - No Capture: The bot speaks into the void. (You are past this)
2 - Passive Capture: You log the prompts, but users can't rate them. (You are past this)
3 - Active Capture for Manual Review (YOU ARE HERE): You successfully capture human preference (RLHF data) alongside the exact prompts and context. You, as the developer, can look at a dashboard, see that users are downvoting Tomás Roncero for being too repetitive, and manually tweak the system prompt in groqEngine.js.
4 - Dynamic Reuse (Prompt Injection): The engine dynamically searches the database for the 3 most highly-upvoted past interactions for that specific group, and injects them into the prompt as examples. (You are not doing this yet).
5 - Automated Retraining: An automated pipeline periodically takes all upvoted outputs, formats them into a JSONL dataset, and runs a DPO (Direct Preference Optimization) fine-tuning job on a local model, deploying the new weights automatically. (You are not doing this yet).
How to get to a 4 easily:
If you want to immediately increase your moat without building a complex retraining pipeline, you can update groqEngine.js to do Dynamic Few-Shot Prompting. When generating a response for a user, query the database for 1 or 2 messages to that specific user that received an "Upvote", and inject them into the prompt: "Here are examples of past jokes this user found funny: [Insert past upvoted outputs]. Match this tone."



**What you capture today: Users can thumbsup or thumbsdown the messages from the Agent. When thumbsdown they can also select the reason.**
**How it compounds:**





### Preference Loop - _4_/5


Based on the architecture of your application, here is the assessment of your current flywheel loop regarding individual and team preferences.

Does the product learn preferences over time?
Partially. The product has "memory" and "context," but it does not have an automated learning loop that updates its understanding of a user permanently.

Here is what your app currently does:

Manual Personalization: It pulls static data from User.js (like likes, dislikes, and humor_style) and injects it into the prompt.
Short-Term RAG Memory: It pulls recent chat history (chatContext) via RAG, so the bot knows what was just discussed in the group.
Repetition Avoidance: In groqEngine.js, you use recentBotOutputs to prevent the bot from repeating the same catchphrases.
However, if a user jokes in the chat, "I absolutely hate the French team now," the bot will remember it for a few hours (via RAG), but it will not permanently append "Hates France" to that user's profile in the database.

Score: 3 out of 5
I would rate your current system a 3 (Short-term Contextual Personalization).

Here is the scale to understand where you are and how to level up:

1 - Stateless: The bot has no idea who is talking. Every message is a blank slate. (You are past this).
2 - Static Profiling: The bot uses predefined DB fields (Name, Points) but has no memory of the chat. (You are past this).
3 - Short-Term RAG (YOU ARE HERE): The bot reads the recent chat window to inform its current response. It feels personalized in the moment, but the "learning" degrades as older messages fall out of the RAG context window.
4 - Automated Fact Extraction (The Next Step): The bot actively extracts permanent preferences from the chat and updates the database. (e.g., A background task analyzes the daily chat logs and automatically updates a user's dislikes array in MongoDB with new things they complained about).
5 - Deep Personalization (The Flywheel): The bot not only updates facts, but automatically adjusts its own systemPrompt weights based on user feedback. It learns that Group A loves dark humor and Group B prefers analytical sports stats, and it shifts its personality matrix permanently without developer intervention.
How to get to a 4 (The easiest flywheel upgrade)
To create a true learning flywheel, you can add a simple background cron job. At the end of every day, take the chat logs for a group, send them to Groq with a prompt like: "Analyze this chat log. Extract any new permanent facts, likes, or dislikes about the users. Output JSON." Then, automatically save those new facts directly into the likes and dislikes arrays in User.js. The next time the bot talks to them, it will reference a joke they made weeks ago, creating a deeply magical, compounding user experience.




**What you capture today: In the chat, I capture the las 100 messages from each user and with a RAG I pass them over to the Agent to provide tailored responses. Users can also select preferences on the tone & personality of the Agent.**
**How it compounds: The more the users interact with each other, the more knowledge I get from them and can pick specific topics to generate engagement**

### Domain Context Loop - _3_/5




In the context of a Domain Context loop (where usage in one feature or domain actively improves the quality of another), here is the assessment of your current architecture:

Does usage in one area improve quality in adjacent areas?
Mostly Unidirectionally. Your app currently acts as a funnel where data from adjacent areas flows into the Chatbot, but usage of the Chatbot does not flow back out to improve the other areas.

Here is the current flow of domains in your app:

News Domain (rssFeedService.js) $\rightarrow$ Chat Domain: Breaking news is injected into the chat and prompt (webContext).
Game Domain (Leaderboard/Stats) $\rightarrow$ Chat Domain: Player points and exact hits are injected into the prompt.
Chat Domain $\rightarrow$ Game Domain: [MISSING]. Bantering in the chat does not improve the prediction experience. The bot doesn't notice you always bet on Brazil and auto-fill your Brazil matches, nor does it generate prop bets based on chat debates.
Chat Domain $\rightarrow$ News Domain: [MISSING]. The RSS feed searches globally for static keywords (like "España" or "World Cup"). It does not dynamically adjust its keyword search based on the teams your specific friend group is debating in the chat.
Group A $\rightarrow$ Group B: [MISSING]. An upvoted interaction in one friend group does not currently improve the prompt or experience for a completely different friend group.
Score: 2 out of 5
I would rate your current Domain Context loop a 2 (Unidirectional Injection).

Here is the scale:

1 - Siloed: Chat is just a chat. Predictions are just a spreadsheet. They don't talk to each other. (You are past this).
2 - Unidirectional Injection (YOU ARE HERE): Data from Domain A (News) and Domain B (Scores) is dumped into Domain C (Chat) to make Domain C better. But Domain C doesn't make A or B better.
3 - Basic Cross-Pollination: Usage in the chat begins to affect the UI. For example, if users argue about Mbappé in the chat, the app highlights France's upcoming matches on the Prediction screen.
4 - Strong Cross-Domain Loop: The domains are mutually beneficial. If your group makes heavy predictions on Group C (Argentina/Mexico), the rssFeedService dynamically updates its filter to pull more news about those specific teams.
5 - Deep Cross-Domain Transfer: The entire app becomes a personalized organism. Chatting improves the News algorithm; Making Predictions improves the Chatbot's personality matrix; Upvoting jokes in Group A improves the humor model for Group B globally.
How to get to a 3 or 4 easily:
You can build a strong Cross-Domain loop by tying the RSS Feed Engine directly to the Prediction Data. Instead of hardcoding global worldCupKeywords in your config, update rssFeedService.js to look at the database of predictions. If 80% of the app's users just bet heavily on a specific underdog team, have the RSS feed dynamically add that team to its search keywords so it broadcasts breaking news about them. Suddenly, making a prediction (Domain A) makes the News engine (Domain B) smarter.





**What you capture today: Based on users conversations I can "push" notifications that are "breaking news" for them. i.e. they mention they are Brasil supporters -> I send push notifications about Neymar news from RSS feeds. However it does not affect the "gameplay" rules.**
**How it compounds:**

### Network Loop - _2_/5


In the context of a Network Loop (where every new user on the platform makes the product inherently more valuable for all existing users), here is the assessment of your current architecture:

Does each new user/team make the product better for everyone?
Locally, yes. Globally, no.

Your product has strong Intra-group (local) network effects, but zero Inter-group (global) network effects.

The Local Loop: When User A invites User B to their private "Porra" (prediction group), the game gets instantly better for that specific group. More competition, more points to fight over, and more context for the "Agente Mundial" to roast people.
The Global Silo: If 1,000 new friend groups suddenly download your app in Argentina, does the app get any better for a friend group in Spain? No. The data is completely siloed. The Argentine groups do not improve the AI's prompts, they do not contribute to a global "wisdom of the crowds" statistic, and they do not interact.
Score: 2 out of 5
I would rate your current Network Loop a 2 (Local/Siloed Network Effects).

Here is the scale to understand your position:

1 - Isolated: A single-player tool (like a calculator or a solo habit tracker). Another user downloading the app does absolutely nothing. (You are past this).
2 - Local Network Effects (YOU ARE HERE): A multiplayer game in private rooms. A new user makes the product better only for the specific friends in their room. Strangers add no value to each other.
3 - Aggregated Network Effects (The Next Step): Strangers' data is anonymized and aggregated to provide value to everyone. (e.g., "75% of all users globally picked France to win this match. Are you sure you want to pick Germany?")
4 - AI-Driven Network Effects: Every interaction from any user makes the core technology smarter for everyone else. (e.g., A group in Mexico upvotes a specific style of joke, and the central AI model updates its weights, making the bot funnier for a group in Spain).
5 - Strong/Marketplace Network Effects: True exponential value. Every new user actively increases liquidity or matching (like Uber, Airbnb, or a global matchmaking video game).
How to get to a 3 easily:
You have all the prediction data in your MongoDB. You can instantly create a Global Pulse or "Wisdom of the Crowds" feature. Before a user locks in their prediction for Spain vs. Germany, show them a small UI element: "Across 15,000 active groups, 82% of players are predicting a Spain victory."

By doing this, every new user who makes a prediction anywhere in the world makes the data slightly more accurate and interesting for every other user, instantly moving you from a 2 to a 3.








**What you capture today: The Agent does not improve by itself over time because more people use it. It would take development effort to analyse the signals and understand how to make the messages better.**
**How it compounds:**

**Total Flywheel Score: _12_/20**
**Weakest Loop: Network Loop**
**Fix for weakest loop: **

---

## Encroachment Threat Assessment

### 1. Platform Encroachment
**Attacker:**
**Vector:**
**Time-to-threat:**
**% of value at risk:**

### 2. Vertical Competitor
**Attacker:**
**Vector:**
**Time-to-threat:**
**% of value at risk:**

### 3. Adjacent Expansion
**Attacker:**
**Vector:**
**Time-to-threat:**
**% of value at risk:**

1. Platform Encroachment
Attacker: Meta (WhatsApp) / Apple (Apple Intelligence)
Vector: Native, multimodal AI agents embedded directly into the OS or default group chats. A user simply types in their existing WhatsApp group: "@MetaAI, we are doing a World Cup pool. Keep track of our scores and roast us like Andrés Montes."
Time-to-threat: 1-2 years. (The agents are rolling out now, but prompt-engineering a stateful, math-heavy game with perfect recall in a chaotic group chat is still highly error-prone).
% of value at risk: 30% - 40%. You survive because of your UI/UX. People want a visual, clickable leaderboard and dedicated prediction screens, not an endless wall of text trying to verify if the AI did the math right.

3. Vertical Competitor
Attacker: Sleeper (or ESPN/Yahoo Fantasy)
Vector: The massive incumbent in social fantasy sports. They already own the exact social graph (friend groups who play sports games together), the real-time sports data feeds, and the group chat infrastructure. They simply integrate the Groq API to create an "AI Commish" that roasts their existing users.
Time-to-threat: 6-12 months. (Sleeper has the engineering velocity to build this in a single sprint if they identify the trend).
% of value at risk: 90% - 100%. This is an existential threat. If your target audience is already on Sleeper for NFL or Premier League, and Sleeper launches an identical AI-banter World Cup pool, the friction for users to download your standalone app becomes insurmountable.

5. Adjacent Expansion
Attacker: Telegram (via Mini-App / Bot Developers)
Vector: Frictionless distribution. An indie developer builds exactly what you have built, but as a "Telegram Mini App." Because friend groups already talk on Telegram, they don't have to convince their friends to download a new app or create new accounts. They just add the bot to the group, and the UI opens instantly within the chat.
Time-to-threat: 3-6 months. (Telegram's bot and WebApp APIs are extremely mature, and developers are flooding the ecosystem right now).
% of value at risk: 70% - 80%. This attacks your biggest weakness: Go-To-Market Friction. In a B2B2C/Multiplayer app, the hardest part is getting 10 friends to install the same app. By expanding into the chat app where the friends already live, the adjacent attacker bypasses your entire acquisition funnel.




---

## 90-Day Encroachment Plan

*Your partner played the Big Tech attacker. What was their plan to kill you?*


Attacker: Meta (WhatsApp + Meta AI)

Attack vector (target the weakest loop): Your weakest loop is Go-To-Market Friction. In a multiplayer app, the "Alpha" friend has to convince 10 lazy friends to download a brand new app, create accounts, and learn a new UI. Meta attacks this friction by launching the feature exactly where the friend group already lives.

Weeks 1-4 - what they ship: Meta rolls out "WhatsApp Sports Bots" natively in the app, powered by Llama 3. Any user can type @MetaAI start a World Cup prediction pool in this chat. WhatsApp instantly pins a lightweight, native leaderboard to the top of the group chat. The AI is programmed to automatically announce the daily matches and gently roast whoever is in last place.

Weeks 5-8 - how they poach users: The World Cup is one week away. The "Alpha" friend in the group tries to get everyone to download "Agente Mundial," but three friends complain about downloading another app. The Alpha friend gives up, types @MetaAI in their existing group, and the pool is set up in 4 seconds. You lose the entire group before the first match kicks off.

Weeks 9-12 - why users don't come back: The tournament is now in the knockout stages. The game is highly stateful. The friends have accumulated points, a rich history of banter in the chat, and established rivalries. Moving a live, mid-tournament prediction pool to a third-party app is impossible. They are permanently locked into WhatsApp for the duration of the event.

Your defense: Meta is a massive, horizontal monopoly. They build tools that must work safely for 3 billion people. Your defense relies entirely on being hyper-vertical, gamified, and culturally unhinged.

The "Brand Safety" Moat: Meta AI is heavily RLHF'd to be polite, safe, and universally inoffensive. Your AI (Tomás Roncero, Darth Vader) is specifically prompted to be dramatic, aggressive, and highly culturally specific. People play these games to laugh at their friends; Meta won't let their AI be mean enough to be genuinely funny. You win on the humor quality.
Dedicated UX: A WhatsApp chat thread is a terrible interface for viewing complex stats. Your app has a beautiful, dedicated UI for tracking plenos (exact hits), honor points, and group rankings that a simple pinned chat message can't replicate.
The "Sanctuary" Argument: Many friend groups actually want a dedicated app for their betting pools to separate the noise of the game from their daily life/work WhatsApp messages. You position Agente Mundial as the dedicated "Sports Bar" away from the noise of their main inbox.
