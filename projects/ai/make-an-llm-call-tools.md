[Home](../../README.md) › [AI/ML Projects](README.md) › **Make an LLM Pick the Right Tool (Function Calling)**

# Make an LLM Pick the Right Tool (Function Calling)

`AI/ML` · `🟡 Mid` · `🔨 Build` · `Ticket AI-211`

**Skills** — function calling · tools · agents · LLM

> Ask "what's 47 times 9?" and the assistant returns `{"tool": "calculator", "input": "47*9"}` - the model picked the tool but nobody ran it. You're building the dispatch that turns a tool *choice* into a tool *result*.

> [!NOTE]
> **The scenario**
> An assistant should answer by calling tools (a calculator, etc.) instead of guessing. The LLM is prompted to reply with JSON naming a tool and input, but `/api/ask` returns that raw JSON without running anything. You implement the dispatch: parse the tool choice, run the matching tool, and return the computed result.

## 🧩 The code

The endpoint that never runs the tool (`app.py`):

```python
@app.post("/api/ask")
def ask():
    message = (request.get_json(silent=True) or {}).get("message", "")
    raw = ask_llm(message)
    # TODO: parse the JSON tool choice from `raw`, run TOOLS[tool](input),
    # and return {"tool": <name>, "result": <value>}.
    return jsonify(error="not implemented", raw=raw)
```

The symptom:

```text
"what is 47*9?" -> {"tool": "calculator", "input": "47*9"}   # never executed
```

## 🛠️ How you'll approach it

1. **Parse the choice.** The LLM returns JSON like `{"tool": "calculator", "input": "47*9"}` - parse it (robustly, fences and all).
2. **Dispatch deterministically.** Look the tool up in your `TOOLS` map and call it with the input - this is the part *your code* does, not the model.
3. **Return the result.** Respond `{"tool": ..., "result": ...}` with the actual computed value.
4. **Verify.** A calculation question now returns the answer, not the unexecuted tool choice.

## 🎓 What you'll learn

- The function-calling pattern: model chooses, your code executes
- Parsing the tool choice and dispatching to the right tool
- Why you run tools deterministically (LLMs can't do math or reach live data)
- The foundation every agent is built on

## 🏆 What it proves

You can build the function-calling core that powers tools and agents - letting a model act on the world instead of hallucinating answers.

> [!TIP]
> **Resume-ready** — *Implemented LLM function-calling dispatch - parsing the model's tool choice and executing it deterministically - so the assistant returns computed results.*

## 🗺️ On the roadmap

Part of the [AI/ML Engineer Roadmap](../../roadmaps/ai.md) - **Stage 7: Agents & Tools** → Function calling.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **AI-211** on HeyDevJob - the real tool-choice-without-dispatch above, in a cloud workspace you build from your browser. Free on the junior tier, no card, no setup.
> [Make an LLM Pick the Right Tool on HeyDevJob →](https://heydevjob.com/ai)

**Explore AI/ML** · [📍 Roadmap](../../roadmaps/ai.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/ai.md) · [✅ Checklist](../../checklists/ai.md)

[◀ Prev: Power RAG Search With a Vector Database](power-rag-with-a-vector-database.md) · [Next: Cut LLM Costs With Redis Caching ▶](cut-llm-costs-with-redis-caching.md)
