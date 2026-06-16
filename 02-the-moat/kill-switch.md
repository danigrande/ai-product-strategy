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


