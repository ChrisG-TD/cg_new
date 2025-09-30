---
layout:
title: RAG for Beginners - Building Your First Retrieval-Augmented Workflow  
date: 2025-09-03
author: 
categories: [ai fundamentals]
description: A beginner's guide to Retrieval-Augmented Generation (RAG) using a local-first approach for animation and filmmaking projects.
tags: [rag_basics, faiss, ai_fundamentals, local_first, animation_pipeline, ai_filmmaking]
---

<div style="text-align:center;">
  <video
    controls
    preload="metadata"
    style="max-width:100%;height:auto;"
    poster="{{ '/assets/img/RAG_bunny_run_poster.png' | relative_url }}"
  >
    <source src="{{ '/assets/video/RAG_bunny_run.mp4' | relative_url }}" type="video/mp4">
    Your browser does not support the video tag.
    <a href="{{ '/assets/video/RAG_bunny_run.mp4' | relative_url }}">Download the video</a>.
  </video>

  <p style="color:#888; font-size:0.9em; margin-top:0.5em;">
    Created with my ideas into Midjourney
  </p>
</div>

## RAG for Beginners: Building Your First Retrieval-Augmented Workflow

**I do not want or think AI should be used to take artists' jobs. But if we don't understand these tools, others will use them without us. This knowledge belongs to artists. I'm sharing it so we stay in control of our own creative futures, because an informed artist is an empowered artist.**

