# Build a Tool-Calling Agent Loop

**Role:** AI/ML Engineer · **Level:** Senior · **Type:** Design · **Skills:** agents, tool-calling, ReAct pattern, LLM

> An agent should think in a loop: pick a tool, run it, look at the result, decide what's next - until it can answer. Right now it calls the model once and hands back its first reply, so `ACTION: calculator | 12*8` becomes the "answer." You're building the loop that *is* an agent.

## The scenario

An agent should reason in a decide → act → observe loop. The LLM and a calculator tool are wired up, but `run()` calls the model once and returns the first reply - so when the model emits `ACTION: calculator | 12*8`, that raw string becomes the answer and the tool never runs. You implement the loop.

## The code

The single-shot `run()` (`agent.py`):

```python
def run(question, max_steps=5):
    messages = [
        {"role": "system", "content": SYSTEM},
        {"role": "user", "content": question},
    ]
    # BUG: calls the model once and returns its first reply. When the model
    # asks for a tool (ACTION: ...), that raw string becomes the "answer" and
    # the tool is never run. Implement the decide -> act -> observe loop.
    reply = call_llm(messages)
    return {"answer": reply, "steps": []}
```

The symptom:

```text
"what is 12*8?" -> {"answer": "ACTION: calculator | 12*8"}   # tool never ran
```

## How you'll approach it

1. **Loop, don't single-shot.** Call the model, then inspect its reply each iteration instead of returning immediately.
2. **Act on an ACTION.** If the reply is `ACTION: <tool> | <input>`, run that tool and capture the result.
3. **Observe.** Feed the tool result back into the conversation as an Observation and let the model decide the next step.
4. **Stop on FINAL.** Repeat until the model returns a final answer (or you hit `max_steps`). Verify a math question returns the computed result with the steps recorded.

## What you'll learn

- The ReAct decide → act → observe loop that defines an agent
- Detecting an action, running the tool, and feeding the observation back
- Bounding the loop with `max_steps` to avoid runaways
- Why this loop - not the model alone - is what "agent" means

## What it proves

You can build a tool-calling agent loop from scratch - the defining pattern of agentic AI, and the thing most candidates can talk about but can't implement.

> Resume-ready: *Built a tool-calling agent loop (ReAct) - decide, execute the tool, feed the observation back, repeat to a final answer - bounded by a step limit.*

## On the roadmap

Part of the [AI/ML Engineer Roadmap](../../roadmaps/ai.md) - **Stage 9: Senior - RAG Systems, Eval & Agents** → Agent loops.

---

**Build it for real.** This is ticket **AI-309** on HeyDevJob - the real single-shot agent above, in a cloud workspace you build from your browser. Each fix lands on a portfolio you can show. [Build a Tool-Calling Agent Loop on HeyDevJob →](https://heydevjob.com/ai)
