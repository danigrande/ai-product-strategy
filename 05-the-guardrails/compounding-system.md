# Compounding System Design

## Feedback Loops

Loop	Iteration 1	Iteration n	Compounds?	Status
Quality Gate	Judge gives feedback → response corrected	Judge feedback from n-1 refines prompt for n	✅ Yes — each attempt learns from the last	✅ Live
HITL → Golden Dataset	Human approves → added to local_overrides	Benchmark runs against growing dataset → more tests, better coverage	✅ Yes — the golden dataset self-expands	✅ Live
RAG Context	Message → embedding → context for next response	Every new message enriches the vector store	✅ Yes — available context grows with each interaction	✅ Live
⏳ Meta-Judge	Judge evaluates response → human evaluates Judge	Judge self-calibrates from its own accuracy history	✅ Yes — the evaluator improves from its own evaluations	🔲 Planned
⏳ Response → Catchphrase → Prompt	Bot uses catchphrase → saved to banned list	List grows → bot forced to innovate → new phrases → more variety	✅ Yes — exhausts old patterns, forces creativity	⚠️ Partial (in-memory)
🔀 Cross-Domain Transfer
Loop	Source Domain	Target Domain	Transfer Mechanism	Status
Transcreation	ES/EN humor (jokes, cultural references)	8 non-Latin languages (KO, TH, AR, JA, RU, ZH, VI, HI)	Cultural adaptation guided by anchors	✅ Live
Personality Anchors	Humor mechanisms (hyperbole, dark_humor)	Different personalities (7 profiles)	buildAnchorBlock() injects into system prompt	✅ Live
⏳ Cross-Group Preference	Upvote/downvote patterns from Group A	System prompt for Group B (similar users)	Preference clustering by demographics/group	🔲 Planned
⏳ Tournament Phase Transfer	Tone used in group stage (high-energy)	Tone in KO phase (more serious/analytical)	Phase→personality mapping in tournamentState.js	🔲 Planned
⏳ Prediction History → Bot Persona	User's past prediction hits/misses	Bot adjusts attitude (modest/arrogant) per user	Scoring history injected into user context in prompt	🔲 Planned
🌐 Network Intelligence
Loop	Network Nodes	Emergent Intelligence	Status
Group Personality Consensus	Admin + Group members	Admin picks personality → members react → admin may change → group "votes with feet"	✅ Live (indirect)
HITL Collective Judgment	Multiple human reviewers	Judge calibration improves with aggregated human agreement/disagreement	✅ Live
Feedback Voting	All users	Feedback items with most upvotes rise in priority → team knows what to build	✅ Live
⏳ Cross-User Pattern Mining	All groups and users	If ≥70% of Spanish groups prefer high energy in groups, system learns it as a general rule	🔲 Planned
⏳ Social Graph + Bot	User A mentions @agente + User B replies	Bot learns social dynamics: "A and B always argue → moderate tone"	🔲 Planned
⏳ Epidemic Alert	Group network + sensitive topic detection	If 3+ groups report a similar response as "rude" → global flag, bot corrects across all groups	🔲 Planned

 How They Connect
Recursive Learning      Cross-Domain Transfer       Network Intelligence
       │                        │                           │
       │  Quality Gate retries   │                           │
       ├─────────────────────────┤                           │
       │   improve with each     │                           │
       │   iteration             │                           │
       │                        ├───────────────────────────┤
       │                        │  HITL + Golden Dataset    │
       │                        │  feeds from multiple      │
       │                        │  reviewers (network) and  │
       │                        │  applies to all           │
       │                        │  personalities (cross-    │
       │                        │  domain)                  │
       │                        │                           │
       └──────────┬─────────────┘                           │
                  │                                         │
                  ▼                                         │
         RAG Context Store                                   │
         (recursive + cross-user)                            │
                  │                                         │
                  └─────────────────────────────────────────┘
                                       │
                                       ▼
                          The system improves with
                          every message, every review,
                          every benchmark run
                          






