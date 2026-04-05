# Retrieval Augmented Generation (RAG)

#rag #llm #retrieval #vector-database #embeddings #large-language-models #knowledge-base #nlp

---

## What is RAG?

Retrieval Augmented Generation (RAG) is an AI architecture pattern that enhances Large Language Models (LLMs) by dynamically fetching relevant, external knowledge at inference time — **before generating a response**. Instead of relying solely on the knowledge baked into model weights during training, RAG grounds each response in fresh, curated, domain-specific information.

Introduced by Lewis et al. in the 2020 paper *"Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks"*, RAG has become the dominant approach for building production AI applications that require accuracy, freshness, and traceability.

**Core problems RAG solves:**

- **Hallucinations** — LLMs generate plausible-sounding but factually incorrect content. RAG anchors answers to retrieved evidence.
- **Knowledge cutoff** — Pre-trained models have a fixed training date. RAG retrieves current information.
- **Context window limitations** — RAG surfaces only what's relevant instead of stuffing entire corpora into the prompt.
- **Domain specificity** — RAG can be configured to retrieve from proprietary, internal, or specialized knowledge bases.
- **Auditability** — Retrieved sources can be cited, making answers traceable.

---

## Core Architecture

![RAG Architecture Overview](diagrams/rag-architecture.drawio.svg)

RAG systems consist of two main pipelines: an **offline ingestion pipeline** (builds the index once) and an **online query pipeline** (runs at every inference). These pipelines are connected by a **vector store**.

### Key Components

**1. Knowledge Base** — The authoritative data source. Can include PDFs, web pages, databases, APIs, code, CSVs, logs, Markdown files, or any structured/unstructured data.

**2. Embedding Model** — A neural network that converts text (documents and queries) into dense numerical vectors that capture semantic meaning. Both documents at index time and queries at runtime must use the *same* model to ensure comparable vector spaces.

**3. Vector Store** — A database optimized for storing and querying high-dimensional vectors using Approximate Nearest Neighbor (ANN) search. It enables sub-second similarity lookups over millions of vectors.

**4. Retriever** — The component responsible for finding the most relevant chunks for a given query vector. Modern retrievers often combine vector search with keyword (BM25) search in a hybrid approach.

**5. Reranker** — A cross-encoder model that takes the top-K retrieved chunks and re-scores them for precision. More accurate than the initial retriever but too slow to run over the entire index.

**6. Context Builder (Prompt Assembly)** — Combines the query, retrieved chunks, metadata, and system instructions into a structured prompt.

**7. LLM Generator** — The language model that produces the final response, conditioned on the assembled context.

---

## The Two Pipelines

![RAG Pipeline Flow](diagrams/rag-pipeline-flow.drawio.svg)

### Offline Ingestion Pipeline

This pipeline runs ahead of time (or incrementally as new data arrives) to build the searchable index.

**Step 1 — Raw Documents:** Collect source data. Common formats: PDF, HTML, DOCX, Markdown, JSON, CSV, database tables.

**Step 2 — Pre-processing:** Clean and normalize text. Remove boilerplate, deduplicate, handle encoding issues, extract structured metadata (author, date, source URL, section title).

