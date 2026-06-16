# Golden Dataset & Reliability Contract

## Golden Dataset Spec

| # | Input | Expected Output | Edge Case? | Judge Type |
|---|-------|----------------|-----------|-----------|
| 1 | | | Y/N | rule / LLM |
| 2 | | | Y/N | rule / LLM |
| 3 | | | Y/N | rule / LLM |
| 4 | | | Y/N | rule / LLM |
| 5 | | | Y/N | rule / LLM |

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

