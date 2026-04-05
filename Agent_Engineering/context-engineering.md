# Context Engineering

#context-engineering #llm #agents #prompt-engineering #ai #rag #memory

> *"Context engineering is the delicate art and science of filling the context window with just the right information for the next step."*
> — Andrej Karpathy

---

## What Is Context Engineering?

Context engineering is the discipline of strategically designing, curating, and managing the information that flows into a language model's context window during inference. It represents the evolution beyond **prompt engineering** — moving from crafting clever sentences to orchestrating the full information architecture that enables AI agents to succeed at complex, multi-step tasks.

While prompt engineering asked: *"What instructions should I give the model?"*, context engineering asks: *"What is the full configuration of context that maximizes the likelihood of my desired outcome?"*

A useful mental model: **the LLM is a CPU, and the context window is RAM** — a limited, precious workspace that must be managed with care.

> Most agent failures are not model failures. They are **context failures**.

---

## Context Window Anatomy

The context window is composed of multiple distinct layers, each competing for the same finite token budget:

![Context Window Anatomy](diagrams/context-window-anatomy.drawio.svg)

| Layer | Description |
|---|---|
| **System Prompt** | Role definition, instructions, output format, guardrails |
| **Tool Definitions** | Available function schemas and parameter descriptions |
| **Retrieved Memory** | Long-term facts, past episodes, user preferences (via RAG) |
| **Retrieved Knowledge** | Relevant documents and vector search results |
| **Conversation History** | Prior user/assistant turns, tool calls, and observations |
| **Tool Outputs** | Execution results, API responses, code output |
| **Scratchpad / Working State** | In-progress reasoning, notes, intermediate results |
| **Current User Input** | The immediate task, request, or query |

As the window fills, models experience **context rot** — recall accuracy degrades with volume. GPT-4o, for example, dropped from 98.1% to 64.1% accuracy based solely on context size and information presentation.

---

## The Four Core Strategies

Context engineering is organized around four primary strategies for managing what enters the model's context:

![Context Engineering: Four Core Strategies](diagrams/context-engineering-strategies.drawio.svg)

### 1. Write — Save Information Outside the Window

Store important information externally so it can be retrieved later without permanently consuming context space.

- **Scratchpads**: Agents write notes mid-task via tool calls or state objects
- **Long-term memory stores**: Persist facts, preferences, and reflections across sessions (as used in ChatGPT, Cursor, Windsurf)
- **External files**: Structured notes, progress logs, and dependency maps for complex tasks
- **State objects**: Structured records of task progress and decisions

### 2. Select — Retrieve Only What Is Relevant

Pull information into context just-in-time, rather than pre-loading everything.

- **RAG (Retrieval-Augmented Generation)**: Semantic vector search over knowledge bases
- **Knowledge graphs**: Episodic, procedural, and semantic memory types
- **Dynamic tool selection**: Use semantic search over tool descriptions (achieving up to 3x better accuracy)
- **Progressive disclosure**: Let agents incrementally discover context through exploration, mirroring human cognition

### 3. Compress — Reduce Tokens While Preserving Signal

Reduce the volume of context without losing critical information.

- **Summarization**: Recursive or hierarchical compaction at trajectory boundaries or post-tool-call
- **Trimming**: Remove older messages via heuristics or trained pruning models
- **Compaction**: Auto-compact at ~95% window capacity (as done by Claude Code), preserving architectural decisions and key facts
- **Distillation**: Condense sub-agent results to 1,000–2,000 token summaries before returning to a coordinator

### 4. Isolate — Partition Context Across Components

Distribute work so no single context window becomes overloaded.

- **Multi-agent architectures**: Specialized sub-agents with independent context windows; Anthropic's research system showed a 90.2% improvement using this approach
- **Sandboxed execution**: Run code and tools outside the LLM context, injecting only relevant summaries
- **Selective state exposure**: Expose only relevant schema fields to the model at each step
- **Context partitioning**: Divide parallelizable workloads among agents with focused responsibilities

---

