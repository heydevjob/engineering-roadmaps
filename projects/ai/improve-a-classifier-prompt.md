# Improve an LLM Sentiment Classifier Prompt

**Role:** AI/ML Engineer · **Level:** Junior · **Type:** Tune · **Skills:** prompt engineering, LLM, structured output

> Clients want one word: `positive`, `negative`, or `neutral`. The classifier returns "The text has a positive sentiment because...". The API is fine - the prompt just never told the model to shut up and answer.

## The scenario

A sentiment-classification API returns freeform paragraphs instead of clean labels. Clients expect exactly one word from a fixed set, but the model returns explanatory prose. The LLM is working; the prompt doesn't constrain the output format. You rewrite the prompt to force a single-word answer.

## The code

The unconstrained prompt (`classifier.py`):

```python
# BUG: this prompt doesn't constrain the output format.
payload = {
    "model": MODEL,
    "messages": [
        {"role": "system",
         "content": "You are a sentiment analysis assistant. Analyze the sentiment of the text the user provides."},
        {"role": "user", "content": text}
    ],
    "temperature": 0.1,
    "max_tokens": 200
}
```

The symptom:

```text
input:  "I love this product!"
output: "The text has a positive sentiment because..."   # wanted: "positive"
```

## How you'll approach it

1. **Diagnose the prompt, not the model.** "Analyze the sentiment" invites an essay - the model is doing exactly what you asked.
2. **Constrain the output.** Rewrite the system prompt to demand one word from the allowed set and nothing else (and consider lowering `max_tokens`).
3. **Make it robust.** Optionally normalize the response (trim/lowercase) so a stray period or capital doesn't fail downstream validation.
4. **Verify.** The classifier returns clean single-word labels across the test inputs.

## What you'll learn

- How prompt wording controls output format
- Constraining an LLM to a fixed label set
- The trade-offs: prompt constraints (cheap, brittle) vs JSON mode vs constrained decoding
- Why structured output is a near-universal production need

## What it proves

You can engineer a prompt to produce reliable, structured output - the cheapest and most-used lever on LLM output quality.

> Resume-ready: *Reworked an LLM classifier prompt to enforce single-word labels, eliminating freeform responses that broke downstream validation.*

## On the roadmap

Part of the [AI/ML Engineer Roadmap](../../roadmaps/ai.md) - **Stage 4: Prompt Engineering** → Constraining output with prompts.

---

**Build it for real.** This is ticket **AI-102** on HeyDevJob - the real over-chatty classifier above, in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Improve an LLM Classifier Prompt on HeyDevJob →](https://heydevjob.com/ai)
