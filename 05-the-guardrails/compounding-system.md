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
🔗 How They Connect
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