## Failure Modes & Mitigations

Context engineering failures are systematic and predictable. Understanding them is the first step to prevention:

![Context Engineering: Failure Modes & Mitigations](diagrams/context-failure-modes.drawio.svg)

### ☣ Context Poisoning
A single hallucination or error embeds in the context and is repeatedly referenced, compounding mistakes across the conversation (e.g., a misidentified product leads to a cascade of wrong recommendations).

**Mitigation:** Validate tool outputs before adding to context. Use reversible compression. Implement error-checking verification agents.

### 💤 Context Distraction
When context exceeds model capacity (100k+ tokens), models over-focus on accumulated history rather than applying their trained knowledge, favoring pattern-matching from prior interactions over generalization.

**Mitigation:** Apply trimming and summarization proactively. Keep only the last 3–5 tool outputs in active context. Archive older information to external memory.

### 🌀 Context Confusion
Irrelevant tools, documents, or information dilutes the signal. Research from the Berkeley Function-Calling Leaderboard found that *"every single model performs worse when given multiple tools, without exception."*

**Mitigation:** Use dynamic tool selection via semantic search. Keep context scoped to the current task. Minimize toolset overlap and ambiguous decision points.

### ⚡ Context Clash
Contradictory information within the context derails reasoning. Once an LLM takes a wrong turn, research shows it tends not to self-correct.

**Mitigation:** Deduplicate and reconcile retrieved facts before injection. Use structured state schemas. Apply chain-of-thought prompting to surface and resolve contradictions.

### 💀 Context Rot
As total token volume grows, recall accuracy degrades progressively — a "needle in a haystack" degradation effect that compounds with context size.

**Mitigation:** Proactively compact before hitting capacity. Use multi-agent isolation for tasks exceeding 50k tokens. Offload completed results to external memory.

---

## Key Principles & Best Practices

### Principle 1: Treat Context as a Finite, Precious Resource

Every token added to the context window is a cost — not only in money, but in attention quality. The guiding objective is always: **find the smallest set of high-signal tokens that maximize the likelihood of your desired outcome.**

### Principle 2: Just-In-Time Retrieval Over Pre-Loading

Rather than front-loading all potentially relevant information, agents should maintain lightweight identifiers (file paths, document IDs, keys) and retrieve information dynamically using tools when actually needed. This mirrors how human experts work.

### Principle 3: Design System Prompts at the Right Altitude

System prompts should strike a balance:
- Not so complex that they encode brittle, hardcoded logic
- Not so vague that they assume shared understanding
- **Start minimal**, then add instructions based on observed failure modes
- Aim for the smallest information set that fully defines expected behavior
- Use XML tags or Markdown headers to separate distinct sections clearly

### Principle 4: Design Tools for Clarity and Parsimony

- Minimize overlapping tool functionality — ambiguity between tools degrades selection accuracy
- Make input parameters descriptive and unambiguous
- Ensure tools are self-contained and robust to unexpected inputs
- Avoid bloated toolsets; prefer dynamic tool loading over up-front exposure

### Principle 5: Use Few-Shot Examples Strategically

- Curate diverse, canonical examples that demonstrate expected behavior
- Avoid exhaustive edge case lists — they increase context volume without proportional benefit
- Treat examples as demonstrations, not rules
- Update examples based on failure modes, not assumptions

### Principle 6: Engineer for Long-Horizon Tasks

For tasks spanning many steps:
- Enable structured note-taking via files outside the context window
- Use sub-agent architectures with clean context windows per agent
- Implement auto-compaction before reaching context limits
- Preserve architectural decisions and key milestones during compression; discard intermediate outputs

### Principle 7: Observe, Measure, and Iterate

Context engineering is empirical, not intuitive:
- Implement comprehensive logging and token usage tracing
- Use evaluation pipelines to validate improvements (not gut feel)
- Establish baseline metrics before making changes
- Identify the highest-impact optimizations first (not the most interesting ones)

---

## Context Size Decision Framework

