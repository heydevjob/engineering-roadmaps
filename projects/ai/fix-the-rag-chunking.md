[Home](../../README.md) › [AI/ML Projects](README.md) › **Fix the Chunking That's Wrecking RAG Retrieval**

# Fix the Chunking That's Wrecking RAG Retrieval

`AI/ML` · `🟡 Mid` · `🎯 Tune` · `Ticket AI-205`

**Skills** — RAG · chunking · retrieval · embeddings

> Search returns half-sentences and word salad. The retrieval and ranking are fine - the chunker slices documents into 25-character scraps that cut words in half. Garbage chunks silently tank the best RAG system there is.

> [!NOTE]
> **The scenario**
> A RAG search returns incoherent fragments. The retrieval and ranking code is correct; the `chunk()` function splits documents into tiny fixed-size slices that cut sentences apart, so every chunk is a meaningless scrap. You fix the chunker to split on sentence or paragraph boundaries so chunks stay coherent.

## 🧩 The code

The character-slicing chunker (`rag.py`):

```python
def chunk(text):
    # BUG: fixed 25-character slices - cuts words and sentences apart, so
    # every chunk is a meaningless scrap. Fix this to split on sentence
    # (or paragraph) boundaries instead.
    return [text[i:i + 25] for i in range(0, len(text), 25)]
```

The symptom:

```text
query: "What is the refund policy?"
retrieved: ["he refund pol", "icy allows ret", "urns within 30"]  # word salad
```

## 🛠️ How you'll approach it

1. **Locate the real culprit.** The embeddings and similarity search are fine - the inputs they're given are 25-char scraps, so retrieval can't return anything coherent.
2. **Chunk on meaning.** Split on sentence or paragraph boundaries so each chunk is a complete, self-contained passage.
3. **Mind the size.** Too small loses context, too large adds noise - aim for coherent passages, optionally with a little overlap.
4. **Verify.** Retrieved chunks are now whole sentences/passages and search results make sense.

## 🎓 What you'll learn

- Why chunking is the most under-appreciated knob in RAG
- Splitting on sentence/paragraph boundaries instead of fixed sizes
- The chunk-size trade-off: context vs noise
- That bad chunks tank retrieval no matter how good the model is

## 🏆 What it proves

You understand that RAG quality is dominated by retrieval, and you can fix the chunking that quietly destroys it - a real-world skill most tutorials skip.

> [!TIP]
> **Resume-ready** — *Fixed RAG retrieval quality by replacing fixed-size character chunking with sentence-aware chunking, restoring coherent retrieved passages.*

## 🗺️ On the roadmap

Part of the [AI/ML Engineer Roadmap](../../roadmaps/ai.md) - **Stage 6: RAG - Retrieval** → Chunking.

---

> [!IMPORTANT]
> **Build it for real**
> This is ticket **AI-205** on HeyDevJob - the real broken chunker above, in a cloud workspace you fix from your browser. Free on the junior tier, no card, no setup.
> [Fix the Chunking That's Wrecking RAG Retrieval on HeyDevJob →](https://heydevjob.com/ai)

**Explore AI/ML** · [📍 Roadmap](../../roadmaps/ai.md) · [🛠️ Projects](README.md) · [💬 Interview](../../interview/ai.md) · [✅ Checklist](../../checklists/ai.md)

[◀ Prev: Restore Token Streaming on a Chat Endpoint](restore-token-streaming.md) · [Next: Power RAG Search With a Vector Database ▶](power-rag-with-a-vector-database.md)
