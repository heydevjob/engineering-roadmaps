# AI/ML Engineer Roadmap

The path from "I can call an LLM API" to "I can ship a RAG system with evals" - the skills, in order, that get you hired as an AI engineer building real LLM features, not demos.

AI engineering in 2026 is **building on top of LLMs**: integrate, prompt, retrieve, evaluate. This roadmap follows that arc. Every node tells you **what** to learn, **why it matters**, and **how you'd prove it**, tagged with the kind of work it is:

- **Build** - create the feature from nothing
- **Debug** - find and fix a broken LLM integration
- **Tune** - improve prompt or retrieval quality
- **Optimize** - cut cost and latency
- **Design** - architect a RAG/agent/eval system (senior)

Work top to bottom. You learn to talk to a model before you build a system around it.

---

## Stage 1 - LLM API Integration

The OpenAI-compatible contract every provider speaks. Get the call right first.

### Calling an LLM API
- *What:* The request shape - `model` + `messages`, the `/v1/` endpoint, reading the response.
- *Why it matters:* Day-one integration hits cryptic 404s and "model not found" from one missing field. The OpenAI-compatible contract is the same across providers.
- *Prove it:* [Restore a Broken LLM API Integration](../projects/ai/restore-a-broken-llm-api-integration.md) *(Debug)*

## Stage 2 - Structured Output

Getting reliable, parseable data out of a model that loves to wrap it in prose.

### Parsing structured output
- *What:* Extracting JSON from a response - including stripping the markdown fences models add unbidden.
- *Why it matters:* LLMs wrap JSON in ```` ```json ```` even when told not to - the #1 JSON-parsing footgun in production LLM features.
- *Prove it:* [Parse JSON From an LLM](../projects/ai/parse-json-from-an-llm.md) *(Debug)*

## Stage 3 - Building LLM Endpoints

The prompt-wrap → call → validate → return pattern that repeats in every LLM product.

### Build an LLM-backed endpoint
- *What:* Wiring an API endpoint to an LLM with a tight prompt and response handling.
- *Why it matters:* This is the core loop of every LLM feature. Owning it end to end is the entry skill.
- *Prove it:* [Build a Text Summarization Endpoint](../projects/ai/build-a-summarization-endpoint.md) *(Build)*

## Stage 4 - Prompt Engineering

The cheapest lever on output quality - and the most underestimated.

### Constraining output with prompts
- *What:* Shaping the system prompt so the model returns exactly the format you need.
- *Why it matters:* An unconstrained classifier returns paragraphs when you need one word. Production features almost always need structured, constrained output.
- *Prove it:* [Improve an LLM Classifier Prompt](../projects/ai/improve-a-classifier-prompt.md) *(Tune)*

## Stage 5 - Stateful & Streaming Chat

Making a stateless model feel like a conversation that's fast.

### Conversation memory
- *What:* Resending history each turn and capping it with a sliding window.
- *Why it matters:* LLMs are stateless - "memory" is whatever you resend. Unbounded history balloons cost and latency until it times out.
- *Prove it:* [Add Conversation Memory to a Chatbot](../projects/ai/add-conversation-memory.md) *(Build)*

### Token streaming
- *What:* Streaming tokens to the client over SSE instead of buffering the whole reply.
- *Why it matters:* Perceived latency tracks first-token time, not total time. Streaming is what makes LLM UX feel fast.
- *Prove it:* [Restore Token Streaming on a Chat Endpoint](../projects/ai/restore-token-streaming.md) *(Debug)*

## Stage 6 - RAG: Retrieval

Grounding answers in your own data - the dominant pattern in applied AI.

### Chunking
- *What:* Splitting documents on sentence/paragraph boundaries so chunks stay coherent.
- *Why it matters:* Chunking is the most under-appreciated RAG knob. Garbage chunks silently tank retrieval quality no matter how good the model is.
- *Prove it:* [Fix the Chunking That's Wrecking RAG Retrieval](../projects/ai/fix-the-rag-chunking.md) *(Tune)*

### Vector search
- *What:* Embedding docs and queries and retrieving top-k by similarity from a vector database.
- *Why it matters:* RAG grounds LLM answers in domain knowledge. Retrieval quality - not the model - is what makes or breaks the feature.
- *Prove it:* [Power RAG Search With a Vector Database](../projects/ai/power-rag-with-a-vector-database.md) *(Build)*

## Stage 7 - Agents & Tools

Letting a model do things - call a calculator, hit an API - instead of guessing.

### Function calling
- *What:* Having the LLM choose a tool, then running it deterministically and returning the result.
- *Why it matters:* LLMs are bad at math and have no live data. You let them pick the tool and you run it - the core of every agent.
- *Prove it:* [Make an LLM Pick the Right Tool (Function Calling)](../projects/ai/make-an-llm-call-tools.md) *(Build)*

## Stage 8 - Cost & Performance

LLM bills scale with volume. A cache is the cheapest win you'll ever ship.

### Response caching
- *What:* A cache-aside layer keyed on the prompt so repeat questions skip the model.
- *Why it matters:* Even a 30% hit rate cuts the bill by a third in five lines of code. Costs scale linearly with requests.
- *Prove it:* [Cut LLM Costs With Redis Caching](../projects/ai/cut-llm-costs-with-redis-caching.md) *(Optimize)*

## Stage 9 - Senior: RAG Systems, Eval & Agents

Where you stop wiring calls and start designing systems you can trust.

### End-to-end RAG
- *What:* The full pipeline - chunk → embed → retrieve → augment → generate - as one system.
- *Why it matters:* The four RAG stages are universal. Knowing the pipeline cold is table stakes for an AI engineer interview.
- *Prove it:* [Build an End-to-End RAG Pipeline](../projects/ai/build-an-end-to-end-rag-pipeline.md) *(Build/Design)*

### Evaluation harnesses
- *What:* A golden set, a scoring function, and a runner that gives a pass rate - tests for prompts.
- *Why it matters:* Without evals you have no signal on "is the new prompt better?" Production AI quality is gated by eval pass rate, like unit tests gate code.
- *Prove it:* [Build an LLM Evaluation Harness](../projects/ai/build-an-llm-eval-harness.md) *(Design)*

### Agent loops
- *What:* The decide → act → observe loop that turns a model into an agent that uses tools.
- *Why it matters:* This loop *is* what an "agent" is - emit an action, run it, feed the observation back, repeat. The defining pattern of agentic AI.
- *Prove it:* [Build a Tool-Calling Agent Loop](../projects/ai/build-a-tool-calling-agent-loop.md) *(Design)*

---

## Where you are on the path

| Stage | You can... | Hiring level |
|-------|-----------|--------------|
| 1-3 | Integrate an LLM and build a working endpoint | Junior |
| 4-5 | Ship prompted, stateful, streaming features | Junior → Mid |
| 6-8 | Build RAG retrieval, tools, and cost controls | Mid |
| 9 | Design RAG systems, evals, and agents | Senior |

## Build it for real

Every project linked above is a live ticket on [HeyDevJob](https://heydevjob.com/ai) - a real LLM feature you build or fix in a cloud workspace, from your browser. The junior tier is free, no card, no setup. Each one you ship lands on a portfolio you can show.

**Start your portfolio →** [heydevjob.com/ai](https://heydevjob.com/ai)
