## AI Assisted Development Architecture


![AI Development Architecture](diagrams/AI-Development-Architecture.svg)

---

## Architecture Layers

### Integration Layer

The Integration Layer acts as the entry point into the system. It sits on the left side of the top row and is responsible for connecting external systems, APIs, data sources, and services into the agent architecture. It abstracts the complexity of external communication and feeds inputs into the Control Layer for orchestration.

---
### Control Layer

The Control Layer is the orchestration hub of the architecture. It decides *what* needs to happen and *in what order*, routing work to the appropriate agents and tools. It has two subsections:
#### Coordination

Coordination handles the planning and dispatching logic. It determines how tasks are broken down and assigned across multiple agents simultaneously. **[Superset.sh](https://superset.sh/)** is the key tool here — it is a dedicated IDE for orchestrating multiple AI coding agents running in parallel on your machine. Its core innovation is using **git worktrees** to give each agent its own isolated copy of the repository, preventing file conflicts and context bleed between agents. With Superset, you can assign different tasks to different agents at the same time each on its own branch, while Superset provides a unified interface to review and integrate their work.
#### Agents

The Agents sub-section hosts the active AI agents that execute reasoning and task fulfillment. Each agent operates within the context provided by the Context Layer and uses the tools exposed by the Execution Layer to carry out its assigned responsibilities.

---
### Execution Layer

The Execution Layer is where actions are physically performed. Agents in the Control Layer delegate concrete operations here. It exposes a **Tools** section containing four types of capabilities:

- **File I/O** — reading and writing files on the local filesystem, enabling agents to persist results, load documents, or process data files.
- **Scripts** — executing shell or scripting language programs, allowing agents to run automation scripts or pipeline steps.
- **MCPs** (Model Context Protocol servers) — integrations with external services exposed as structured tool endpoints that agents can call to interact with third-party systems.
- **CLIs** — invoking command-line interfaces of installed tools, enabling agents to use any system utility or developer tool.

---
### Context Layer

The Context Layer forms the foundation of the entire architecture, spanning across the full bottom of the diagram. It supplies agents with the structured knowledge, behavioral patterns, and persisted artifacts they need to operate intelligently. It is organized into three sub-sections:
#### Frameworks

Frameworks define *how* agents are instructed to behave. They provide the behavioral and structural templates that shape agent execution:

- **Commands / Workflows** — declarative descriptions of what to do and how, outlining the sequence of steps an agent should follow for a given task.
- **Skills** — focused instruction sets for specific tasks, each piece defining the steps, tools, and logic required to accomplish a particular type of work.
- **Agents / Personas / Gems** — predefined agent configurations, personalities, or specialized roles that can be activated to handle particular domains or responsibilities.
#### Artifacts

Artifacts are the persistent outputs and inputs that agents produce and consume over time:

- **Specs** — requirement documents, design specifications, or task definitions that agents reference when planning work.
- **Memory** — stored records of prior interactions, decisions, or session state that agents use to maintain continuity across tasks.
- **Docs** — documentation files that serve as reference material or deliverables produced by agents.
- **Code** — source code files generated, reviewed, or modified by agents during software engineering tasks.
#### Knowledge

Knowledge provides the structured information repositories that agents query to reason over domain-specific content:

- **KB** (Knowledge Base) — a curated store of facts, domain knowledge, or reference information that agents retrieve to ground their responses.
- **Code Indexing** — a searchable index of codebases that enables agents to look up functions, files, and patterns when working on software tasks.