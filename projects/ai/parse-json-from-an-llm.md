# Parse JSON From an LLM (Strip Markdown Fences)

**Role:** AI/ML Engineer · **Level:** Junior · **Type:** Debug · **Skills:** LLM output parsing, JSON, robustness

> The extractor crashes with `JSONDecodeError` on random requests. The model returns valid JSON - then wraps it in a ```` ```json ```` block, even though you didn't ask. `json.loads()` chokes on the backticks. This is the #1 LLM parsing footgun.

## The scenario

An entity-extraction API intermittently crashes with `JSONDecodeError`. The LLM returns valid JSON but often wraps it in a markdown code block (```` ```json ... ``` ````), and the parser calls `json.loads()` on the raw string. You make the parsing robust to the fences the model adds unbidden.

## The code

The fragile parse (`extractor.py`):

```python
raw_content = data["choices"][0]["message"]["content"].strip()

# BUG: the LLM often wraps JSON in markdown like:
# ```json
# {"people": ["Alice"], "places": ["Paris"]}
# ```
# This parses the raw response directly, which fails on the backtick fences.
parsed = json.loads(raw_content)
```

The symptom:

```text
json.decoder.JSONDecodeError: Expecting value: line 1 column 1
# happens whenever the model wraps the JSON in ```json ... ```
```

## How you'll approach it

1. **See the real input.** Print the raw content - it's valid JSON, but fenced in ```` ```json ... ``` ````, so `json.loads` fails on the first backtick.
2. **Strip the fences.** Detect and remove the surrounding code-block markers before parsing.
3. **Be defensive.** Handle both fenced and unfenced responses (and ideally trailing prose) so it's robust to model variability.
4. **Verify.** Both fenced and raw JSON responses now parse cleanly.

## What you'll learn

- Why LLMs wrap structured output in markdown even when told not to
- Stripping code fences before parsing
- Writing parsing that's robust to model output variability
- The general principle: never trust LLM output to be exactly the requested shape

## What it proves

You can make LLM output parsing production-robust - handling the markdown-fence footgun that breaks naive `json.loads` in real features.

> Resume-ready: *Hardened an LLM JSON extractor against markdown-fenced responses, eliminating intermittent JSONDecodeError crashes in production.*

## On the roadmap

Part of the [AI/ML Engineer Roadmap](../../roadmaps/ai.md) - **Stage 2: Structured Output** → Parsing structured output.

---

**Build it for real.** This is ticket **AI-104** on HeyDevJob - the real crashing extractor above, in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup. [Parse JSON From an LLM on HeyDevJob →](https://heydevjob.com/ai)