| Context Size | Recommended Strategy |
|---|---|
| < 10k tokens | Simple append-only with basic caching |
| 10k – 50k tokens | Compression at boundaries + KV-cache optimization |
| 50k – 100k tokens | Offload to external memory + smart retrieval |
| > 100k tokens | Multi-agent isolation architecture |

### When to Apply Context Engineering

Apply these techniques when:
- Context exceeds **30k tokens** per session
- Tasks require **more than 10 tool calls**
- Processing **more than 1,000 sessions per day**

For simple prototypes, single-turn queries, or real-time applications where retrieval latency is unacceptable, simpler approaches often suffice.

---

## What Belongs in Context vs. External Memory

### Keep In the Active Context Window
- Current task objectives and constraints
- Recent tool outputs (last 3–5 calls)
- Active errors and warnings requiring attention
- Immediate conversation history
- Currently relevant facts and decisions

### Store in External Memory
- Historical conversations and sessions
- Learned patterns and user preferences
- Large reference documents
- Intermediate computational results
- Completed task summaries and archived artifacts

---

## Memory Architecture

A complete memory system for AI agents typically includes three types:

**Short-term Memory** — the live context window: recent turns, retrieved documents, tool outputs.

**Working Memory** — temporary scratchpads for multi-step task information; reset between sessions.

**Long-term Memory** — external storage (vector databases, knowledge graphs) for:
- *Episodic memory*: past interactions and experiences
- *Semantic memory*: facts, knowledge, and concepts
- *Procedural memory*: learned patterns and how-to information

Memory management principles:
- Filter which interactions get stored using importance scoring
- Prune outdated entries and merge duplicates regularly
- Use recency and retrieval frequency as retention signals
- Tailor architecture to the specific task requirements

---

## Anti-Patterns to Avoid

- ❌ Loading all available tools upfront — use dynamic selection instead
- ❌ Modifying previous context entries — invalidates KV-cache
- ❌ Aggressive pruning without a recovery mechanism
- ❌ Ignoring error messages in context — they are learning opportunities
- ❌ Stopping at basic instructions without detailed specifications
- ❌ Omitting temporal context for time-sensitive queries
- ❌ Over-engineering prematurely — simpler solutions often win as models improve
- ❌ Iterating on intuition rather than measured evaluation results

---

## Context Engineering vs. Prompt Engineering

| Dimension | Prompt Engineering | Context Engineering |
|---|---|---|
| **Scope** | Single prompt / instruction | Full information architecture |
| **Focus** | What words to use | What information to include |
| **Metaphor** | Writing a magic spell | Writing a full screenplay |
| **Primary concern** | Instruction quality | Token budget management |
| **Key skill** | Phrasing & framing | System design & retrieval |
| **Scale** | Works at demo scale | Required for production scale |

---

## References

- [Effective Context Engineering for AI Agents — Anthropic Engineering](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [Context Engineering for Agents — LangChain Blog](https://blog.langchain.com/context-engineering-for-agents/)
- [Context Engineering: LLM Memory and Retrieval for AI Agents — Weaviate](https://weaviate.io/blog/context-engineering)
- [Context Engineering Guide — Prompting Guide](https://www.promptingguide.ai/guides/context-engineering-guide)
- [Deep Dive into Context Engineering for Agents — Galileo AI](https://galileo.ai/blog/context-engineering-for-agents)
- [Context Engineering: What It Is and Techniques to Consider — LlamaIndex](https://www.llamaindex.ai/blog/context-engineering-what-it-is-and-techniques-to-consider)
- [Andrej Karpathy on Context Engineering — X (Twitter)](https://x.com/karpathy/status/1937902205765607626)
- [Context Engineering — GitHub (davidkimai)](https://github.com/davidkimai/Context-Engineering)
- [Context Engineering: The Definitive 2025 Guide — FlowHunt](https://www.flowhunt.io/blog/context-engineering/)
- [Context Engineering Best Practices for Reliable AI — Kubiya](https://www.kubiya.ai/blog/context-engineering-best-practices)
