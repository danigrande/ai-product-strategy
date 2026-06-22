# Kill Switch Audit

## Vendor Dependency Assessment

| Dimension | Current State | Risk Level | 48-Hour Action |
|-----------|--------------|------------|---------------|
| **Provider** | Groq (Primary) + HuggingFace (Fallback). | L | Ensure HuggingFace API key is actively monitored and limits are raised. Identify a 3rd backup (e.g., Together.ai) just in case. |
| **Abstraction** | Direct SDK imports (groq-sdk and @huggingface/inference) hardcoded inside groqEngine.js. |  M  | Wrap the LLM calls in a generic callLLM(messages, provider) interface. Avoid tying the logic directly to the Groq SDK. |
| **Routing** | Hardcoded try/catch block (Tries HF first, if it fails, falls back to Groq). |  M  | Implement a lightweight router (like LiteLLM or an internal switch) to dynamically route based on latency or cost, rather than hardcoded logic. |
| **Eval** | No automated Evals. You are flying blind if you switch models; you won't know if "Tomás Roncero" stops being funny until users complain. | H | Extract 50 known "Upvoted" interactions from your ChatbotFeedback DB and create a "Golden Dataset" to test any new model before routing traffic to it. |


## Portability Score
80% Ready (Highly Portable) Your prompts are standard system and user strings, and your conversation format matches the OpenAI standard ([{role: 'system', content: ...}]). Because you are heavily utilizing open-source models (like Qwen/Qwen2.5-7B-Instruct or Llama 3 on Groq), you are completely un-tethered from proprietary OpenAI features (like Assistant APIs or function calling). If Groq dies, you can point your code to Together.ai, Anyscale, or Fireworks in less than 2 hours.

## If Groq doubles pricing tomorrow:
You are highly insulated. Because you rely on open-source weights (Llama/Qwen) rather than proprietary models (like GPT-4), Groq's API is just compute. Action: You simply change the base URL and API key to a cheaper inference provider (Together.ai, DeepNova, or even running a local model on RunPod) that hosts the exact same open-source model. The users will not notice a single difference in the bot's personality because the model weights are identical.

## If Groq or HuggingFace ships a competing product:
Note: Groq is a hardware company, so they won't build a World Cup prediction app. But hypothetically, if OpenAI or an AI vendor shipped a generic "Sports Chatbot" app...

What's defensible that they can't replicate? Your defense relies on State and Social Graph. An AI provider can build a bot that talks like Andrés Montes. They cannot build a bot that knows exactly how many "Plenos" your friend Dani has in his private group chat, what Dani's specific dislikes are in his User.js profile, and exactly what the group was making fun of him for 10 minutes ago in the RAG history.

They sell the engine; you own the car, the racetrack, and the drivers.


Here is the corrected Kill Switch Audit, focused on provider and infrastructure dependency — not product viability.

---

# Kill Switch Audit

## Vendor Dependency Assessment

| Dimension | Current State | Risk Level | 48-Hour Action |
|-----------|--------------|------------|---------------|
| **Provider** | **Groq** (primary) for all inference: chat responses, daily summaries, transcreation. **HuggingFace Inference API** (fallback) for when Groq errors. **Self-hosted GPU** (planned) for 96% of traffic at scale — 8B triage, 8B judge, 8B fine-tuned transcreation. **GPT-4o-mini** (planned) for 4% async summaries only. | **L** | No urgent action. Groq free tier handles current scale. Validate that the self-hosted vLLM endpoint matches the OpenAI-compatible format before World Cup start so the fallback chain is production-ready. |
| **Abstraction** | Direct SDK imports (`groq-sdk` and `@huggingface/inference`) hardcoded in `groqEngine.js:7-8`. Self-hosted calls would use a different client (OpenAI-compatible fetch to vLLM). | **M** | Wrap all LLM calls in a generic `callLLM(messages, provider)` interface. Both Groq and vLLM already follow the same OpenAI message format (`[{role, content}, ...]`). The abstraction is straightforward — the current code just hasn't been refactored. |
| **Routing** | Hardcoded try/catch block: first tries Groq (`groqEngine.js:311`), if it fails, falls back to HuggingFace (`groqEngine.js:357`). No dynamic routing based on latency, cost, or model capability. No configurable priority chain. | **M** | Implement a lightweight provider router (priority list: self-hosted GPU → Groq → HuggingFace). The fallback chain already exists; it just needs to be configurable rather than hardcoded. LiteLLM is one option, but a 50-line internal router would work just as well. |
| **Eval** | **Full automated eval system exists.** `evalRunner.js` runs 101 golden test cases across 6 datasets (intent, personality, language, transcreation, edge, summary). `EvalRun` model persists results with regression detection and A/B comparison (`--compare` flag). Dev dashboard (`devDashboard.js`) shows pass rate trends, per-dataset breakdowns, and regression alerts. Judge service evaluates every response in real-time (language purity + quality scores). | **L** | No 48-hour action needed. The eval system is production-ready. Optional: integrate the eval runner into a CI pipeline so every model change triggers an automatic eval run before deployment — currently it's CLI-only. |

