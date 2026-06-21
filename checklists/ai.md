[Home](../README.md) › **AI/ML Checklist**

# AI/ML Engineer Job-Readiness Checklist

`Checklist` · `9 stages` · `Tick what you can prove`

This is the skills inventory that gets you hired to build on top of LLMs in 2026 - integrate, prompt, retrieve, evaluate, and design systems you can trust. It follows the [AI/ML Engineer Roadmap](../roadmaps/ai.md) from junior integration work up to senior RAG, eval, and agent design.

Tick what you can already do in production, on real broken systems, not in a notebook you wrote once. The gaps are your study list. When you can't tick an item honestly, there's a ticket for it - see the bottom of the page.

## 🔌 Stage 1 - LLM API integration (junior)

- [ ] Send a chat completion request with `model` + `messages` to a `/v1/` endpoint and read the response
- [ ] Diagnose a "model not found" / 404 from a wrong base URL or missing field
- [ ] Use system, user, and assistant roles correctly, putting behavior and format rules in the system role
- [ ] Explain why LLMs are stateless and what that means for building a conversation
- [ ] Handle API errors (timeouts, rate limits, 5xx) with retries and graceful failure

## 🧱 Stage 2 - Structured output (junior)

- [ ] Strip markdown code fences from a model reply before parsing JSON
- [ ] Parse LLM JSON defensively with try/except and a fallback or retry
- [ ] Use a provider JSON mode / structured-output feature to force valid JSON
- [ ] Validate parsed output against a schema (e.g. Pydantic) so bad shapes fail loudly

## 🚀 Stage 3 - Building LLM endpoints (junior)

- [ ] Wire an API endpoint through the prompt-wrap, call, validate, return loop
- [ ] Build a constrained system prompt that returns exactly the format the endpoint needs
- [ ] Never return raw model output without a validate or parse step
- [ ] Ship a working LLM-backed endpoint (e.g. summarization) end to end

## ✍️ Stage 4 - Prompt engineering (junior to mid)

- [ ] Constrain a classifier to a fixed label set with no explanation, using the prompt alone
- [ ] Use few-shot examples to lock in an output format
- [ ] Set temperature appropriately (near 0 for classification/extraction, higher for creative tasks)
- [ ] Iterate on a prompt by changing the system message before reaching for a bigger model
- [ ] Recognize when a problem is a prompt fix vs a code fix vs a model swap

## 💬 Stage 5 - Stateful and streaming chat (mid)

- [ ] Add conversation memory by resending history each turn
- [ ] Cap history with a sliding window or running summary to control cost and latency
- [ ] Explain why unbounded history grows cost linearly and eventually hits the context limit
- [ ] Stream tokens to the client over SSE with `stream=True`
- [ ] Debug a stream that buffers (proxy buffering, missing per-chunk flush) into a slow single response
- [ ] Reason about time-to-first-token vs total latency for UX

## 🔎 Stage 6 - Embeddings, vector search, and RAG retrieval (mid)

- [ ] Explain what an embedding is and why cosine similarity measures semantic closeness
- [ ] Index documents into a vector database and query top-k by similarity
- [ ] Build the RAG retrieval loop: embed docs at ingest, embed query at lookup, return top-k
- [ ] Chunk documents on natural boundaries with sensible size and overlap
- [ ] Diagnose retrieval failures caused by bad chunking ("the answer was in the docs but RAG missed it")
- [ ] Explain how RAG narrows hallucination and why retrieval quality, not the model, usually decides the feature

## 🛠️ Stage 7 - Agents and tools (mid to senior)

- [ ] Implement function/tool calling: let the model choose a tool and emit structured args, then run it deterministically
- [ ] Explain why tool calling beats letting the model answer math or live-data questions directly
- [ ] Feed a tool result back into the model and continue the conversation
- [ ] Validate tool arguments before executing them

## 💸 Stage 8 - Cost and performance (mid)

- [ ] Add a cache-aside layer keyed on the prompt (e.g. Redis) with a TTL
- [ ] Estimate cache savings from a hit rate and explain why cost scales linearly with requests
- [ ] Route simple requests to a smaller model and reserve the large model for hard cases
- [ ] Trim prompts and cap history to cut token spend
- [ ] Measure tokens-per-request and cache hit rate instead of guessing

## 🧠 Stage 9 - Senior: RAG systems, eval, and agents

- [ ] Build an end-to-end RAG pipeline as one system: chunk, embed, retrieve, augment, generate
- [ ] Build an eval harness: golden set, scoring function, runner, pass rate
- [ ] Make the LLM call injectable so one harness evaluates any prompt, model, or pipeline version
- [ ] Choose scoring methods (lexical, embedding similarity, LLM-as-judge) and explain their trade-offs
- [ ] Gate prompt and model changes on eval pass rate, like tests gate code
- [ ] Build a decide-act-observe agent loop with stop conditions and an iteration cap
- [ ] Handle failed tool calls by feeding the error back as an observation, not crashing
- [ ] Add observability (per-step logging) so agent failures are visible

## 📐 ML fundamentals (cross-level)

- [ ] Explain overfitting, detect it from a train/validation gap, and prevent it
- [ ] Define precision and recall and choose which to optimize by error cost
- [ ] Explain why data is split into train, validation, and test sets and why the test set stays held out
- [ ] Pick an evaluation metric that matches the task and its failure costs

> [!IMPORTANT]
> **Can't tick it? Prove it.**
> Every unchecked box is a ticket. On HeyDevJob you fix real broken LLM systems - a dead API integration, RAG that retrieves nothing, an agent that loops forever, a prompt with no eval gate - in a live cloud workspace from your browser. Each fix you ship lands on a portfolio hiring managers can open and click. The junior tier is free, no card, no setup.
>
> **Start your portfolio →** [heydevjob.com/ai](https://heydevjob.com/ai)

---

**Explore AI/ML** · [📍 Roadmap](../roadmaps/ai.md) · [🛠️ Projects](../projects/ai/README.md) · [💬 Interview](../interview/ai.md) · [✅ Checklist](ai.md)
