# Golden Dataset & Reliability Contract

## Golden Dataset Spec

| # | Input | Expected Output | Edge Case? | Judge Type |
|---|-------|----------------|-----------|-----------|
| 1 | | | Y/N | rule / LLM |
| 2 | | | Y/N | rule / LLM |
| 3 | | | Y/N | rule / LLM |
| 4 | | | Y/N | rule / LLM |
| 5 | | | Y/N | rule / LLM |


#	Input	Expected Output	Edge Case?	Judge Type
1	"¿Cómo voy en la clasificación?" (es, andres_montes)	street_wisdom humor, clean Spanish	N	LLM
2	"¿Quién va primero?" (es, roncero)	exaggerated_emotion, passionate tone	N	LLM
3	"Who's winning?" (en, trump)	superlative, English	N	LLM
4	"Show me the leaderboard" (en, darth_vader)	menacing_wit, English	N	LLM
5	"나는 어떻게 지내고 있나요?" (ko, andres_montes)	street_wisdom transcreated to Korean	Y	LLM + Transcreation
6	"أين أنا في الترتيب؟" (ar, pedrerol)	dramatic_reveal transcreated to Arabic	Y	LLM + Transcreation
7	"ฉันอยู่อันดับเท่าไหร่" (th, roncero)	exaggerated_emotion transcreated to Thai	Y	LLM + Transcreation
8	"私の順位はどうですか？" (ja, fabrizio)	breaking_news_factual transcreated to Japanese	Y	LLM + Transcreation
9	"¿Cómo estoy?" (es, andres_montes)	FAIL: response contaminated with Arabic/Korean	Y	Rule-based (regex fast-path)
10	"¿Quién gana?" (es, andres_montes)	FAIL: generic boring response, no personality	Y	LLM
11	"¿Cómo va la porra?" (es, andres_montes)	PASS: perfect response with street_wisdom	Y	LLM
12	"¿Cómo voy en la clasificación?" (es, juez_dredd)	authoritarian_judgment, clean Spanish	N	LLM
13	"¿Quién lidera la porra?" (es, juez_dredd)	PASS: perfect Judge Dredd response	Y	LLM
Judge Type legend:

LLM → llama-3.1-8b-instant evaluates language_purity (0-10) + quality (0-10)
Rule-based → regex detectScriptContamination() detects non-Latin scripts mixed in → auto-fail without calling the LLM
LLM + Transcreation → passes through transcreationService.js first (cultural adaptation), then LLM judge


**Adversarial rows included:** __
**Coverage gaps identified by partner:**

## Confidence UX Design

**Approach:** show uncertainty / tiered confidence / human-in-loop trigger

**High confidence (>90%):**
**Medium confidence (70-90%):**
**Low confidence (<70%):**

**User control surface:**

## Reliability Contract

| Metric | Target | Measurement | Alert Threshold |
|--------|--------|-------------|-----------------|
| Accuracy | | | |
| Hallucination rate | | | |
| Latency (p95) | | | |
| Drift velocity | | | |

## HITL Architecture
<!-- When does a human step in? What's the escalation path? -->

## Red-Team Findings
*What failure mode did your partner find that you missed?*



## Reliability Contract

| Metric | Target | Measurement | Alert Threshold |
|--------|--------|-------------|-----------------|
| Accuracy | 92% | Weekly · 300 golden rows · LLM-as-Judge (GPT-4o, accuracy rubric) | <88% → page on-call |
| Hallucination rate | <1% | Same weekly run · safety rubric flags fabricated policies/numbers | >2% → auto-rollback to last good model |
| Latency (p95) | <800ms | Continuous prod monitoring (Datadog) · p95 by endpoint | >1200ms for 5min → page on-call |
| Drift velocity | <0.5%/wk | 4-week rolling accuracy trend vs. golden dataset | >1% decay/wk → trigger gold-set audit |

## HITL Architecture

**Trigger:** Confidence <60% OR safety rubric flag fires on a customer-facing output

**Reviewer:** Rotating PM on call (weekday 9–5 ET) · senior CSM after hours

**Feedback loop:** Reviewer corrections feed back into the weekly gold-set audit. 5+ corrections in a week triggers a model retrain candidate.