In my June post, *[The Role That Doesn't Exist](https://chrisgallagher.com/posts/the-role-that-doesnt-exist/)*, I outlined the need for **AI TDs** - people who can bridge creative prompting with production systems.

This post is where I started from, building those systems. **If you've never heard of RAG, this is your primer.** If you have, consider this my "basic setup" that I've been using and tweaking. The advanced stuff comes in future posts.

---

## What is RAG?

**Retrieval-Augmented Generation (RAG)** is a fancy name for giving AI models a memory system.

Here's the problem: When you ask ChatGPT or Claude about your film project, they don't know anything about *your specific project*. They know general filmmaking, but not your characters, your story, or your production notes.

RAG fixes this by:
1. **Storing** your project data in a searchable format
2. **Finding** relevant pieces when you ask questions  
3. **Feeding** those pieces to the AI along with your question

Think of it as giving the AI access to your project notebook before it answers.

---

## Why I Went Local-First

Most RAG tutorials point you toward cloud services. I went the opposite direction and built everything locally. Here's why:

**Cost Control**: No monthly bills that scale with usage  
**Data Privacy**: My scripts and concepts never leave my machine/local network  
**Learning**: Understanding how the pieces work together  
**Flexibility**: I can modify anything without API limitations

My setup runs on a Ryzen 9 with an RTX 4070 Super. Nothing exotic, just solid mid-range hardware.

---

## The Basic RAG Workflow

Here's how RAG works, broken down into digestible steps:

### 1. Ingestion
Collect your project data:
- Script drafts and notes (I use Obsidian)
- Character descriptions and world-building docs
- Reference images and storyboards
- Production logs and meeting notes

### 2. Chunking & Embedding
Break everything into small pieces and convert to numbers:
```python
# Your text gets split into chunks
chunks = ["Scene 1: Lily enters the forest...", 
          "Character note: Lily is cautious but curious...",
          "Visual ref: Dappled sunlight through trees..."]

# Then converted to vectors (simplified)
embeddings = embedding_model.encode(chunks)
```

### 3. Indexing
Store those number representations in a database:
```python
import faiss

# Create the search index
index = faiss.IndexFlatL2(384)  # 384 = embedding dimensions
index.add(embeddings)

# Also store the original text + metadata
metadata = [
    {"text": chunk, "type": "scene", "project": "forest_film"} 
    for chunk in chunks
]
```

### 4. Retrieval
When you ask a question, find the most relevant chunks:
```python
# Your question becomes a vector too
query = "How does Lily feel about entering the forest?"
query_vector = embedding_model.encode([query])

# Search for similar content
distances, ids = index.search(query_vector, k=3)

# Get the relevant chunks
relevant_chunks = [metadata[i]["text"] for i in ids[0]]
```

### 5. Generation
Combine your question with retrieved context:
```python
context = "\n".join(relevant_chunks)
full_prompt = f"""
Context from the project:
{context}

Question: {query}
"""
# Send to your LLM of choice
```

---

## Why This Matters for Creative Work

Without RAG, asking AI about your project is like asking a stranger. With RAG, it's like asking someone who's read all your notes.

**For Animation:**
- "Show me all the reference poses for this character"
- "What's the lighting setup we used in similar forest scenes?"
- "Find dialogue that shows Lily's personality development"

**For Filmmaking:**
- "What camera angles worked best for tension in Act 2?"
- "Retrieve all the notes about the villain's motivation"
- "Find scenes where we established this visual motif"

The AI can now give you project-specific answers instead of generic filmmaking advice.

---

## My Basic Setup

Here's what I'm actually running:

**Vector Database**: FAISS (Facebook AI similarity search library)  
**Embeddings**: Sentence-transformers models (run locally)  
**Storage**: JSON files for metadata, numpy arrays for vectors  
**Interface**: Python scripts (nothing fancy yet)

It's intentionally simple. No microservices, no complex deployments. Just the core components working together.

```python
from typing import List, Dict, Literal, Optional
import numpy as np

Metric = Literal["ip", "cosine", "l2"]

def retrieve_context(
    query: str,
    k: int = 5,
    *,
    model=None,
    index=None,
    metadata: Dict[int, Dict] | List[Dict] = None,
    metric: Metric = "cosine",
    normalize_query: bool = True,
    score_normalization: Optional[Literal["none", "minmax"]] = "none",
) -> List[Dict]:
    """
    Retrieve top-k contexts from a FAISS index.

    Assumes:
      - metric="cosine": index is IndexFlatIP and both db vectors and query are L2-normalized
      - metric="ip":     index is IndexFlatIP with raw inner products
      - metric="l2":     index is IndexFlatL2

    metadata:
      - If you used add_with_ids, pass a dict keyed by FAISS ids.
      - If you used add (implicit ids 0..n-1), a list is OK and we will treat ids as positions.
    """
    if index is None or model is None:
        raise ValueError("model and index are required")
    if k <= 0:
        return []
    if getattr(index, "ntotal", 0) == 0:
        return []

    # Encode
    q = model.encode([query])
    q = np.asarray(q, dtype=np.float32)
    if q.ndim == 1:
        q = q[None, :]
    if metric in ("cosine",) and normalize_query:
        # Safe to normalize even if already normalized
        norms = np.linalg.norm(q, axis=1, keepdims=True) + 1e-12
        q = q / norms

    # Search
    k_eff = int(min(k, index.ntotal))
    distances, ids = index.search(q, k_eff)  # shapes: (1, k_eff)

    # Convert score depending on metric
    d = distances[0].tolist()
    I = ids[0].tolist()

    # Handle missing rows (faiss pads with -1 if < k matches)
    pairs = [(id_, dist) for id_, dist in zip(I, d) if id_ != -1]

    # Optionally normalize scores to 0..1 for readability
    def to_score(dist: float) -> float:
        if metric == "l2":
            # smaller is better; invert
            return -float(dist)
        # ip or cosine: larger is better
        return float(dist)

    scored = [(id_, to_score(dist)) for id_, dist in pairs]

    if score_normalization == "minmax" and scored:
        vals = [s for _, s in scored]
        vmin, vmax = min(vals), max(vals)
        if abs(vmax - vmin) > 1e-12:
            scored = [(i, (s - vmin) / (vmax - vmin)) for i, s in scored]
        else:
            scored = [(i, 1.0) for i, _ in scored]

    results = []
    for id_, score in scored:
        # metadata lookup: dict by id or list by position
        if isinstance(metadata, dict):
            meta = metadata.get(int(id_), {})
        elif isinstance(metadata, list):
            if 0 <= id_ < len(metadata):
                meta = metadata[id_]
            else:
                meta = {}
        else:
            meta = {}

        results.append({
            "id": int(id_),
            "text": meta.get("text", ""),
            "relevance": float(score),
            "source": meta.get("source", None),
        })

    return results
```

---

## What's Coming Next

This is just the foundation. In future posts, I'll cover:

- **Advanced chunking strategies** for different content types
- **Hybrid search** combining keyword and semantic search  
- **Query expansion** and reranking techniques
- **Evaluation methods** to measure retrieval quality
- **Multi-modal RAG** for images, audio, and video

For now, this basic setup has transformed how I work with AI on creative projects. The model finally knows what I'm talking about.

---

## The Bottom Line

RAG isn't magic, it's just organized memory. But for creative work where context matters, that memory makes all the difference.

If you're working on any long-term creative project with AI, start here. Get the basics working, then we'll make them better.

*Next post: Advanced chunking strategies for creative content*


---