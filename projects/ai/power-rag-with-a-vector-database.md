# Power RAG Search With a Vector Database

**Role:** AI/ML Engineer · **Level:** Mid · **Type:** Build · **Skills:** RAG, vectors, embeddings, ChromaDB

> This is the canonical RAG move: embed your docs once, embed the query at lookup, and pull the top-k by similarity. Two functions stand between you and semantic search - and they're both empty.

## The scenario

You build the RAG retrieval loop with ChromaDB. Implement `index_docs()` (embed each doc and add it to the collection) and `search(query, k)` (embed the query and return the top-k semantically similar docs). This is the pattern under every RAG feature: embed at ingest, embed at lookup, find top-k by cosine similarity.

## The code

The empty retrieval functions (`rag.py`):

```python
def index_docs() -> None:
    # TODO: for each doc in DOCS, get its embedding via _embed(),
    # then add to _collection with id=str(i), embeddings=[vec], documents=[doc].
    pass

def search(query: str, k: int = 3) -> list[str]:
    # TODO: embed the query, query _collection with n_results=k,
    # return the documents list.
    return []
```

The symptom:

```text
search("how do refunds work?") -> []   # nothing is indexed or retrieved
```

## How you'll approach it

1. **Index at ingest.** For each doc, compute its embedding with `_embed()` and add it to the ChromaDB collection with an id and the document text.
2. **Embed the query.** At search time, embed the incoming query the same way.
3. **Retrieve top-k.** Query the collection for the `k` nearest vectors (cosine similarity) and return their documents.
4. **Verify.** A query returns the semantically closest docs, not keyword matches.

## What you'll learn

- The RAG retrieval loop: embed docs at ingest, embed query at lookup, top-k by similarity
- Using a vector database (ChromaDB) to store and query embeddings
- Why semantic search beats keyword matching for grounding answers
- That retrieval quality - not the LLM - makes or breaks RAG

## What it proves

You can build the retrieval half of a RAG system - the part the whole feature depends on - with a real vector database.

> Resume-ready: *Built a RAG retrieval loop with ChromaDB - embedding documents at ingest and queries at lookup to return top-k semantically similar results.*

## On the roadmap

Part of the [AI/ML Engineer Roadmap](../../roadmaps/ai.md) - **Stage 6: RAG - Retrieval** → Vector search.

---

**Build it for real.** This is ticket **AI-204** on HeyDevJob - the real empty retrieval loop above, in a cloud workspace you build from your browser. Free on the junior tier, no card, no setup. [Power RAG Search With a Vector Database on HeyDevJob →](https://heydevjob.com/ai)