**Step 3 — Chunking:** Split documents into retrievable segments. Chunk size directly impacts both retrieval precision and LLM context quality. See the [Chunking Strategies](#chunking-strategies) section below.

**Step 4 — Embedding:** Run each chunk through the embedding model to produce a dense vector. Store the vector alongside the original text and metadata.

**Step 5 — Indexing:** Insert vectors into the vector store. Also store metadata in a separate relational or document store for filtering.

### Online Query Pipeline

This pipeline executes for every user query, typically in under 500ms for production systems.

**Step 1 — Query Transformation:** Optionally rewrite or expand the query to improve retrieval. Techniques: HyDE, multi-query, step-back prompting, query decomposition.

**Step 2 — Query Embedding:** Encode the (transformed) query with the same embedding model used during ingestion.

**Step 3 — Hybrid Retrieval:** Perform ANN vector search and BM25 keyword search in parallel. Fuse results using Reciprocal Rank Fusion (RRF).

**Step 4 — Reranking:** Apply a cross-encoder to re-score the top-K candidates and select the best 5–10 chunks.

**Step 5 — Context Assembly:** Build the final prompt: system instructions + retrieved chunks (with source metadata) + user query.

**Step 6 — LLM Generation:** Pass the assembled prompt to the LLM. The model generates a response grounded in the retrieved context.

**Step 7 — Guardrails:** Optionally verify the response for hallucination, relevance, and policy compliance before returning to the user.

---

## Chunking Strategies

Chunking is one of the highest-leverage decisions in a RAG system. The goal is chunks that are *focused enough to retrieve accurately* but *large enough to give the LLM sufficient context*.

| Strategy | Description | Best For |
|---|---|---|
| **Fixed-size** | Split at N tokens, with overlap | Simple default; fast ingestion |
| **Recursive character splitting** | Split by paragraph, then sentence, then word | Best general-purpose default (512 tokens, 50-100 overlap) |
| **Semantic chunking** | Split at topic boundaries using embeddings | Long mixed-topic documents |
| **Hierarchical (parent-child)** | Index small chunks, retrieve parent context | Balance precision + context |
| **Document-level** | No splitting; index whole docs | Short, single-topic documents |
| **Late chunking** | Embed full doc first, then split | Preserves inter-sentence context in embeddings |
| **Agentic chunking** | LLM decides chunk boundaries | Complex, messy, heterogeneous documents |

**Recommended defaults:** Recursive character splitting at 512 tokens with 50–100 tokens of overlap. Research from 2025–2026 benchmarks shows this outperforms more complex strategies in end-to-end accuracy (69% vs 54% for semantic chunking on FloTorch's test set). Always preserve 20–30% overlap to avoid context gaps at boundaries.

---

## Retrieval Strategies

### Vector (Semantic) Search
Encodes the query and retrieves chunks with the highest cosine similarity. Excels at paraphrase matching and semantic understanding but can miss exact keyword matches.

### BM25 (Keyword Search)
Classic TF-IDF-based ranking. Excels at exact-match queries, product codes, names, and rare terms. Fails on synonyms and paraphrases.

### Hybrid Search (Recommended)
Combines vector and BM25 results via **Reciprocal Rank Fusion (RRF)**. Consistently outperforms single-method retrieval. Most vector databases (Weaviate, Qdrant, Elasticsearch) support this natively.

### Metadata Filtering
Apply pre-filters (date range, source type, author, document category) before vector search to reduce the candidate pool and improve precision.

### Query Transformation Techniques

- **Multi-query:** Generate 3–5 variants of the query, retrieve for each, merge results.
- **HyDE (Hypothetical Document Embeddings):** Ask the LLM to generate a "hypothetical ideal answer," embed that, and retrieve similar real documents. Bridges the query-document phrasing gap.
- **Step-back prompting:** Generalize the specific query into a broader concept before retrieval.
- **Query decomposition:** Break compound questions into sub-questions and retrieve for each.

---

## Advanced RAG Patterns

![Advanced RAG Patterns](diagrams/rag-advanced-patterns.drawio.svg)

### Self-RAG
The model learns to decide *when* to retrieve (not every query needs retrieval) and critically evaluates its own outputs using special reflection tokens (`[Retrieve]`, `[ISREL]`, `[ISSUP]`, `[ISUSE]`). Reduces unnecessary retrieval, improves factuality.

### Corrective RAG (CRAG)
Evaluates the quality of retrieved documents. If docs are deemed irrelevant or incorrect, it triggers a fallback to web search and document refinement before generation. Robust against low-quality or stale knowledge bases.

### Graph RAG
Combines vector retrieval with a **knowledge graph** (nodes = entities, edges = relationships). Enables multi-hop reasoning and community-level summarization. Particularly powerful for scientific, medical, and financial domains where entities are highly interconnected. Developed by Microsoft Research.

### Agentic RAG
Transforms the static RAG pipeline into a **dynamic reasoning loop**. An orchestrator agent plans multi-step retrieval, selects tools (vector DB, SQL database, web search, APIs), reflects on intermediate results, and adapts strategy. Enables complex question answering that requires synthesizing information from multiple heterogeneous sources.

### Modular RAG
Decomposes the pipeline into interchangeable modules (query planner, retriever, reranker, answer generator, critic) that can be swapped, combined, or parallelized independently. The current frontier for production RAG systems.

### Long RAG
Processes longer retrieval units (sections, full documents) instead of small chunks. Preserves document-level context and reduces chunking-induced information loss. Useful for legal, medical, and scientific texts.

---

## Embedding Models

The embedding model is the "semantic backbone" of a RAG system. Mismatches between the model used at index time and query time will break retrieval.

| Model | Provider | Notes |
|---|---|---|
| `text-embedding-3-small` / `large` | OpenAI | Strong general-purpose; widely used |
| `embed-english-v3.0` | Cohere | Excellent multilingual support |
| `BGE-M3` | BAAI | Open-source; top MTEB scores; multilingual |
| `E5-large-v2` | Microsoft | Strong zero-shot retrieval |
| `nomic-embed-text` | Nomic AI | Open-source; long context (8192 tokens) |
| `mxbai-embed-large` | MixedBread | High MTEB performance; open-source |

**Key advice:** Domain-specific models outperform general-purpose ones by 20–40% in specialized fields (medical, legal, code). Always benchmark on a representative sample from your actual data before committing to a model.

---

## Vector Databases

| Database | Type | Best For |
|---|---|---|
| **Pinecone** | Managed SaaS | Production scale; minimal ops; &lt;50ms queries |
| **Weaviate** | OSS + Managed | Strong hybrid search; flexible schema |
| **Qdrant** | OSS + Managed | Rust-based; memory efficient; great filters |
| **Chroma** | OSS (local) | Rapid prototyping; local development |
| **FAISS** | Library | High-performance research; no persistence layer |
| **Milvus** | OSS + Managed | Billion-scale; data engineering heavy |
| **pgvector** | PostgreSQL ext. | Already on Postgres; simple deployments |

**Decision guide:**
- Starting out / prototyping → **Chroma** or **pgvector**
- Production, no infra team → **Pinecone**
- Open-source, hybrid search → **Weaviate** or **Qdrant**
- Billion-scale with data engineering → **Milvus**

---

## Main Frameworks & Tools

### Orchestration Frameworks

**LangChain** — The most widely adopted RAG framework. Excels at rapid prototyping and chaining multiple components. Large ecosystem of integrations. `LangGraph` extension adds stateful, multi-step workflows with cycles (ideal for Agentic RAG). Higher overhead than alternatives.

**LlamaIndex** — Optimized for document ingestion and indexing. Achieved 35% boost in retrieval accuracy in 2025 benchmarks. 40% faster document retrieval than LangChain. Best for document-heavy applications.

**Haystack (deepset)** — Production-grade, modular pipeline framework. Lowest framework overhead (~5.9ms). Excellent evaluation tooling. Best for teams that need fine-grained control and production reliability.

**RAGFlow** — Visual, open-source RAG pipeline builder. Good for teams that want workflow visibility without heavy coding.

### Evaluation Frameworks

**RAGAS** — The standard framework for reference-free RAG evaluation. Metrics include Faithfulness, Answer Relevancy, Context Precision, Context Recall, and Context Entity Recall. Uses LLM-as-judge internally.

**DeepEval** — Alternative evaluation suite with support for hallucination detection, answer relevancy, and toxicity metrics.

**Langfuse** — Observability platform for LLM applications; integrates with RAGAS for production monitoring.

**TruLens** — Evaluation and tracking framework with RAG triad (groundedness, answer relevance, context relevance).

### Supporting Tools

| Category | Tools |
|---|---|
| **Reranking** | Cohere Rerank, Jina Reranker, BGE Reranker, Flashrank |
| **Document parsing** | Unstructured.io, LlamaParse, Apache Tika, Docling |
| **Web scraping** | Firecrawl, Jina Reader, Apify |
| **Observability** | Langfuse, LangSmith, Arize Phoenix, W&B Weave |
| **Guardrails** | NeMo Guardrails, Guardrails AI, LlamaGuard |

---

## Evaluation Metrics (RAGAS)

A RAG system must be evaluated across **three dimensions**: retrieval quality, generation quality, and end-to-end performance.

### Retrieval Metrics
- **Context Precision** — Are the retrieved chunks ranked in relevance order? (0–1, higher is better)
- **Context Recall** — Does the retrieved context contain all information needed to answer? (0–1)
- **MRR (Mean Reciprocal Rank)** — How early does the first relevant chunk appear in the ranked list?
- **NDCG@k** — Normalized Discounted Cumulative Gain; measures ranked retrieval quality.

### Generation Metrics
- **Faithfulness** — Is the generated answer factually consistent with the retrieved context? Key hallucination metric. (0–1)
- **Answer Relevancy** — Does the generated answer actually address the user's question? (0–1)

### End-to-End Metrics
- **Exact Match / F1** — Against reference answers for benchmark datasets.
- **Latency** — Time-to-first-token and total response time.
- **Cost per query** — Token cost across embedding, retrieval, reranking, and generation.

> **Important:** Different LLM judges can score the same system very differently. Claude 3 Sonnet and Llama 3 can differ by 80 percentage points on faithfulness for poorly-retrieved content. Always use multiple judges or human evaluation for critical systems.

---

## Best Practices & Recommendations

### Data & Ingestion
- **Start with data quality**, not model quality. Garbage in, garbage out applies strongly to RAG.
- **Preserve metadata** (source URL, date, section, document type). It enables filtering and source attribution.
- **Use domain-specific embedding models** for medical, legal, financial, or code retrieval.
- **Chunk with overlap** (20–30%) to prevent information loss at boundaries.
- **Default to recursive splitting at 512 tokens** unless you have a compelling reason to use a more complex strategy.
- **Update your index incrementally.** Stale knowledge is a common RAG failure mode.

### Retrieval
- **Always use hybrid search** (vector + BM25 + RRF) in production. It consistently outperforms either method alone.
- **Add metadata filtering** to reduce the search space before ANN search.
- **Implement a reranker** (cross-encoder) — it is the single highest-ROI improvement for retrieval quality.
- **Retrieve more than you need** (top-100) then rerank to top-5. This improves recall without sacrificing precision.
- **Apply query transformation** (multi-query or HyDE) for vague or underspecified queries.

### Generation
- **Keep prompts structured.** Separate instructions, context, and the question with clear delimiters.
- **Instruct the model to cite sources** and indicate uncertainty. Reduces hallucination rate.
- **Limit context window usage.** More context is not always better — irrelevant chunks hurt accuracy (the "lost in the middle" phenomenon).
- **Apply guardrails** post-generation to check faithfulness before returning to the user.

### Architecture
- **Cache embeddings** for frequently occurring documents to reduce ingestion costs.
- **Separate retrieval from generation infrastructure** for independent scaling.
- **Monitor drift.** Track retrieval quality, faithfulness, and latency over time in production.
- **Evaluate on your own data.** Public benchmarks rarely reflect your specific domain distribution.
- **Consider Agentic RAG** when questions require multi-hop reasoning or multiple data sources.
- **Use Graph RAG** when your domain has rich entity relationships (biomedical, legal, financial).

---

## RAG vs. Fine-tuning vs. Long Context

| Approach | RAG | Fine-tuning | Long Context Window |
|---|---|---|---|
| **Knowledge freshness** | ✅ Real-time | ❌ Static | ⚠️ Depends on input |
| **Cost** | ⚠️ Per-query retrieval | 💰 High upfront training | 💰 High per-query tokens |
| **Accuracy on domain** | ✅ High | ✅ High | ⚠️ May lose in the middle |
| **Interpretability** | ✅ Citable sources | ❌ Black-box | ⚠️ Hard to trace |
| **Implementation complexity** | ⚠️ Medium | 💰 High | ✅ Low |
| **Catastrophic forgetting risk** | ✅ None | ❌ Possible | ✅ None |

In practice, the best production systems combine **RAG + fine-tuning**: fine-tune for style, tone, and task format; use RAG for current knowledge grounding.

---

## Common Failure Modes

- **Retrieval misses** — The relevant information is in the knowledge base but isn't retrieved (wrong chunking, poor embeddings, low-recall retrieval).
- **Hallucination despite retrieval** — The LLM ignores the context and generates from parametric memory. Mitigate with explicit grounding instructions and faithfulness checks.
- **Lost in the middle** — Relevant information is retrieved but placed in the middle of a long context window where the LLM tends to ignore it. Use reranking and limit context size.
- **Chunk boundary issues** — A critical sentence is split across two chunks. Mitigate with overlap.
- **Semantic drift** — The embedding model represents the query and documents in incompatible ways (e.g., different vocabularies, jargon). Use domain-adapted models.
- **Index staleness** — The knowledge base hasn't been updated and returns outdated information.
- **Context window overflow** — Too many chunks retrieved, exceeding the model's context limit.

---

## References

### Foundational Papers

- Lewis, P. et al. (2020). [Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks](https://arxiv.org/abs/2005.11401). *NeurIPS 2020*.
- Shi, F. et al. (2023). [Large Language Models Can Be Easily Distracted by Irrelevant Context](https://arxiv.org/abs/2302.00093). *ICML 2023*.
- Asai, A. et al. (2023). [Self-RAG: Learning to Retrieve, Generate, and Critique through Self-Reflection](https://arxiv.org/abs/2310.11511). *ICLR 2024*.
- Yan, S. et al. (2024). [Corrective Retrieval Augmented Generation](https://arxiv.org/abs/2401.15884).
- Es, S. et al. (2023). [RAGAS: Automated Evaluation of Retrieval Augmented Generation](https://arxiv.org/abs/2309.15217).
- Edge, D. et al. (2024). [From Local to Global: A Graph RAG Approach to Query-Focused Summarization](https://arxiv.org/abs/2404.16130). *Microsoft Research*.

### Guides & Documentation

- [The 2025 Guide to Retrieval-Augmented Generation (RAG)](https://www.edenai.co/post/the-2025-guide-to-retrieval-augmented-generation-rag) — Eden AI
- [RAG in 2026: How Retrieval-Augmented Generation Works for Enterprise AI](https://www.techment.com/blogs/rag-in-2026/) — Techment
- [Advanced RAG Techniques — Neo4j](https://neo4j.com/blog/genai/advanced-rag-techniques/)
- [Chunking Strategies for RAG — Weaviate](https://weaviate.io/blog/chunking-strategies-for-rag)
- [Best Chunking Strategies for RAG in 2025 — Firecrawl](https://www.firecrawl.dev/blog/best-chunking-strategies-rag)
- [RAG Chunking Strategies: The 2026 Benchmark Guide — PremAI](https://blog.premai.io/rag-chunking-strategies-the-2026-benchmark-guide/)
- [Agentic Retrieval Guide — LlamaIndex](https://www.llamaindex.ai/blog/rag-is-dead-long-live-agentic-retrieval)
- [Retrieval-Augmented Generation in Azure AI Search — Microsoft](https://learn.microsoft.com/en-us/azure/search/retrieval-augmented-generation-overview)
- [RAG Frameworks Comparison 2025 — LangCopilot](https://langcopilot.com/posts/2025-09-18-top-rag-frameworks-2024-complete-guide)
- [Best Vector Databases for RAG 2025 — Firecrawl](https://www.firecrawl.dev/blog/best-vector-databases)
- [RAGAS Evaluation Metrics Documentation](https://docs.ragas.io/en/stable/concepts/metrics/available_metrics/)
- [RAG 2.0: The 2025 Guide to Advanced RAG — Vatsal Shah](https://vatsalshah.in/blog/the-best-2025-guide-to-rag)

### Related Notes

- [[AI-Agent-Architectures]]
- [[AI-Agent-Harness-Engineering]]
- [[context-engineering]]
- [[agent-compound-engineering]]
