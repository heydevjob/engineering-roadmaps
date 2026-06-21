# Stream the AI Chat Response

**Role:** Fullstack Engineer · **Level:** Mid · **Type:** Debug · **Skills:** streaming, SSE, Flask, React, LLM

> The chat should type the reply out word by word. Instead it freezes for a few seconds, then dumps the whole thing at once. The React side reads the stream fine - the backend is collecting every token before sending a single one.

## The scenario

A chat UI should stream the AI's reply token by token, but it freezes then dumps the full answer. The React side reads a streamed response correctly; the backend `POST /api/chat/stream` collects the *entire* LLM response before yielding anything. You fix it to forward each token the moment it arrives.

## The code

The backend buffering the whole reply (`app.py`):

```python
@stream_with_context
def gen():
    # BUG: collects full response THEN yields once
    full = ""
    with requests.post(LLM_URL, json=body, stream=True, timeout=60) as r:
        for line in r.iter_lines():
            if not line:
                continue
            # ... parse line ...
            full += json.loads(payload)["choices"][0]["delta"].get("content", "")
    yield f"data: {json.dumps({'content': full})}\n\n"
    yield "data: [DONE]\n\n"
```

The symptom:

```text
chat freezes ~3s, then the entire reply appears at once
# instead of typing out word by word
```

## How you'll approach it

1. **Find the buffering link.** The generator accumulates into `full` and yields once at the end - so nothing reaches the browser until the LLM is completely done.
2. **Yield per token.** Move the `yield` inside the loop: emit each token's content as an SSE event the moment it arrives from the LLM.
3. **Keep the protocol.** Send each chunk as `data: {...}\n\n` and finish with `[DONE]`, so the React reader renders incrementally.
4. **Verify.** The reply now types out live instead of appearing all at once.

## What you'll learn

- Why streaming is end-to-end: LLM → server → browser, all incremental
- How one buffering link kills the whole effect
- Yielding SSE events as tokens arrive with `stream_with_context`
- The SSE wire format (`data:` lines, `[DONE]`)

## What it proves

You can debug a streaming pipeline across the stack and find the link that buffers - the difference between a chat that feels alive and one that freezes.

> Resume-ready: *Fixed a streaming chat backend that buffered the full LLM response, forwarding tokens incrementally over SSE so the UI renders the reply live.*

## On the roadmap

Part of the [Fullstack Engineer Roadmap](../../roadmaps/fullstack.md) - **Stage 8: Real-Time & Interactivity** → Streaming responses.

---

**Build it for real.** This is ticket **FS-208** on HeyDevJob - the real buffering chat backend above, waiting in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Stream the AI Chat Response on HeyDevJob →](https://heydevjob.com/fullstack)
