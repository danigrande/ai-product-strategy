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
**What you capture today: In the chat, I capture the las 100 messages from each user and with a RAG I pass them over to the Agent to provide tailored responses. Users can also select preferences on the tone & personality of the Agent.**
**How it compounds: The more the users interact with each other, the more knowledge I get from them and can pick specific topics to generate engagement**

### Domain Context Loop - _3_/5
**What you capture today: Based on users conversations I can "push" notifications that are "breaking news" for them. i.e. they mention they are Brasil supporters -> I send push notifications about Neymar news from RSS feeds. However it does not affect the "gameplay" rules.**
**How it compounds:**

### Network Loop - _2_/5
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

---

## 90-Day Encroachment Plan

*Your partner played the Big Tech attacker. What was their plan to kill you?*

**Attacker:**
**Attack vector (target the weakest loop):**
**Weeks 1-4 - what they ship:**
**Weeks 5-8 - how they poach users:**
**Weeks 9-12 - why users don't come back:**
**Your defense:**
