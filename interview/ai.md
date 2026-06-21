# AI/ML Engineer Interview Questions

The questions below mirror the work AI engineers actually get hired to do in 2026: build on top of LLMs, ground answers in real data, and ship features you can trust. They follow the [AI/ML Engineer Roadmap](../roadmaps/ai.md) from junior integration work up to senior RAG, eval, and agent design.

Each question has a tight, correct answer. Where a topic maps to a live HeyDevJob ticket, there's a "Practice it live" pointer - a real broken system you fix in a cloud workspace, not a quiz. Read the answer, then go prove you can do it.

---

## LLM API integration (junior)

**What does a minimal chat completion request look like, and why do you get "model not found" or a 404 on day one?**

The OpenAI-compatible contract is a POST to `/v1/chat/completions` with a JSON body of at least `model` and `messages` (a list of `{role, content}` objects). The two day-one failures are a wrong base URL (the `/v1/` path missing or a trailing-slash mismatch) and a `model` string the provider doesn't recognize. Most providers speak this same shape, so the fix is usually one field, not a rewrite.

Practice it live: [Restore a Broken LLM API Integration](https://heydevjob.com/ai) (AI-101)

**Why are LLMs called "stateless," and what does that mean for building a chatbot?**

The model holds no memory between calls - every request is independent, so any "conversation" is whatever history you resend in the `messages` array each turn. To make a chatbot remember, you accumulate prior turns and send them back on the next call. This is also why unbounded history is a real cost and latency problem: you re-pay for the entire transcript on every message.

**What's the difference between the system, user, and assistant roles?**

The system message sets behavior and constraints (persona, output format, rules) and applies for the whole conversation. User messages are the human's input; assistant messages are the model's prior replies, included so the model has context. Putting format and behavior constraints in the system role - not buried in the user turn - is the reliable way to shape output.

## Structured output (junior)

**An LLM is supposed to return JSON but your `json.loads()` keeps throwing. What's the most common cause and fix?**

Models wrap JSON in markdown code fences (```` ```json ... ``` ````) even when told not to, so the raw string isn't valid JSON. The robust fix is to strip the fences (and any leading/trailing prose) before parsing, rather than trusting the model to comply. In production you also wrap the parse in a try/except and either retry or fail gracefully, because the model will eventually return something malformed.

Practice it live: [Parse JSON From an LLM](https://heydevjob.com/ai) (AI-104)

**How do you make structured output more reliable beyond prompting for it?**

Use the provider's structured-output or JSON-mode feature (a response format or schema constraint) so the decoder is forced to emit valid JSON. When that isn't available, give a tight example in the system prompt, parse defensively (strip fences), and validate against a schema (e.g. Pydantic) so bad shapes fail loudly. Lower temperature also reduces format drift.

## Building LLM endpoints (junior)

**Walk through the core loop of an LLM-backed API endpoint.**

It's prompt-wrap, call, validate, return: take the request input, build a constrained prompt (system + user), call the model, validate or parse the response, then return clean data to the caller. Every LLM feature is a variation on this loop, which is why owning it end to end is the entry-level AI engineering skill. The validate step is what separates a demo from production - you never return raw model output blindly.

Practice it live: [Build a Text Summarization Endpoint](https://heydevjob.com/ai) (AI-106)

## Prompt engineering (junior to mid)

**Your classifier is supposed to return one label but returns a paragraph of explanation. How do you fix it with the prompt alone?**

Constrain the output in the system prompt: state the exact allowed labels, demand the label only with no explanation, and give one or two examples (few-shot) of the precise format. Lowering temperature toward 0 makes a classifier far more deterministic. Prompt constraints are the cheapest lever on output quality and almost always the first thing to reach for before adding code or a bigger model.

Practice it live: [Improve an LLM Classifier Prompt](https://heydevjob.com/ai) (AI-102)

**What is few-shot prompting and when does it beat zero-shot?**

Few-shot means including a handful of input-output examples in the prompt so the model infers the pattern and format from demonstration. It beats zero-shot when the task has a specific output shape, an unusual label set, or edge cases the model otherwise guesses on. The trade-off is token cost - more examples mean a larger, slower, pricier prompt - so you use the fewest examples that lock in the behavior.

**What is temperature and how should you set it for different tasks?**

Temperature controls randomness in token sampling: low (near 0) makes output near-deterministic and focused, high makes it more varied and creative. For classification, extraction, and structured output you want low temperature for consistency; for brainstorming or creative copy you raise it. It does not make the model "smarter" - it only widens or narrows the distribution it samples from.

## Stateful and streaming chat (mid)

**How do you stop conversation history from blowing up cost and latency?**

Cap the resent history with a sliding window - keep the system prompt plus the most recent N turns - or summarize older turns into a compact running summary. Because you re-pay for every token of history on each call, unbounded transcripts grow latency and cost linearly until they hit the context limit and error out. A token budget on the history is the standard production guard.

Practice it live: [Add Conversation Memory to a Chatbot](https://heydevjob.com/ai) (AI-107)

**Why stream tokens over SSE instead of returning the whole reply, and what breaks if streaming is misconfigured?**

Perceived latency tracks time-to-first-token, not total time, so streaming each token as it's generated makes the UX feel fast even when the full reply takes seconds. You stream over Server-Sent Events with the client reading an event stream and the model called with `stream=True`. If buffering is on anywhere in the path (a proxy, or the response not flushed per chunk), the client gets nothing until the end - the stream silently collapses back into a slow single response.

Practice it live: [Restore Token Streaming on a Chat Endpoint](https://heydevjob.com/ai) (AI-109)

## Embeddings and vector search (mid)

**What is an embedding, and why does cosine similarity matter for it?**

An embedding is a dense vector that encodes the semantic meaning of text, so that similar meanings land near each other in vector space regardless of exact wording. Cosine similarity measures the angle between two vectors, which captures semantic closeness while ignoring magnitude - the standard metric for "how related are these two texts." This is what lets retrieval find "how do I get my money back" when the doc says "refund policy."

**What does a vector database do that a regular database doesn't?**

A vector database stores embeddings and does fast approximate nearest-neighbor search to return the top-k most similar vectors to a query vector, typically by cosine or dot-product similarity. A relational database matches on exact values or keywords; a vector DB matches on meaning. It's the storage and retrieval engine under every RAG feature.

Practice it live: [Power RAG Search With a Vector Database](https://heydevjob.com/ai) (AI-204)

## RAG: retrieval (mid)

**What are the four stages of a RAG pipeline?**

Chunk, embed, retrieve, generate: split documents into coherent chunks, embed each chunk into the vector store at ingest, embed the query and retrieve the top-k similar chunks at lookup, then augment the prompt with those chunks and generate the answer. Knowing this pipeline cold is table stakes for an AI interview. The key insight is that retrieval quality - not the model - usually decides whether the feature works.

Practice it live: [Build an End-to-End RAG Pipeline](https://heydevjob.com/ai) (AI-301)

**Why is chunking the most under-appreciated RAG knob, and what makes a good chunk?**

If you split documents at arbitrary character counts you slice sentences and ideas in half, so the embedding of a chunk no longer represents a coherent thought and retrieval silently degrades no matter how good the model is. Good chunks respect natural boundaries (sentences, paragraphs), stay within a sensible size, and often overlap slightly so context isn't lost at the seams. Bad chunking is a leading cause of "the answer was in the docs but RAG didn't find it."

Practice it live: [Fix the Chunking That's Wrecking RAG Retrieval](https://heydevjob.com/ai) (AI-205)

**How does RAG reduce hallucination compared to asking the model directly?**

RAG grounds the answer in retrieved source text injected into the prompt, so the model summarizes real, current, domain-specific content instead of recalling (and sometimes inventing) facts from its training data. You can also cite the retrieved chunks, giving users a way to verify. It doesn't eliminate hallucination - the model can still misread or over-extend the context - but it sharply narrows the surface for it.

## Agents and tools (mid to senior)

**What is function (tool) calling and why use it instead of letting the model answer directly?**

Tool calling means the LLM chooses a tool and emits structured arguments for it, then your code runs the tool deterministically and feeds the result back. You use it because models are unreliable at math, have no live data, and can't take real actions - so you let them decide what to do and you actually do it. The model picks the calculator; your code computes the number.

Practice it live: [Make an LLM Pick the Right Tool](https://heydevjob.com/ai) (AI-211)

**Describe the agent loop. What turns a model into an "agent"?**

An agent runs a decide-act-observe loop: the model emits an action (a tool call), your runtime executes it, you feed the observation back into the context, and the model decides the next step - repeating until it produces a final answer or hits a limit. That loop is what an agent is. The hard parts in production are stopping conditions, error handling on failed tool calls, and bounding the number of iterations so it can't spin forever.

Practice it live: [Build a Tool-Calling Agent Loop](https://heydevjob.com/ai) (AI-309)

**How do you keep an agent from looping forever or going off the rails?**

Set a hard cap on iterations, define explicit stop conditions (a final-answer tool or a done signal), and handle tool errors by feeding the failure back as an observation rather than crashing. Validate tool arguments before executing, and constrain which tools are available for the task. Observability - logging each decide-act-observe step - is essential because agent failures are otherwise invisible.

## Cost and performance (mid)

**What's the cheapest meaningful win on an LLM bill, and how does it work?**

A cache-aside layer keyed on the prompt: before calling the model, check a cache (e.g. Redis) for that exact prompt; on a hit, return the stored response and skip the model entirely; on a miss, call the model and store the result. Costs scale linearly with requests, so even a 30 percent hit rate cuts the bill by roughly a third in a few lines of code. You add a TTL so cached answers don't go stale.

Practice it live: [Cut LLM Costs With Redis Caching](https://heydevjob.com/ai) (AI-203)

**Beyond caching, what levers cut LLM cost and latency in production?**

Route simple requests to a smaller, cheaper model and reserve the large model for hard cases (model cascading); trim prompts and cap history to spend fewer tokens; batch where the API supports it; and stream to improve perceived latency. Prompt caching at the provider level can also discount repeated prefixes. The discipline is measuring tokens-per-request and hit rates, not guessing.

## Evals (senior)

**How do you answer "is the new prompt better than the old one" with evidence instead of vibes?**

Build an eval harness: a golden set of `(input, expected)` cases, a scoring function, and a runner that returns a pass rate. You run the candidate prompt against the set, score each case, aggregate into a pass rate, and compare versions - the same shape as RAGAS or OpenAI-evals. Making the LLM call injectable lets one harness evaluate any prompt, model, or pipeline version, so prompt changes get gated like code changes do.

Practice it live: [Build an LLM Evaluation Harness](https://heydevjob.com/ai) (AI-302)

**What scoring methods do you use to evaluate LLM output, and what are their trade-offs?**

Lexical methods (keyword presence, exact match, ROUGE/BLEU) are cheap, deterministic, and fast but miss paraphrase and semantics. Embedding similarity catches meaning but blurs precision. LLM-as-judge - a strong model scoring the output against a rubric - handles open-ended quality but adds cost, latency, and its own bias, so you validate the judge against human labels. Most real harnesses combine a cheap lexical gate with a judge for the nuanced cases.

**Why do evals matter more for LLM features than traditional unit tests?**

LLM output is non-deterministic and the same prompt change can fix one case while silently regressing five others, so you can't reason about correctness by reading the diff. Evals turn "did this prompt change help" into a number you can track over time, catching regressions a code review never would. Without them, every prompt tweak is a blind deploy.

## ML fundamentals (cross-level)

**Explain overfitting and how you detect and prevent it.**

Overfitting is when a model learns noise and quirks of the training set instead of the general pattern, so it scores high on training data but poorly on unseen data. You detect it by a large gap between training and validation metrics. You prevent it with more or cleaner data, regularization, simpler models, early stopping, and proper train/validation/test splits so you're never evaluating on data the model trained on.

**What's the difference between precision and recall, and when do you optimize for each?**

Precision is the fraction of predicted positives that are correct; recall is the fraction of actual positives you caught. You favor precision when false positives are costly (e.g. flagging a transaction as fraud and blocking a real customer) and recall when missing a positive is costly (e.g. a disease screen). F1 balances the two; the right trade-off is always set by the cost of each error type, not by maximizing a single number.

**Why split data into train, validation, and test sets?**

Train fits the model, validation tunes hyperparameters and guides model selection, and test gives a final unbiased estimate of real-world performance on data touched at no earlier stage. If you tune against the test set it leaks into your choices and your reported accuracy becomes optimistic. Keeping the test set truly held out is the only way the final number means anything.

---

## Build it for real

Reading answers gets you through the phone screen. Shipping the work gets you the offer. Every "Practice it live" link above is a real broken LLM system on HeyDevJob - you fix it in a live cloud workspace from your browser, and every fix lands on a portfolio hiring managers can open and click. The junior tier is free, no card, no setup.

**Start your portfolio →** [heydevjob.com/ai](https://heydevjob.com/ai)