| Loop | Input | Output | Compounds? | Status |
|------|-------|--------|-----------|--------|
| Quality Gate | Judge gives feedback → response corrected | Judge feedback from n-1 refines prompt for n | ✅ Yes — each attempt learns from the last | ✅ Live |
| | | | Y/N | active / broken / missing |
| | | | Y/N | active / broken / missing |

**Broken loop identified by partner:**
**Fix plan:**

## Context Connectivity
<!-- How does knowledge flow across teams and domains? Where does it silo? -->

Flow	Source → Target	What Travels	Mechanism	Persistence	Status
User → Bot	Message → messageHandler → groqEngine	Text, userId, groupName, game state, RAG, web context	Socket.IO + function params	Ephemeral (per-request)	✅ Live
Game State → Bot	DB (predictions, reality) → scoringEngine → leaderboard → system prompt	Rankings, points, exact hits, match drama	Cache (30s TTL) + template injection	Ephemeral (TTL-bound)	✅ Live
Chat History → Bot	Message collection → ragService → system prompt	Raw chat text mentioning player (last 10-30 msgs)	Regex + name matching	Persistent (DB), but retrieval is session-only	✅ Live
Response → Judge	groqEngine output → judgeService	Response text, system prompt, anchors, target lang	LLM call (llama-3.1-8b) + regex fast-path	Ephemeral (per-request)	✅ Live
Judge → Retry	Judge scores → retry prompt in generateWithQualityGate	judgesFeedback string (what went wrong)	Loop param (max 3 iterations)	Ephemeral (intra-request only)	✅ Live
Force-Approved → HITL → Golden Dataset	Exhausted retry → HumanReview → promote → local_overrides.json	Query, response, judge scores	MongoDB + file write	✅ Persistent	✅ Live
User Downvote → HITL → Judge Calibration	Chat rating → HumanReview → agreement stats	messageId, rating, reason	MongoDB + aggregated stats	✅ Persistent	✅ Live
Feedback → LangFlow → PRD	User feedback → AI analysis → PRD document	Feedback text, priority, acceptance criteria	LangFlow API + MongoDB	✅ Persistent	✅ Live
Benchmark → Regression Alert	Eval run n vs n-1 → pass rate diff	Per-test pass/fail, delta	JSON comparison	✅ Persistent (runs stored)	✅ Live
Catchphrases → Cross-User	User A triggers persona → recentBotOutputs map → User B gets different phrase	Last 3 outputs per personalityId	In-memory Map (global, all groups)	❌ Ephemeral (lost on restart)	⚠️ Live
Admin → Group Personality	Admin sets ai_personality → bot persona for the group	Personality ID → anchors + system prompt	DB field + code switch	✅ Persistent	✅ Live
Group Identity → Bot Context	Group name → rules, members, prediction mode, phase	Scoring rules, member list, prediction mode	DB fetch per request	Ephemeral (30s cache)	✅ Live
🧱 Where Knowledge Silos
#	Silo	What's Trapped	Why It Hurts	Files
1	RAG ↔ Web Search	A user asking "¿quién ganó y cómo voy?" gets EITHER web data OR chat history, never both	Bot gives incomplete answers; user must rephrase	messageHandler.js:240-361
2	Per-Group RAG	Group A's messages never inform Group B's bot	Same user in 2 groups gets no cross-group continuity	ragService.js:53-62
3	Embeddings computed but unused	vectorizeMessage() stores embeddings, retrieveContextForPlayer() uses regex only	Semantic search capability exists but is dead code	ragService.js
4	Judge is stateless	Each judgeResponse() starts from zero — no memory of "this personality/ language fails a lot on purity"	Same mistakes repeated across requests; no trend-based threshold adjustment	judgeService.js
5	No per-group/personality eval config	All languages, all personalities share minLanguagePurity:8, minQuality:6	Transcreated Korean held to same standard as native Spanish; roncero (hyperbole) judged same as fabrizio_romano (factual)	config.js:47-48
6	No transcreation memory	"toma, toma, toma" → Korean costs a full LLM call every time	Repeated work, no translation memory, no cache of successful adaptations	transcreationService.js
7	No user language preference	Language re-detected from scratch every message	User who always writes in Korean pays detection cost every time; no fallback to "last used language"	languageRouter.js
8	Anchors are hardcoded	Humor mechanisms, catchphrases, cultural domains are immutable	Personality tuning requires code changes; no A/B testing; no group-level personality variants	anchors.js
9	No cross-group dashboard analytics	/evals/stats groups by language and personality, NOT by group	Cannot answer "which group has the best/worst bot quality?"	routes/devDashboard.js
10	No feedback↔AILog linkage	A chatbot_feedback entry (messageId, rating) is not correlated to its AILog	Cannot compute "what % of downvoted responses also failed the Judge?"	routes/devDashboard.js
11	Group cache is memory-only	Leaderboard, rankings, profiles cached per-group with 30s TTL	Lost on server restart; no warm-up on boot	messageHandler.js:19-20
12	No cross-response memory (beyond catchphrases)	recentBotOutputs tracks 3 catchphrases per persona, not topics or conversation state	Bot starts each exchange fresh; no multi-turn coherence beyond what RAG provides	groqEngine.js:27-39
🔁 Connectivity Map
                         ┌──────────────┐
                         │   SILO #1    │
                         │  RAG ↔ Web   │  ── mutually exclusive
                         └──┬───────────┘
                            │
              ┌─────────────▼──────────────┐
              │     messageHandler.js       │
              │  (assembles context, per    │
              │   request, no cross-group)  │
              └──────────┬──────────────────┘
                         │
                         ▼
              ┌─────────────────────┐
              │   groqEngine.js     │
              │  (stateless per     │── SILO #12 (no multi-turn memory)
              │   call, catches     │── SILO #4 (judge feedback ephemeral)
              │   global across     │── flows: catchphrases cross-users ✅
              │   groups/ users)    │
              └──────────┬──────────┘
                         │
              ┌──────────▼──────────┐
              │    judgeService.js   │── SILO #4 (no cross-request memory)
              │   (stateless, per    │── SILO #5 (same thresholds for all)
              │    request)          │
              └──────────┬──────────┘
                         │
              ┌──────────▼──────────┐
              │ transcreationService │── SILO #6 (no cache/memory)
              │  (stateless, per     │
              │   request)           │
              └─────────────────────┘

    ┌────────────┐    ┌────────────┐    ┌────────────┐
    │  RAG       │    │  Anchors   │    │ Language   │
    │  SILO #2-3 │    │  SILO #8   │    │  SILO #7   │
    │  per-group │    │  hardcoded │    │  no memory │
    │  + no      │    │  + global  │    │  + stateless│
    │  embedding │    │            │    │            │
    └────────────┘    └────────────┘    └────────────┘

                    ┌─────────────────────┐
                    │   MongoDB (DB)       │
                    │   AILog ── write-only │── persistent but runtime never reads
                    │   Feedback ── no link │── SILO #10
                    │   to AILog           │
                    │   HITL ── linked ✅   │
                    │   Golden Dataset ✅   │
                    └──────────┬──────────┘
                               │
                    ┌──────────▼──────────┐
                    │  devDashboard.js     │
                    │  SILO #9-10          │
                    │  (no per-group evals,│
                    │   no feedback↔log)   │
                    └─────────────────────┘







## Governance Policy

**Scope:**
**Autonomy boundaries:**
**Escalation triggers:**
**Audit cadence:**
**Regulatory exposure (EU AI Act / other):**

## Agent Topology
<!-- If using agents: what can each agent do? What can't it do? Who approves what? -->

## Shadow AI Audit

| Tool | Owner | Risk Level | Decision |
|------|-------|-----------|----------|
| | | H / M / L | keep / govern / kill |
| | | H / M / L | keep / govern / kill |
| | | H / M / L | keep / govern / kill |

**Total tools found:**
**Tools after triage:**
**Estimated hidden spend:**
