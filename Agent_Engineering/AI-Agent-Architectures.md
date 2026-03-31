# AI Agent Architectures

#agent-engineering #llm #ai-architecture #multi-agent #best-practices

> **Last updated:** 2026-03-27
> A comprehensive reference on AI Agent Architectures: core principles, design patterns, frameworks, memory systems, orchestration strategies, and production best practices.

---

## Table of Contents

1. [What is an AI Agent?](#what-is-an-ai-agent)
2. [Core Principles](#core-principles)
3. [Agent Architecture Types](#agent-architecture-types)
4. [Key Components](#key-components)
5. [Memory Systems](#memory-systems)
6. [Orchestration Patterns](#orchestration-patterns)
7. [Agentic Frameworks](#agentic-frameworks)
8. [Context Engineering](#context-engineering)
9. [Reliability & Error Handling](#reliability--error-handling)
10. [Observability & Monitoring](#observability--monitoring)
11. [Human-in-the-Loop](#human-in-the-loop)
12. [Evaluation & Testing](#evaluation--testing)
13. [Cost & Latency Management](#cost--latency-management)
14. [Best Practices Summary](#best-practices-summary)
15. [References](#references)

---

## What is an AI Agent?

An **AI Agent** is an autonomous system that uses an LLM as its reasoning engine to perceive its environment, plan actions, use tools, and achieve goals across multiple steps — without requiring human intervention at each step.

The standard agent loop follows: **Thought → Action → Observation → Repeat**

![Agent Core Loop - ReAct Pattern](diagrams/agent-core-loop.drawio.svg)

---

## Core Principles

Anthropic's guidance distills agent design into three governing principles:

**1. Simplicity** — Maintain straightforward design. Many patterns can be implemented in just a few lines of code rather than complex frameworks. Start with the simplest solution; add complexity only when proven necessary.

**2. Transparency** — Show the agent's planning steps explicitly. Users must be able to understand the decision-making process, especially for high-stakes workflows.

**3. Tool Design** — Create clear *agent-computer interfaces* (ACIs) through careful tool documentation and testing — with the same rigor applied to human-computer interfaces. The LLM knows about available tools *only* through their definitions.

---

## Agent Architecture Types

![Agent Architecture Patterns](diagrams/agent-architecture-patterns.drawio.svg)

### ReAct (Reasoning and Acting)

ReAct combines chain-of-thought reasoning with external tool use in a dynamic interleaved loop:

- Agent cycles through: **Thought → Action → Observation**
- Mimics human problem-solving: alternates between thinking and doing
- Adjusts plans on-the-fly based on tool outputs
- Demonstrated superior performance over pure CoT approaches

**Best for:** Tasks requiring dynamic adaptation — web search, code execution, information retrieval.

### Chain of Thought (CoT)

- Linear, step-by-step reasoning without tool interactions
- Foundation for structured thinking in all agent patterns
- Often combined with other architectural approaches

### Plan-and-Execute

- **Planning phase:** Agent generates a full sequence of steps before starting execution
- **Execution phase:** Executor tackles each step sequentially
- **Re-planning:** Optional dynamic adjustment when results deviate from plan
- Better for tasks with clear sequential structure and predictable steps

### Reflexion

Adds autonomous feedback loops through self-reflection:

1. Actor generates response/action
2. Evaluator scores the output (natural language or heuristic)
3. Self-Reflection module generates verbal critique
4. Memory is updated with reflection
5. Cycle resets with improved context

Achieved **91% pass@1** on HumanEval (vs GPT-4's 80%). Key innovation: verbal reinforcement learning *without* weight updates — learning through linguistic feedback stored in episodic memory.

### Tree of Thoughts (ToT)

- Maintains a tree structure where nodes represent partial solutions ("thoughts")
- LLM generates and evaluates intermediate thoughts
- Uses BFS/DFS search for systematic exploration
- Enables lookahead and backtracking when needed
- Significantly enhances LLM capability for logical, mathematical, and planning tasks

### Graph of Thoughts (GoT)

- Evolution beyond hierarchical tree structure
- Allows arbitrary connections and transformations between thoughts
- Enables more complex, non-hierarchical reasoning chains

### AutoGPT-Style (Goal-Directed Loop)

- Agent sets goals autonomously and executes tasks iteratively
- Includes planning, execution, error recovery, and memory
- Foundation for many modern autonomous agentic systems

---

## Key Components

Every production-grade agent system is composed of these building blocks:

### LLM Backbone

The foundation reasoning engine (Claude, GPT-4o, Gemini, DeepSeek). Provides reasoning, planning, and decision-making. The choice of model dramatically affects reliability and capability.

### Tool Use and Function Calling

Tools extend what the agent can do beyond generating text. Best practices:

- **Tool definitions are the highest-leverage optimization.** The LLM only knows about tools through their definitions — invest heavily in clear, precise descriptions.
- Apply validation gates in front of every tool: reject, fix, or escalate (no silent failures).
- Add action-level logs with reasons for every tool call.
- Implement retry logic with exponential backoff and escalation — never infinite loops.

**Standard Tool Categories:**
- File operations (read, write, edit)
- Command execution (bash, shell)
- Code search (glob, grep)
- Web capabilities (search, fetch)
- User interaction (approval requests, questions)
- External APIs (databases, services, calendars)

### Planning Module

Responsible for decomposing goals into actionable steps. Can be implicit (embedded in system prompt) or explicit (a dedicated planning LLM call before execution begins).

---

## Memory Systems

![Agent Memory Systems](diagrams/agent-memory-systems.drawio.svg)

Agents use multiple types of memory, each serving a distinct purpose:

### Short-Term Memory (Working Memory)

- Current session context: system prompt, conversation history, tool outputs
- Think of it as **RAM** — fast, immediate, cleared when session ends
- Limited by the model's context window size
- Managed through careful context engineering (see below)

### Long-Term Memory

- Persists **across multiple sessions**
- Enables personalization and learning over time
- Implemented with databases, knowledge graphs, or vector embeddings
- Allows agents to become more intelligent with use

### Episodic Memory

- Stores specific past events and interaction experiences
- Implemented via vector databases for semantic retrieval
- Finds conceptually similar past experiences even if surface details differ
- *Example:* Travel agent remembers past trip bookings and preferences

### Semantic Memory

- Stores general knowledge, facts, and relationships
- Backed by knowledge bases: policies, FAQs, domain facts
- Retrieved proactively when contextually relevant
- *Example:* Visa rules, product catalogs, compliance policies

### Procedural Memory

- Stores learned skills and optimal action sequences
- Agent's repertoire of effective behaviors
- Evolves through experience and reflection
- *Example:* Optimal multi-step process for booking flights

---

## Orchestration Patterns

![Agent Orchestration Patterns](diagrams/agent-orchestration-patterns.drawio.svg)

### Single Agent

Simple, straightforward. One agent handles the entire task. Best for well-defined, focused problems. Lowest latency, simplest debugging.

### Prompt Chaining

Output of one LLM call becomes the input of the next. Adds validation "gates" between steps. Best for tasks with clear sequential stages where each step depends on the previous.

### Routing

A classifier agent routes requests to specialized handlers. Enables task-appropriate handling without monolithic prompts. Common in customer support and multi-domain systems.

### Parallelization

Multiple agents work simultaneously on independent subtasks. Two key variants:
- **Sectioning:** Different agents handle different aspects of the problem
- **Voting:** Multiple agents produce outputs; a synthesis step selects or merges the best answer

### Supervisor / Worker (Orchestrator-Worker)

1. Supervisor receives user request and decomposes it into subtasks
2. Delegates work to specialized worker agents
3. Monitors progress and validates outputs
4. Synthesizes a unified final response

**Best for:** Complex tasks requiring multiple specialized skills — document analysis, business process automation.

### Hierarchical Multi-Agent

Multiple layers of orchestration. Each division has its own supervisor; a higher-level orchestrator coordinates across divisions. Balances flexibility with control.

**Best for:** Enterprise systems with many specialized domains.

### Peer-to-Peer

Agents coordinate directly without a central orchestrator. Useful for collaborative creative or research workflows. More complex to debug and monitor.

### Swarm (OpenAI-style)

Lightweight, stateless coordination between specialized agents. Three core components: **agents, handoffs, routines**. A triage agent routes to specialists; explicit handoffs provide clarity and observability. Trades opaque automation for fine-grained control.

---

## Agentic Frameworks

| Framework | Architecture Style | Best For | Learning Curve |
|---|---|---|---|
| **LangGraph** | Graph-based workflow | Complex stateful pipelines, production systems | High |
| **CrewAI** | Role-based (organizational) | Business workflows, rapid prototyping | Low |
| **AutoGen** | Conversational coordination | Chat-driven, flexible deployments | Medium |
| **LlamaIndex Workflows** | Event-driven, async | Research, complex branching logic | Medium |
| **Claude Agent SDK** | Computer-use, built-in tools | Code agents, file management, research | Medium |
| **OpenAI Agents SDK** | Handoff-based swarm | Customer support, triage routing | Medium |

### LangGraph

Graph-based design treating agent interactions as nodes in a directed graph. Exceptional flexibility for complex decision-making pipelines — conditional logic, branching, dynamic adaptation. Better for production systems needing fault tolerance and detailed state management.

### CrewAI

Role-based model inspired by real-world organizational structures. Intuitive responsibility abstraction, excellent for task-oriented collaboration, fastest to prototype.

### AutoGen (Microsoft)

Conversational agent coordination emphasizing natural language interactions. Dynamic role-playing, context adaptation, minimal coding required.

### LlamaIndex Workflows

Event-driven, step-based with async execution. Steps triggered by events emit further events. Supports complex flows with loops, branching, and parallel paths. Fully open source.

### Claude Agent SDK

Gives agents a computer. Provides built-in tools (Read, Write, Edit, Bash, Glob, Grep, WebSearch, WebFetch), lifecycle hooks (PreToolUse, PostToolUse, SessionStart), subagent spawning, MCP integration, and session management.

---

## Context Engineering

Context engineering goes beyond prompt engineering: it is the holistic curation of *all* information entering the model's attention budget.

**Information budget includes:** system prompts, tool definitions, retrieved documents, message history, tool outputs.

### Core Optimization Techniques

**Retrieval-Augmented Generation (RAG):** Retrieve only the most relevant information at query time rather than stuffing entire documents into the prompt.

**Prompt Compression:** Remove redundant information, compress repetitive patterns, eliminate unnecessary formatting. Can reduce prompt length 20–40% while improving results.

**Context Reordering:** Restructure information flow based on importance. Move critical content first — models suffer from "lost-in-the-middle" attention degradation.

**Semantic Chunking:** Chunk documents by meaning rather than arbitrary length for better retrieval alignment.

**Conversation Summarization:** Summarize conversation history to preserve key information while reducing token count across long sessions.

**Prompt Caching:** Cache system instructions or knowledge bases. Subsequent calls reference the cache rather than reprocessing — reduces input costs ~90% and latency ~75%.

---

## Reliability & Error Handling

### Hallucination Mitigation

Agents can hallucinate in several ways: fabricating tool outputs, inventing function calls, generating false reasoning. Mitigation strategies:

- **Chain-of-Thought prompting** significantly reduces hallucination rates
- **Self-Verification:** Agents revisit and critique their own outputs
- **Self-Consistency:** Generate multiple candidates, select via majority vote or confidence weighting
- **RAG with reasoning enhancement:** Ground responses in retrieved, verifiable sources
- **Confidence estimation:** Prompt agents to assess their own uncertainty explicitly

### Error Handling Architecture

Every production agent must implement:

1. **Validation gates** before every tool call — never execute blindly
2. **Three-option rule:** reject, fix, or escalate — no silent failures
3. **Action-level logging** with reason codes for every tool invocation
4. **Retry logic** with exponential backoff and a hard retry limit
5. **Stall detection:** If the same tool is called 3+ times without progress, escalate or terminate

### Infinite Loop Prevention

Root causes of agent loops: missing termination conditions, tools returning empty results, circular reasoning. Prevention:

- Set maximum iteration limits (typically 50–100 steps)
- Implement loop fingerprinting: track tool call patterns
- Hard budget caps per task (token and time limits)
- Escalation path to human review when loop detected

---

## Observability & Monitoring

### Four Pillars of Agent Observability

**1. Traces** — Complete story of a user's request. Parent-child hierarchy of events connecting every model interaction, data retrieval step, and final response.

**2. Tool Monitoring** — Agents are only as fast as their slowest tool. Track tool latency as a distinct metric alongside success/failure rates.

**3. Decision Steps** — Why the agent chose a particular action. Which tool was selected and why. Reasoning trace must be visible for effective debugging.

**4. Failure Analysis** — When and why agents fail. Root cause analysis. Failure pattern identification across sessions.

### Standards and Tools

- **OpenTelemetry (OTEL):** Industry-converging standard for agent telemetry. Prevents vendor lock-in.
- **Langfuse:** Purpose-built AI agent observability
- **Arize:** Agent observability and evaluation platform
- **Datadog / AWS CloudWatch:** Full observability integration

### Key Metrics to Track

- Token usage (input/output per step and per session)
- Tool interaction latency and success rates
- Agent decision paths and branching frequency
- Error rates by type and tool
- End-to-end task completion rate

---

## Human-in-the-Loop

Human-in-the-Loop (HITL) places humans at strategic decision points within the agent's execution cycle — the agent proposes, a human reviews, corrects, or approves before irreversible action.

### When HITL Adds Most Value

- High-stakes decisions (loan approvals, medical, legal)
- Ambiguous situations requiring human judgment
- Regulatory compliance requirements (SOC 2, HIPAA, GDPR)
- Content moderation of sensitive material
- Any irreversible action (deletions, financial transfers, external sends)

### Implementation Patterns

**HumanTool Pattern:** Agent calls a `HumanTool` for guidance at any point — keeps humans as decision-makers or fallback experts within multi-agent workflows.

**User Confirmation:** Simple approve/edit/reject for specific actions.

**Strategic Checkpoints:** Pre-determined pause points in the workflow. Humans review before irreversible stages.

---

## Evaluation & Testing

### Three Evaluation Approaches

**1. Final Response Evaluation** — Measures end result quality. Metrics: task completion rate, accuracy, coherence, relevance, groundedness.

**2. Trajectory Evaluation** — Focuses on internal decision-making. Metrics: tool selection accuracy, reasoning quality, execution efficiency, steps to completion.

**3. Robustness Testing** — Evaluates reliability under adverse conditions. Metrics: error handling, prompt injection resistance, bias assessment.

### Evaluation Methods

- **Deterministic rules:** For factual correctness and format compliance
- **LLM-as-a-Judge:** A more capable model evaluates agent outputs against quality criteria
- **Human judgment:** For nuance and subjective quality assessment
- **A/B testing:** Comparative evaluation of agent variants

### Scaling Evaluation

Start simple, then expand:
1. Task success rates on a golden test dataset
2. After consistent success: add tool usage metrics
3. Next: evaluate reasoning trajectory quality
4. Finally: optimize cost-performance ratio once quality is stable

---

## Cost & Latency Management

### Latency vs. Accuracy Trade-offs

| Architecture | Typical Latency | Accuracy |
|---|---|---|
| Single LLM call | ~800 ms | Baseline |
| ReAct agent (3–5 steps) | 3–8 s | Good |
| Orchestrator-Worker | 10–30 s | Better |
| With Reflexion loop | 30–120 s | Best |

### Cost Control Measures

- **Token budget caps** per task and per session
- **Iteration limits** (max 50–100 loop steps)
- **Stall detection** terminates unproductive loops early
- **Prompt caching** reduces repeated input costs by ~90%
- **Model routing** — use smaller models for simple subtasks, reserve large models for reasoning-heavy steps

### Real Cost Benchmark

An unconstrained agent on a software engineering benchmark: **$5–8 per task**. With proper iteration limits and caching: minimal cost overhead. Context optimization alone can reduce costs 30–60% in production systems.

---

## Best Practices Summary

### Design Principles

1. **Simplicity first** — Start with the simplest solution; add complexity only when proven necessary
2. **Modularity** — Build reusable composable patterns: chaining, routing, parallelization
3. **Transparency** — Show agent reasoning clearly for understanding and debugging
4. **Tool excellence** — Invest heavily in tool definitions, documentation, and testing
5. **Measure everything** — Test thoroughly before deployment; evaluate rigorously in production

### Development Workflow

1. **Start simple:** Basic LLM API calls and prompts
2. **Identify patterns:** Which composable pattern fits your use case?
3. **Select framework:** Only if complexity justifies it (prefer no-framework for simple cases)
4. **Engineer tools:** Spend significant effort on tool definitions and documentation
5. **Test extensively:** Build golden test datasets and use LLM-as-a-Judge
6. **Add observability:** Implement tracing before going to production
7. **Monitor and iterate:** Track failure patterns and optimize continuously

### Common Anti-Patterns to Avoid

- **Over-engineering:** Using heavy frameworks when simple patterns suffice
- **Poor tool documentation:** Incomplete descriptions confuse the agent's tool selection
- **No loop controls:** Infinite loops leading to runaway costs
- **Moving to production without evaluation:** Skipping golden dataset testing
- **Missing observability:** Unable to debug failures in production
- **Context window waste:** Unoptimized prompts burning unnecessary tokens
- **Silent failures:** Tools failing without escalation or logging

---

## References

### Anthropic

- [Building Effective Agents](https://www.anthropic.com/research/building-effective-agents)
- [Effective Context Engineering for AI Agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
- [Demystifying Evals for AI Agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)
- [Claude Agent SDK Overview](https://platform.claude.com/docs/en/agent-sdk/overview)

### Academic Papers

- [Agentic Artificial Intelligence: Architectures, Taxonomies, and Evaluation](https://arxiv.org/html/2601.12560v1)
- [Reflexion: Language Agents with Verbal Reinforcement Learning](https://arxiv.org/abs/2303.11366)
- [Fundamentals of Building Autonomous LLM Agents](https://arxiv.org/html/2510.09244v1)
- [AI Agentic Programming: A Survey of Techniques, Challenges, and Opportunities](https://arxiv.org/html/2508.11126v2)
- [AI Agents: Evolution, Architecture, and Real-World Applications](https://arxiv.org/html/2503.12687v1)
- [LLM-based Agents Suffer from Hallucinations Too](https://arxiv.org/html/2509.18970v1)

### Frameworks and Tools

- [LangGraph Documentation](https://docs.langchain.com)
- [CrewAI Documentation](https://docs.crewai.com)
- [AutoGen (Microsoft)](https://microsoft.github.io/autogen/)
- [LlamaIndex Workflows 1.0](https://www.llamaindex.ai/blog/announcing-workflows-1-0-a-lightweight-framework-for-agentic-systems)
- [OpenAI Swarm](https://github.com/openai/swarm)

### Research Blogs

- [Anthropic Research](https://www.anthropic.com/research)
- [Google DeepMind Blog](https://research.google/blog/)
- [Towards a Science of Scaling Agent Systems](https://research.google/blog/towards-a-science-of-scaling-agent-systems-when-and-why-agent-systems-work/)
- [Prompt Engineering Guide](https://www.promptingguide.ai)
- [Tree of Thoughts Guide](https://www.promptingguide.ai/techniques/tot)

### Observability

- [AI Agent Observability with Langfuse](https://langfuse.com/blog/2024-07-ai-agent-observability-with-langfuse)
- [Agent Observability Best Practices - Azure](https://azure.microsoft.com/en-us/blog/agent-factory-top-5-agent-observability-best-practices-for-reliable-ai/)

### Related Notes

- [[Agent_Engineering/diagrams/agent-core-loop.drawio.svg]]
- [[Agent_Engineering/diagrams/agent-architecture-patterns.drawio.svg]]
- [[Agent_Engineering/diagrams/agent-memory-systems.drawio.svg]]
- [[Agent_Engineering/diagrams/agent-orchestration-patterns.drawio.svg]]
