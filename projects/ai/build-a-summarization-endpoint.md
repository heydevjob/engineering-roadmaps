[Home](../../README.md) › [AI/ML Projects](README.md) › **Build a Text Summarization Endpoint**

# Build a Text Summarization Endpoint With an LLM

`AI/ML` · `🟢 Junior` · `🔨 Build` · `Ticket AI-106`

**Skills** — Flask · LLM · prompt engineering · API

> The `/summarize` endpoint exists and returns "not implemented" for everything. You're wiring it to an LLM - the prompt-wrap → call → validate → return loop that's the heartbeat of every LLM product.

> [!NOTE]
> **The scenario**
> A Flask app has a `/summarize` endpoint that returns a placeholder. You wire it to the LLM: accept `{"text": "..."}`, call the OpenAI-compatible chat API with a summarization prompt, and return `{"summary": "..."}` where the summary is meaningfully shorter than the input.

## 🧩 The code

The stubbed endpoint (`app.py`):

```python
@app.route("/summarize", methods=["POST"])
def summarize():
    """Accepts {"text": "..."}, returns {"summary": "..."}.
    TODO: Call the LLM API (OpenAI-compatible at LLM_API_URL + /v1/chat/completions)."""
    data = request.get_json(silent=True)
    if not data or not data.get("text"):
        return jsonify({"error": "Missing 'text' field"}), 400
    input_text = data["text"]
    # TODO: Replace this with an actual LLM API call
    summary = "not implemented"
    return jsonify({"summary": summary})
```

The symptom:

```text
POST /summarize {"text": "..."} -> {"summary": "not implemented"}  (always)
```

## 🛠️ How you'll approach it

1. **Write the prompt.** A system message that instructs a concise summary, with the user's text as input.
2. **Call the LLM.** POST to the chat-completions endpoint with `model` + `messages`, read the response content.
3. **Validate and return.** Pull the summary out of the response and return it as `{"summary": ...}` (a real summary, shorter than the input).
4. **Handle the unhappy path.** Keep the `400` for missing input, and fail gracefully if the API errors.

## 🎓 What you'll learn

- The core LLM feature loop: prompt → call → parse → return
- Writing a system prompt for a specific task
- Reading the completion out of an OpenAI-compatible response
- Validating output (shorter than input) instead of trusting it

## 🏆 What it proves

You can build an LLM-backed endpoint from scratch - the foundational AI-engineering build task that every LLM product repeats.

> [!TIP]
> **Resume-ready** — *Built a text-summarization API endpoint backed by an LLM, owning the prompt, the call, and response handling end to end.*

## 🗺️ On the roadmap

Part of the [AI/ML Engineer Roadmap](../../roadmaps/ai.md) - **Stage 3: Building LLM Endpoints** → Build an LLM-backed endpoint.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **AI-106** on HeyDevJob - the real stubbed endpoint above, in a cloud workspace you build from your browser. Free on the junior tier, no card, no setup.
> [Build a Text Summarization Endpoint on HeyDevJob →](https://heydevjob.com/ai)

**Explore AI/ML** · [📍 Roadmap](../../roadmaps/ai.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/ai.md) · [✅ Checklist](../../checklists/ai.md)

[◀ Prev: Parse JSON From an LLM](parse-json-from-an-llm.md) · [Next: Improve an LLM Classifier Prompt ▶](improve-a-classifier-prompt.md)
