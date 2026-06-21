# Restore a Broken LLM API Integration

**Role:** AI/ML Engineer · **Level:** Junior · **Type:** Debug · **Skills:** LLM APIs, OpenAI-compatible format, integration

> The summarizer crashes with "model not found" and connection errors. The code is two small mistakes away from working: a malformed endpoint and a missing required field. This is the cryptic-404 wall every LLM integration hits on day one.

## The scenario

A summarization script fails calling an OpenAI-compatible LLM API. Two bugs: the endpoint URL is wrong, and the request payload is missing the required `model` field. You fix both so the call succeeds. The lesson is the OpenAI-compatible contract - `model` + `messages`, the `/v1/` path - which is the same across providers.

## The code

The broken request (`summarize.py`):

```python
API_URL = "http://shared-services...:9999/v1/completions"  # wrong port + endpoint

def summarize(text):
    payload = {
        # BUG: missing "model" field - required by OpenAI-compatible APIs
        "messages": [
            {"role": "system", "content": "Summarize the following text..."},
            {"role": "user", "content": text}
        ],
        "temperature": 0.3,
        "max_tokens": 150
    }
```

The symptom:

```text
"model not found" / connection error -> crash on response.raise_for_status()
```

## How you'll approach it

1. **Read the error.** "model not found" and a connection failure point at the request shape, not your prompt.
2. **Fix the endpoint.** Use the correct host/port and the chat-completions path the API actually serves.
3. **Add the required field.** OpenAI-compatible APIs require `model` alongside `messages` - add it.
4. **Verify.** The call returns a completion and the summary comes back.

## What you'll learn

- The OpenAI-compatible request contract: `model`, `messages`, the `/v1/` path
- Reading "model not found" / 404s as request-shape problems
- Why the same contract works across providers
- Validating an integration end to end

## What it proves

You can wire up an LLM API correctly - the day-one integration skill - and debug the cryptic errors that come from one missing field.

> Resume-ready: *Restored a broken LLM API integration by correcting the endpoint and adding the required model field to the OpenAI-compatible request.*

## On the roadmap

Part of the [AI/ML Engineer Roadmap](../../roadmaps/ai.md) - **Stage 1: LLM API Integration** → Calling an LLM API.

---

**Build it for real.** This is ticket **AI-101** on HeyDevJob - the real broken integration above, in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Restore a Broken LLM API Integration on HeyDevJob →](https://heydevjob.com/projects/llm-api-integration-portfolio-project)
