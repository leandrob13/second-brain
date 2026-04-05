# GraphRAG — Graph Retrieval-Augmented Generation

#graphrag #rag #knowledge-graph #llm #agent-engineering #retrieval #graph-database #microsoft

> **GraphRAG** extends traditional RAG by replacing flat vector retrieval with a structured **knowledge graph**, enabling multi-hop reasoning, relationship traversal, and both local and global corpus understanding.

---

## What is GraphRAG?

GraphRAG (Graph Retrieval-Augmented Generation) is a technique pioneered by **Microsoft Research** that combines knowledge graphs with large language models to overcome the fundamental limitations of traditional RAG systems. While standard RAG retrieves semantically similar text chunks via vector search, GraphRAG builds an explicit graph of entities, relationships, and communities from the source corpus — enabling richer, more connected answers.

The core insight: **relationships between entities carry information that embeddings alone cannot capture**. When asking "How did the acquisition of Company X affect the strategy of Company Y?", a vector search returns isolated chunks. A knowledge graph traverses the path *X → acquisition → Y → strategy*, surfacing the answer directly.

Key reference: [From Local to Global: A Graph RAG Approach to Query-Focused Summarization (Microsoft, 2024)](https://arxiv.org/abs/2404.16130)

---

## Core Principles

### 1. Knowledge Graph as the Index
Instead of a flat embedding store, GraphRAG builds a property graph where **nodes** are entities (people, organizations, places, concepts) and **edges** are relationships between them. Each node and edge carries an LLM-generated description summary.

### 2. Hierarchical Community Structure
The graph is partitioned into communities using the **Leiden algorithm** (a refinement of Louvain), producing a hierarchy of clusters from fine-grained (Level 0: 2–10 entities) up to coarse-grained theme clusters. Each community receives an LLM-generated summary that captures its collective meaning.

### 3. Dual Search Modes: Local and Global
GraphRAG introduces two retrieval strategies addressing fundamentally different query types:

| Mode | Best For | Mechanism |
|---|---|---|
| **Local Search** | Specific entity/relationship queries | Vector search + graph traversal of neighbors |
| **Global Search** | Thematic/corpus-wide questions | Parallel LLM calls over community summaries → aggregation |

### 4. TextUnits as the Atomic Processing Unit
Before any graph construction, source documents are split into **TextUnits** — overlapping or fixed-size text chunks that preserve semantic coherence. Every entity and relationship extracted is traceable back to the originating TextUnit.

### 5. Entity Resolution
The same real-world entity can appear under different surface forms ("Jane Smith", "J. Smith", "the CFO"). GraphRAG uses an LLM to merge these into a unified node with a consolidated description, preventing graph fragmentation.

### 6. Explainability and Provenance
Every answer can be traced through the graph: retrieved entities → relationships → source TextUnits → original documents. This chain of provenance makes GraphRAG significantly more auditable than opaque vector retrieval.

---

## Architecture

### Indexing Pipeline

![GraphRAG Indexing Pipeline](diagrams/graphrag-indexing-pipeline.drawio.svg)

The indexing pipeline transforms raw documents into a queryable knowledge graph in the following stages:

**Stage 1 — Ingestion & Chunking:** Source documents are loaded and split into TextUnits. Chunk size is configurable; overlapping windows preserve context across boundaries.

**Stage 2 — Entity & Relationship Extraction:** An LLM processes each TextUnit and extracts entities (with types and descriptions) and directed relationships (with descriptions and strength scores). Multiple extraction passes with different prompts can improve recall.

**Stage 3 — Entity Resolution & Summarization:** Duplicate entity mentions are merged via co-reference resolution. The LLM generates a unified summary description for each canonical entity node.

**Stage 4 — Knowledge Graph Construction:** Extracted entities become nodes; extracted relationships become edges. Attributes (descriptions, source references, strength) are stored as node/edge properties.

**Stage 5 — Community Detection (Leiden):** The graph is partitioned hierarchically using the Leiden algorithm. The result is a multi-level community hierarchy where each level captures a different granularity of topic clustering.

**Stage 6 — Community Summarization:** For each community at every level, an LLM generates a natural-language summary. Summaries at higher levels incorporate lower-level summaries (bottom-up).

**Stage 7 — Indexing:** The final artifacts — graph database, vector embeddings of entities and text chunks, community summaries — are persisted and made queryable.

---

### Query & Retrieval Flow

![GraphRAG Query Flow: Local vs Global](diagrams/graphrag-query-flow.drawio.svg)

**Local Search Flow:**
1. Query is embedded and used to retrieve top-K relevant text chunks from the vector index.
2. Entities mentioned in those chunks are identified in the graph.
3. The graph is traversed to fetch neighboring entities, relationships, and their descriptions.
4. All retrieved context (chunks + graph context) is assembled into a prompt.
5. The LLM generates a grounded, relationship-aware answer.

**Global Search Flow:**
1. Community summaries at the appropriate hierarchy level are selected.
2. Parallel LLM calls generate a partial answer from each community summary.
3. Partial answers are aggregated by a final LLM call into a coherent global response.
4. Ideal for questions like "What are the major themes in this dataset?"

---

### GraphRAG vs Traditional RAG

![GraphRAG vs Traditional RAG](diagrams/graphrag-vs-rag.drawio.svg)

| Dimension | Traditional RAG | GraphRAG |
|---|---|---|
| **Index type** | Vector embeddings (flat) | Knowledge graph + vectors |
| **Retrieval** | Semantic similarity (top-K) | Graph traversal + semantic search |
| **Reasoning** | Single-hop (chunk-level) | Multi-hop (entity → relation chains) |
| **Global queries** | Poor — misses corpus-wide themes | Excellent — community summaries |
| **Explainability** | Low — black-box embeddings | High — traceable entity paths |
| **Indexing cost** | Low | High (LLM calls per chunk) |
| **Query latency** | Low | Higher (graph traversal overhead) |
| **Best for** | Factoid lookups, Q&A | Complex reasoning, relationship analysis |

**Key finding (Microsoft Research):** GraphRAG improves answer **comprehensiveness** by up to 35% and **diversity** significantly for global sensemaking tasks on 1M+ token corpora.

---

## Main Tools & Ecosystem

### Microsoft GraphRAG (Reference Implementation)
The canonical open-source implementation from Microsoft Research.

- **Repo:** [github.com/microsoft/graphrag](https://github.com/microsoft/graphrag)
- **Docs:** [microsoft.github.io/graphrag](https://microsoft.github.io/graphrag/)
- **Features:** Full indexing pipeline, local/global search, prompt tuning, Azure OpenAI integration
- **LazyGraphRAG:** A lightweight variant offering 700x lower query cost by deferring community summarization to query time. Ideal for exploratory analysis and streaming data.

### Graph Databases
| Tool | Notes |
|---|---|
| **Neo4j** | Most mature GraphRAG ecosystem; native Cypher + vector indexes; Knowledge Graph Builder UI |
| **Amazon Neptune** | Managed AWS option; supports property graphs and RDF |
| **TigerGraph** | High-performance for large-scale graphs |
| **NebulaGraph** | Open-source, cloud-native distributed graph DB |
| **FalkorDB** | Purpose-built for GraphRAG; sparse matrix graph engine |

### Orchestration Frameworks
| Tool | Notes |
|---|---|
| **LangChain** | `GraphCypherQAChain`; LLM-to-Cypher query generation; Neo4j integrations |
| **LlamaIndex** | `KnowledgeGraphIndex`, Neo4j vector + Cypher retrieval; `GraphRAG_v2` cookbook |
| **LangGraph** | Stateful graph workflows; conditional routing between local/global retrieval paths |
| **Haystack** | Community Neo4j integration for vector + Cypher querying |
| **DSPy** | Neo4j Retriever Module with vector index support |
| **Spring AI / LangChain4j** | Java ecosystem Neo4j vector search integration |

### Supporting Tools
- **Neo4j Knowledge Graph Builder** — UI for transforming unstructured text → graphs without code
- **NeoConverse** — Natural language to Cypher query interface
- **Zilliz / Milvus** — High-performance vector store for the embedding layer
- **Meilisearch** — Fast vector-capable search engine for the initial retrieval layer

---

## Best Practices

### When to Use GraphRAG
GraphRAG adds the most value when:
- Queries require **multi-hop reasoning** across multiple documents (e.g., "How is X connected to Y through Z?")
- The dataset contains **dense entity networks** (e.g., medical records, legal cases, financial filings, research papers)
- You need **global sensemaking** — understanding themes, trends, or patterns across an entire corpus
- **Explainability and provenance** are required (regulated industries, audit trails)
- Documents contain **many coreferences** and **ambiguous entity mentions**

**Do NOT use GraphRAG** when:
- Queries are simple factoid lookups well-served by vector search
- The corpus is small (<50K tokens) — overhead is not justified
- Latency is critical and sub-100ms responses are required
- Time-sensitive queries (GraphRAG performs ~16% worse on recency-based questions than vanilla RAG)
- Budget is constrained — indexing requires many LLM calls per chunk

### Indexing Best Practices
1. **Tune prompts before production.** Default extraction prompts are generic. Domain-specific prompts (medical, legal, financial) dramatically improve entity and relationship quality. Use `graphrag prompt-tune` with sample data.
2. **Start small.** Run the full pipeline on a representative 10K-token sample first. Validate the graph structure before committing to a full corpus indexing job.
3. **Choose chunk size carefully.** Larger chunks give more context to the LLM extractor but cost more. 300–600 token chunks are a common starting point.
4. **Enable multi-pass extraction.** Run entity extraction with multiple diverse prompts and merge results to improve recall.
5. **Monitor entity resolution quality.** Entity resolution accuracy below 85% makes graph traversal unreliable. Validate merged entities manually on a sample.
6. **Use cheap models for extraction.** GPT-4o-mini or Claude Haiku can perform extraction at a fraction of the cost of frontier models while maintaining acceptable quality.

### Query Best Practices
1. **Route queries explicitly.** Build a query classifier to route specific entity questions to local search and thematic questions to global search. Don't rely solely on keyword heuristics.
2. **Tune community level for global search.** Lower levels (finer clusters) give more detail; higher levels (coarser clusters) give better thematic summaries. Test both for your use case.
3. **Combine with vector RAG for factoid fallback.** Hybrid retrieval (graph + vector) outperforms either alone. Route to vector search when the graph returns no relevant entities.
4. **Cache community summaries.** Community summaries are static post-indexing. Cache them aggressively to reduce global search latency.
5. **Set a max graph hop depth.** Unlimited traversal can fetch irrelevant distant neighbors. 2–3 hops is typical for most use cases.

### Production Best Practices
1. **Graph update strategy.** Incremental updates are expensive. For frequently changing data, consider periodic full re-indexing rather than real-time graph patching.
2. **Security and privacy.** Graph structures can expose sensitive connection patterns even when individual entities are anonymized. Apply role-based graph access controls.
3. **Observability.** Instrument entity extraction quality, graph traversal depth distributions, community summary hit rates, and answer grounding scores.
4. **LazyGraphRAG for exploration.** When running one-off queries or experimenting with new datasets, LazyGraphRAG avoids the full indexing cost while preserving comparable quality.

---

## Common Challenges

| Challenge | Description | Mitigation |
|---|---|---|
| **High indexing cost** | Many LLM calls per document chunk | Use smaller models for extraction; batch API calls; use LazyGraphRAG |
| **Entity resolution errors** | Merging wrong entities corrupts the graph | Domain-specific resolution prompts; manual validation on samples |
| **Graph quality dependency** | Output quality is only as good as extraction | Iterative prompt engineering; few-shot extraction examples |
| **Scalability** | Large graphs (millions of nodes) require careful DB tuning | Use native graph indexes; partition by domain |
| **Time-sensitive queries** | GraphRAG struggles with recency-based questions | Combine with vector RAG + date-filtered retrieval |
| **Latency** | Graph traversal + LLM calls add overhead | Cache community summaries; limit hop depth; async retrieval |
| **Privacy leakage** | Graph topology reveals implicit connections | Graph access controls; node/edge-level permissions |

---

## Use Cases by Domain

**Healthcare:** Clinical decision support, drug interaction mapping, patient journey analysis, medical literature synthesis across thousands of papers.

**Finance:** Fraud detection through transaction relationship networks, risk assessment via entity connection graphs, regulatory compliance (Know Your Customer chains).

**Legal:** Case law research traversing precedent networks; statute and regulation interaction analysis; contract clause cross-referencing.

**Enterprise Knowledge Management:** Corporate memory systems that connect documents, people, projects, and decisions into a queryable knowledge graph.

**Scientific Research:** Literature synthesis, hypothesis generation by connecting findings across papers, protein interaction networks (Microsoft Discovery platform).

**Cybersecurity / IT Operations:** Incident root cause analysis through dependency graphs; alert correlation; change impact analysis.

---

## Variants and Extensions

**LazyGraphRAG** (Microsoft, 2024): Defers community summarization to query time. Offers 700x+ lower query cost vs full GraphRAG global search with comparable quality. Best for exploratory or infrequent queries.

**HippoRAG:** Mimics hippocampal memory by using knowledge graphs as long-term memory for LLM agents. Enables multi-document integration over time.

**SURGE / KGRAG:** Academic variants exploring subgraph extraction approaches for more targeted context assembly.

**Agentic GraphRAG:** Combines GraphRAG retrieval with LLM agents that dynamically decide what to traverse in the graph, enabling iterative multi-step reasoning workflows.

---

## References

- [From Local to Global: A Graph RAG Approach (Microsoft, arXiv 2024)](https://arxiv.org/abs/2404.16130)
- [Retrieval-Augmented Generation with Graphs — Survey (arXiv 2025)](https://arxiv.org/abs/2501.00309)
- [RAG vs. GraphRAG: A Systematic Evaluation (arXiv 2025)](https://arxiv.org/abs/2502.11371)
- [Microsoft GraphRAG — Official Docs](https://microsoft.github.io/graphrag/)
- [Microsoft GraphRAG — GitHub](https://github.com/microsoft/graphrag)
- [LazyGraphRAG Blog (Microsoft Research)](https://www.microsoft.com/en-us/research/blog/lazygraphrag-setting-a-new-standard-for-quality-and-cost/)
- [Neo4j: What is GraphRAG?](https://neo4j.com/blog/genai/what-is-graphrag/)
- [Neo4j GraphRAG Ecosystem Tools](https://neo4j.com/blog/news/graphrag-ecosystem-tools/)
- [IBM: What is GraphRAG?](https://www.ibm.com/think/topics/graphrag)
- [AWS: Improving RAG Accuracy with GraphRAG](https://aws.amazon.com/blogs/machine-learning/improving-retrieval-augmented-generation-accuracy-with-graphrag/)
- [Do You Really Need GraphRAG? — Towards Data Science](https://towardsdatascience.com/do-you-really-need-graphrag-a-practitioners-guide-beyond-the-hype/)
- [LlamaIndex GraphRAG Implementation Guide](https://docs.llamaindex.ai/en/stable/examples/cookbooks/GraphRAG_v2/)
- [Gartner: Top Trends in D&A for 2026 — GraphRAG](https://www.gartner.com/en/documents/7444326)
- [FalkorDB: What is GraphRAG?](https://www.falkordb.com/blog/what-is-graphrag/)
- [Lettria: Common Challenges in GraphRAG Implementations](https://www.lettria.com/blogpost/an-analysis-of-common-challenges-faced-during-graphrag-implementations-and-how-to-overcome-them)

---

*Related notes: [[rag]], [[agent-engineering]], [[knowledge-graphs]], [[llm-context-engineering]]*
