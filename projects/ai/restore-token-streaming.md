# Restore Token Streaming on a Chat Endpoint

**Role:** AI/ML Engineer · **Level:** Junior · **Type:** Debug · **Skills:** LLM, streaming, SSE, Flask

> The chat should type out word by word. Instead it pauses, then dumps the whole reply at once. Two bugs: the LLM call isn't asking for a stream, and the endpoint collects every token before sending one. Streaming is what makes LLM UX feel fast.

## The scenario

The `/chat/stream` endpoint should proxy the LLM as Server-Sent Events so the UI renders tokens live, but the whole response appears at once after a pause. Two bugs: the call collects all chunks into one string before yielding, and it yields once at the end. You make it stream each token as it arrives.

## The code

The generator that buffers everything (`app.py`):

```python
def generate():
    response = requests.post(f"{LLM_API_URL}/v1/chat/completions",
        json={"model": LLM_MODEL, "messages": [...], "stream": True},
        timeout=60, stream=True)
    # BUG: collecting everything first, then yielding once
    full_response = ""
    for line in response.iter_lines():
        if line and line.decode().startswith("data: "):
            # ... parse delta, accumulate ...
            full_response += content
    # yields everything at once instead of streaming
    yield f"data: {json.dumps({'content': full_response})}\n\n"
    yield "data: [DONE]\n\n"
```

The symptom:

```text
ask a question -> long pause -> entire reply appears at once (no streaming)
```

## How you'll approach it

1. **Find the buffering link.** The loop accumulates into `full_response` and yields once - nothing reaches the client until the LLM is fully done.
2. **Yield per token.** Move the `yield` inside the loop: emit each token's content as an SSE event the instant it arrives.
3. **Keep the SSE format.** Send each chunk as `data: {...}\n\n` and finish with `[DONE]`.
4. **Verify.** The reply now types out live instead of arriving all at once.

## What you'll learn

- Why perceived latency tracks first-token time, not total time
- Streaming end to end: a stream-aware LLM call *and* a generator HTTP response
- The SSE wire format (`data:` lines, `[DONE]`)
- Spotting the link that buffers and kills the effect

## What it proves

You can debug a streaming LLM pipeline and find the buffering link - the difference between a chat that feels instant and one that freezes.

> Resume-ready: *Restored token streaming on a chat endpoint by yielding SSE events per token instead of buffering the full LLM response.*

## On the roadmap

Part of the [AI/ML Engineer Roadmap](../../roadmaps/ai.md) - **Stage 5: Stateful & Streaming Chat** → Token streaming.

---

**Build it for real.** This is ticket **AI-109** on HeyDevJob - the real buffering endpoint above, in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Restore Token Streaming on a Chat Endpoint on HeyDevJob →](https://heydevjob.com/ai)