---

## Portability Score

**85% Ready**

| Dimension | Score | Reasoning |
|-----------|-------|-----------|
| **Model weights** | 100% | All open-source (Llama 3.1 8B, Qwen2.5 7B). No proprietary weights. Can run on any provider or self-hosted. |
| **API format** | 100% | OpenAI-compatible message format across all providers: Groq, HuggingFace, vLLM, Together.ai, Fireworks. No vendor-specific SDK features used. |
| **Prompt engineering** | 90% | System prompts use no model-specific features (no tool use, no structured outputs, no special tokens). The Judge's `response_format: { type: 'json_object' }` is the only provider-specific feature — and it's optional (the parser handles plain-text fallback). |
| **Frontier dependency** | 80% | GPT-4o-mini (for 4% async summaries) is the only proprietary dependency. Has viable alternatives: Claude Haiku, Gemini Flash. Swapping requires re-running the eval suite to validate personality parity on the summary datasets. |

---

## If Groq doubles pricing tomorrow:

**Impact: Near zero.**

| Traffic share | Impact | Action |
|---|---|---|
| **96%** (self-hosted GPU) | None. Fixed GPU cost unaffected by API pricing. | No action needed. |
| **4%** (GPT-4o-mini) | None. Unrelated provider. | No action needed. |
| **0%** (Groq) | Currently 100% of traffic. The self-hosted cascade is planned, not deployed. If pricing doubles before the cascade is live: **High impact**. | Accelerate self-hosted GPU deployment. In the interim, switch Groq → Together.ai hosting the same model weights. Change one env var (`GROQ_BASE_URL`) and the API key. Users notice no difference. |

**If the cascade is live:** The only dependency on Groq is as a fallback provider. Double pricing on a fallback that handles near-zero traffic is irrelevant.

---

## If Groq or HuggingFace goes offline for 24 hours:

| Dependency | Traffic share | Can we survive? |
|---|---|---|
| **Self-hosted GPU** | 96% | Independent. No external provider dependency. |
| **GPT-4o-mini** | 4% | Independent. Different provider (OpenAI). |
| **Groq (fallback)** | ~0% (when cascade is live) | Yes. Self-hosted GPU is primary. |
| **HuggingFace (fallback)** | ~0% | Yes. Multiple fallbacks in the chain. |

**During the debug/development phase (no cascade yet):** If Groq goes offline, HuggingFace fallback handles traffic but with degraded throughput (free tier: 30 req/min). That's not enough for more than a few concurrent users. This is a genuine kill scenario at scale before the cascade is deployed.

---

## If Groq or HuggingFace ships a competing product:

They won't build a World Cup prediction app. But hypothetically, if an AI provider shipped a generic "Sports Chatbot" feature:

**What's defensible that they can't replicate?** State and Social Graph. An AI provider can build a bot that talks like Andrés Montes. They cannot build a bot that knows exactly how many plenos your friend Dani has in his private group chat, what Dani's specific dislikes are in his profile, and what the group was making fun of him for 10 minutes ago in the RAG history.

**The cascade strengthens this defense:** You own the inference infrastructure. You are not renting model access from a provider who could become a competitor. If Groq launches "Groq Sports Chat" tomorrow, you don't have to migrate — you're already running your own models on your own GPU.

**The TL;DR:** They sell the engine. You own the car, the racetrack, the drivers, and the mechanic.
