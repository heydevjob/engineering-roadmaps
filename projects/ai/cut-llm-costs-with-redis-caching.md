# Cut LLM Costs by Caching With Redis

**Role:** AI/ML Engineer · **Level:** Mid · **Type:** Optimize · **Skills:** caching, Redis, cost optimization, LLM

> The OpenAI bill is climbing because the same hot prompts get sent dozens of times an hour. Every identical question pays full price. A cache-aside layer takes the per-prompt cost from N down to 1 - in about five lines.

## The scenario

An LLM-backed `/ask` endpoint calls the model on every request, even when the prompt is identical, driving up the bill. You wrap the call in a Redis cache-aside layer: check the cache first, on a miss call the LLM and store the response with a 24h TTL, so repeat questions skip the model entirely.

## The code

The uncached call (`app.py`):

```python
def call_llm(prompt: str) -> str:
    global _llm_hits
    _llm_hits += 1
    r = requests.post(LLM_URL, json={"model": MODEL,
        "messages": [{"role": "user", "content": prompt}]}, timeout=60)
    r.raise_for_status()
    return r.json()["choices"][0]["message"]["content"]

@app.route("/ask")
def ask():
    q = request.args.get("q", "")
    # TODO: cache-aside on Redis with key f"llm:{q}".
    answer = call_llm(q)
    return jsonify({"q": q, "a": answer})
```

The symptom:

```text
same prompt asked 50x/hour -> 50 LLM calls, 50x the cost
```

## How you'll approach it

1. **Check first.** Before calling the LLM, look up `llm:{q}` in Redis - on a hit, return the cached answer and skip the model.
2. **Populate on miss.** On a miss, call the LLM, then store the response under that key.
3. **Set a TTL.** A 24h expiry keeps answers fresh-ish and bounds memory.
4. **Verify.** The LLM hit-counter stops climbing for repeated prompts; identical questions are served from cache.

## What you'll learn

- The cache-aside pattern applied to LLM calls
- Keying a cache on the prompt and choosing a TTL
- Why even a modest hit rate meaningfully cuts the bill
- That LLM cost scales linearly with request volume

## What it proves

You can cut LLM cost with a simple, correct caching layer - the cheapest, highest-leverage optimization in any LLM product.

> Resume-ready: *Added a Redis cache-aside layer to LLM calls keyed on the prompt, eliminating duplicate inference and cutting API cost on repeat questions.*

## On the roadmap

Part of the [AI/ML Engineer Roadmap](../../roadmaps/ai.md) - **Stage 8: Cost & Performance** → Response caching.

---

**Build it for real.** This is ticket **AI-203** on HeyDevJob - the real uncached endpoint above, in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Cut LLM Costs With Redis Caching on HeyDevJob →](https://heydevjob.com/ai)
