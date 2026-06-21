[Home](../../README.md) › [AI/ML Projects](README.md) › **Build an LLM Evaluation Harness**

# Build an LLM Evaluation Harness (Golden Set)

`AI/ML` · `🔴 Senior` · `📐 Design` · `Ticket AI-302`

**Skills** — LLM evaluation · testing · regression · eval harness

> "Is the new prompt better than the old one?" Without evals, that's a vibe. You're building the harness that turns it into a number - a golden set, a score, a pass rate - so prompt changes get tested like code.

> [!NOTE]
> **The scenario**
> You build an evaluation harness: a golden set of `(question, expected_keywords)` cases, a scoring function, and a runner. Implement `score(response, expected_keywords)` (fraction of expected keywords present) and `evaluate(test_cases, llm_fn, threshold)` (run each case through an injectable LLM function, score it, aggregate into a pass rate and per-case results). Same shape RAGAS / OpenAI-evals use.

## 🧩 The code

The stubbed harness (`eval.py`):

```python
def score(response: str, expected_keywords: list[str]) -> float:
    """TODO: fraction of expected_keywords present in response (case-insensitive
    substring). [0.0, 1.0]. Empty keywords -> 0.0."""
    raise NotImplementedError

def evaluate(test_cases, llm_fn, threshold: float = 0.5) -> dict:
    """TODO: run each case (response = llm_fn(question)), score, passed = s >= threshold.
    Return {pass_rate, passed, total, results:[{question, response, score, passed}]}."""
    raise NotImplementedError
```

The symptom:

```text
evaluate(cases, my_llm) -> NotImplementedError   # no quality signal exists
```

## 🛠️ How you'll approach it

1. **Score one response.** `score()` returns the fraction of expected keywords present (case-insensitive substring), handling the empty-keywords edge case.
2. **Run the set.** `evaluate()` calls the injectable `llm_fn` per case, scores each, and marks pass/fail against the threshold.
3. **Aggregate.** Return a pass rate, counts, and per-case results so a human can see *which* cases regressed.
4. **Make it injectable.** Because `llm_fn` is a parameter, the same harness evals any prompt/model/pipeline version. Verify against a known golden set.

## 🎓 What you'll learn

- The eval-harness shape: golden set + scoring + runner (the RAGAS/OpenAI-evals pattern)
- Lexical scoring and aggregating into a pass rate
- Why injectable `llm_fn` lets you compare prompt/model versions
- Treating eval pass rate as the gate for prompt changes, like tests gate code

## 🏆 What it proves

You can build the quality gate that lets a team change prompts with confidence - the difference between shipping AI on vibes and shipping it on evidence.

> [!TIP]
> **Resume-ready** — *Built an injectable LLM evaluation harness - golden set, keyword scoring, and pass-rate aggregation - to gate prompt and model changes against regressions.*

## 🗺️ On the roadmap

Part of the [AI/ML Engineer Roadmap](../../roadmaps/ai.md) - **Stage 9: Senior - RAG Systems, Eval & Agents** → Evaluation harnesses.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **AI-302** on HeyDevJob - the real eval stub above, in a cloud workspace you build from your browser. Each fix lands on a portfolio you can show.
> [Build an LLM Evaluation Harness on HeyDevJob →](https://heydevjob.com/ai)

**Explore AI/ML** · [📍 Roadmap](../../roadmaps/ai.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/ai.md) · [✅ Checklist](../../checklists/ai.md)

[◀ Prev: Build an End-to-End RAG Pipeline](build-an-end-to-end-rag-pipeline.md) · [Next: Build a Tool-Calling Agent Loop ▶](build-a-tool-calling-agent-loop.md)
