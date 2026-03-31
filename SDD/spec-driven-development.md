---
tags:
  - software-engineering
  - spec-driven-development
  - SDD
  - TDD
  - BDD
  - AI-engineering
  - methodology
  - agent-engineering
  - best-practices
  - testing
related:
  - "[[AI-Agent-Harness-Engineering]]"
  - "[[AI-Agent-Architectures]]"
  - "[[agent-compound-engineering]]"
---

# Spec-Driven Development (SDD)

> *"The specification becomes the authoritative definition of system reality. Implementations are continuously derived, validated, and when necessary, regenerated to conform to that truth."*

## What Is Spec-Driven Development?

Spec-Driven Development (SDD) is a software engineering methodology in which a **formal, machine-readable specification serves as the authoritative source of truth** and the primary artifact from which implementation, testing, and documentation are derived.

Unlike traditional approaches where code is written first and documentation follows, SDD mandates that system intent be explicitly defined **before implementation begins**. In the age of AI-assisted engineering, SDD provides a structured framework that prevents inconsistencies from ad-hoc "vibe coding" approaches. Rather than using AI models as autocomplete tools with vague prompts, SDD treats specifications as contracts that AI agents must satisfy.

The central axiom of SDD:

- **The spec is the primary artifact.** Code is merely its expression in a particular language and framework.
- **Maintaining software means evolving specifications.** When you change the spec, the code follows.
- **Debug the spec, not the code.** Root causes live in intent, not in generated output.

### Historical Context

| Era | Development |
|-----|-------------|
| 1960s | NASA workflows emphasizing logic verification before coding |
| 2004 | Academic formalization combining TDD and Design by Contract |
| 2010s | OpenAPI / Swagger popularizes spec-first for REST APIs |
| 2020s | LLM-powered agentic workflows trigger a full SDD renaissance |

---

## Core Principles

### 1. Specification as Source of Truth

The specification must be the **single, canonical definition** of system behavior. All other artifacts — code, tests, documentation, SDKs — are derived from it. This eliminates ambiguity between what was intended and what was implemented.

### 2. Executable Specifications

Specifications must be precise, complete, and unambiguous enough to generate working systems and to fail automatically when implementation diverges. This is the key distinction from traditional requirements documents: **SDD specs are executable**, not passive.

### 3. Explicit Intent Before Implementation

Before a single line of code is written, define what the system should do in full detail: requirements, user flows, behaviors, edge cases, and constraints. That document becomes the north star for all subsequent work — design, implementation, testing, and documentation.

### 4. Architectural Governance (Constitution)

A **constitution document** establishes non-negotiable architectural principles for a project. These are immutable constraints that AI agents and developers alike must respect: security policies, data handling rules, service boundaries, compliance requirements.

### 5. Continuous Refinement, Not Big-Bang Planning

SDD is not waterfall. Unlike waterfall's lengthy feedback cycles, SDD enables short iteration loops by combining rigorous engineering practices with AI's rapid prototyping capabilities. Specifications are **living documents** that evolve alongside the system.

### 6. Spec-as-Source Hierarchy

SDD can be adopted at different maturity levels:

| Level | Description |
|-------|-------------|
| **Spec-Aware** | Teams use structured specs as primary reference; still code manually |
| **Spec-First** | Specs guide and constrain AI output; code remains the deliverable |
| **Spec-Anchored** | Specs persist post-completion for ongoing feature evolution; governance layers added |
| **Spec-as-Source** | Humans edit specs only; AI generates all code (most advanced) |

### 7. Domain-Oriented Language

Specifications use **business intent language**, not technology-specific implementations. They describe what the system must do from a user and domain perspective, making them accessible to non-engineers and reducing translation loss between stakeholders and developers.

---

## The Four-Phase Lifecycle

![SDD Four-Phase Lifecycle](diagrams/sdd-lifecycle.drawio.svg)

### Phase 1: Specify — Define Intent

The specification phase captures everything about what the system must do, in human-readable and machine-parseable formats:

- User stories ("As a..., I want..., so that...")
- Acceptance criteria using Given/When/Then patterns
- Business rules and domain invariants
- Edge cases and error conditions
- Non-functional requirements (performance, security, compliance)
- Success metrics

**Key principle:** Focus on *what* and *why*, never *how*.

### Phase 2: Plan — Technical Architecture

Translate the specification into a technical architecture:

- System design and service boundaries
- API contracts (OpenAPI, AsyncAPI, GraphQL SDL)
- Data models and database schemas
- Technology stack choices
- Security policies and authentication flows
- Compliance requirements (GDPR, SOC 2, etc.)
- Infrastructure decisions

### Phase 3: Task — Decomposition

Break the plan into atomic, testable work units:

- Each task has a clear Definition of Done
- Dependencies are explicitly mapped
- Tasks are scoped to minimize risk
- Each task maps back to specific spec requirements
- Acceptance tests are defined per task

### Phase 4: Implement — AI-Assisted Execution

AI agents generate code within specification constraints:

- Code generation guided by spec artifacts
- Tests auto-generated from acceptance criteria
- Continuous spec validation during CI/CD
- Drift detection triggers regeneration
- Debug by fixing the spec, not the output

---

## The Five-Layer Execution Model

![SDD Five-Layer Execution Model](diagrams/sdd-five-layer-architecture.drawio.svg)

The architecture of a fully realized SDD system operates across five distinct layers:

**Layer 1 — Specification Layer:** Declarative system intent — what must be true. Technologies: OpenAPI, AsyncAPI, GraphQL SDL, Markdown specs, acceptance criteria.

**Layer 2 — Generation Layer:** Transforms specifications into executable forms. Technologies: AI agents, code generators, template engines, multi-language scaffolding.

**Layer 3 — Artifact Layer:** Generated, disposable code and models — regenerable on demand. Includes source code, tests, documentation, SDK clients, database migrations.

**Layer 4 — Validation Layer:** Continuous enforcement through contract testing and drift detection. Technologies: Spectral, Pact, CI gates, architecture fitness functions.

**Layer 5 — Runtime Layer:** Operational systems constrained by upstream specifications. Includes deployed services, APIs, monitoring, SLA enforcement.

A feedback loop runs from the Runtime layer back to the Specification layer via drift reports and operational observations — enabling continuous spec evolution.

---

## How SDD Integrates with TDD, BDD, and Other Practices

![SDD, TDD, and BDD Integration Model](diagrams/sdd-tdd-bdd-integration.drawio.svg)

A critical insight: **SDD, TDD, and BDD are not competing methodologies.** They operate at different abstraction layers and are designed to be used together.

### The Complementary Relationship

| Methodology | Layer | Focus | Answers |
|-------------|-------|-------|---------|
| **SDD** | Architectural | System intent, contracts, invariants | *What* and *Why* |
| **BDD** | Behavioral | User-facing scenarios, system behavior | *How it behaves* |
| **TDD** | Implementation | Unit correctness, code design | *How it works* |

SDD sits at the top: it provides the architectural invariants and business intent. BDD translates those into user-scenario-level tests using ubiquitous language. TDD then verifies correctness at the unit level. Each layer validates a different slice of the system.

### SDD and TDD

TDD is, in a sense, *SDD at the micro-level* — writing a test first is writing a micro-specification. SDD extends this thinking upward:

- SDD uses the specification to **generate** initial tests
- TDD ensures individual units **behave correctly**
- SDD ensures generated code adheres to **architectural constraints and API contracts** across components
- Teams maintain both: TDD for implementation verification, SDD for architectural governance

The ideal flow: **Spec → Auto-generated test stubs → TDD red-green-refactor cycle → CI validation against spec.**

### SDD and BDD

BDD practices remain fully valid within SDD:

- BDD's Given/When/Then scenarios are used as **acceptance criteria format** within SDD specifications
- SDD provides the structural and architectural invariants that BDD scenarios must satisfy
- SDD transforms BDD's living documentation into **automated validation gates**
- BDD's ubiquitous language aligns with SDD's domain-oriented specification language

### SDD and Design by Contract (DbC)

SDD extends Design by Contract concepts (preconditions, postconditions, invariants) from the method level to the **system and API level**. Contracts become first-class artifacts enforced at build time, not just at runtime.

### SDD and Domain-Driven Design (DDD)

SDD's domain-oriented specification language aligns naturally with DDD's bounded contexts and ubiquitous language. Specifications map cleanly onto DDD aggregates and domain events, making them complementary frameworks for complex systems.

### SDD and Contract Testing

Contract testing (e.g., with Pact) is the **runtime enforcement layer** of SDD. Where SDD defines what services should do, contract tests continuously verify that implementations honor those contracts — especially in microservices architectures where service boundaries must be respected.

### SDD and CI/CD

SDD requires robust CI/CD pipelines that treat specification validation as a first-class build gate:

- Spec linting (Spectral) fails builds on invalid specs
- Contract tests run on every commit
- Drift detection alerts when code diverges from spec
- API documentation auto-generated and published from spec

### SDD and "Vibe Coding" / AI-Assisted Development

Research shows that AI-assisted coding increases code complexity by ~41% and static analysis warnings by ~30%. SDD directly counters this by defining constraints upfront that guide generation. It is the antidote to vibe coding — it replaces vague prompting with explicit, verifiable contracts.

---

## Best Practices

### Writing Effective Specifications

**Be explicit about external behavior.** Include input/output mappings, preconditions/postconditions, invariants, constraints, and interface types. Avoid implementation details — focus on observable behavior.

**Use domain language.** Describe business intent, not technology. Prefer "a user can place an order when their cart has at least one item" over "POST /orders returns 201 when request body is valid."

**Structure with Given/When/Then.** This pattern enforces clarity about context, action, and expected outcome. It also maps directly to BDD test cases.

**Define failure modes explicitly.** Specs that only describe happy paths are incomplete. Document all error conditions, edge cases, and constraint violations.

**Keep specs DRY (Don't Repeat Yourself).** Use references and composition to avoid duplication. A spec that contradicts itself is worse than no spec.

**Aim for determinism.** Specifications should produce the same behavior regardless of implementation choices. Reduce ambiguity to reduce LLM hallucinations.

### Team Adoption Strategy

**Low-maturity teams:** Start with GitHub Spec-Kit. Mandate spec review before any AI-assisted code generation. Build the habit of spec-first.

**Intermediate teams:** Add project constitutions for architectural governance. Version your specifications in a dedicated repository. Implement Spectral linting in CI.

**High-maturity teams:** Enable autonomous agent execution within governance boundaries. Implement full spec-as-source for appropriate subsystems. Deploy drift detection in production.

### Avoiding Common Pitfalls

**Spec Drift:** When code evolves without corresponding spec updates, the spec loses its authority. Enforce spec-first discipline through process (code reviews, CI gates) and tooling (drift detection).

**Over-specification:** Specifying implementation details defeats the purpose. Specs should constrain *what*, not dictate *how*.

**Excessive Markdown Burden:** Opinionated SDD workflows (like Spec-Kit) can create more markdown review overhead than code review. Right-size the spec detail to the problem complexity.

**Non-Deterministic Generation:** LLM code generation is inherently non-deterministic. Robust CI/CD with comprehensive contract tests is essential to catch variation.

**Hallucination and Misinterpretation:** Despite detailed specs, AI agents sometimes misinterpret or over-implement requirements. Human review of generated code remains essential.

### When SDD Excels

- API development and microservices (SDD is "the de facto standard for robust, well-documented APIs")
- Large-scale projects requiring a single source of truth across teams
- Enterprise systems with compliance and audit requirements
- Legacy modernization (capture business logic in specs before rebuilding)
- New greenfield projects where upfront spec investment pays maximum dividends

### When SDD Struggles

- Exploratory work and rapid prototyping where requirements are highly uncertain
- Small teams requiring frequent pivots
- Legacy systems requiring extensive reverse-engineering before spec extraction
- Simple, single-developer projects where the overhead exceeds the benefit

---

## Recommended Tools

![SDD Toolchain Ecosystem](diagrams/sdd-toolchain.drawio.svg)

### Specification Formats

| Tool | Purpose |
|------|---------|
| [OpenAPI / Swagger](https://swagger.io/specification/) | REST API specification standard |
| [AsyncAPI](https://www.asyncapi.com/) | Event-driven and messaging API specs |
| [GraphQL SDL](https://graphql.org/learn/schema/) | GraphQL schema definition language |
| [JSON Schema](https://json-schema.org/) | Data structure validation |
| [Protocol Buffers](https://protobuf.dev/) | Language-neutral serialization spec |

### SDD Workflow Tools

| Tool | Description |
|------|-------------|
| [GitHub Spec-Kit](https://github.com/github/spec-kit) | Open-source CLI supporting 22+ AI agents via slash commands. Phases: Specify → Plan → Tasks → Implement |
| [Amazon Kiro](https://kiro.dev/) | Opinionated workflow with Requirements → Design → Tasks phases. Includes "steering" memory (product.md, tech.md, structure.md) |
| [Tessl](https://www.tessl.io/) | Spec-anchored/spec-as-source tool (private beta). Generates code marked `// GENERATED FROM SPEC - DO NOT EDIT` |

### AI Coding Agents

| Tool | Notes |
|------|-------|
| [Claude Code](https://claude.ai/claude-code) | Methodology-neutral; works well with any spec format |
| [GitHub Copilot](https://github.com/features/copilot) | Integrates with Spec-Kit via slash commands |
| [Cursor](https://cursor.com/) | Methodology-neutral; supports custom rule files |
| [Gemini CLI](https://github.com/google-gemini/gemini-cli) | Works with Spec-Kit |

### Validation & Contract Testing

| Tool | Purpose |
|------|---------|
| [Spectral](https://stoplight.io/open-source/spectral) | OpenAPI/AsyncAPI linting and custom rule enforcement |
| [Pact](https://pact.io/) | Consumer-driven contract testing for microservices |
| [Dredd](https://dredd.org/) | API contract testing against live server |
| [Schemathesis](https://schemathesis.readthedocs.io/) | Property-based API testing from OpenAPI specs |
| [Prism](https://stoplight.io/open-source/prism) | API mock server from OpenAPI specs |
| [Cucumber](https://cucumber.io/) | BDD framework using Gherkin (Given/When/Then) |
| [SpecFlow](https://specflow.org/) | BDD for .NET |

### API Design Platforms

| Tool | Purpose |
|------|---------|
| [SwaggerHub](https://swagger.io/tools/swaggerhub/) | Collaborative API design with AI-assisted compliance |
| [Postman](https://www.postman.com/) | API design, testing, and documentation platform |
| [Stoplight](https://stoplight.io/) | API design-first platform with governance |
| [Bump.sh](https://bump.sh/) | API documentation from OpenAPI specs with changelog |

### Code Generation

| Tool | Purpose |
|------|---------|
| [openapi-generator](https://github.com/OpenAPITools/openapi-generator) | Generate clients and servers from OpenAPI |
| [Orval](https://orval.dev/) | Generate TypeScript clients and React Query hooks |
| [NSwag](https://github.com/RicoSuter/NSwag) | .NET/TypeScript client generation |
| [CodeConcise](https://codeconcise.dev/) | Extract legacy codebase structure into specs |

### Context Engineering

| Tool | Purpose |
|------|---------|
| [Context7](https://context7.com/) | Real-time documentation for AI coding contexts |
| AGENTS.md / CLAUDE.md | Project-level instructions for AI agents |
| [MCP Servers](https://modelcontextprotocol.io/) | Extend AI agents with domain-specific tools |
| [Augment Code](https://www.augmentcode.com/) | Multi-repository semantic understanding engine |

---

## SpecOps: Operational Discipline

Just as DevOps operationalized software delivery, **SpecOps** (Specification Operations) operationalizes specification management. It requires five core capabilities:

1. **Version control for specifications** — specs live in git, with full history and branching strategy
2. **Peer review processes** — spec changes require review, just like code changes
3. **Formal validation engines** — automated linting and contract checking on every commit
4. **Multi-language code generation** — specs generate artifacts across the stack
5. **Runtime contract enforcement** — specifications remain active throughout the system lifecycle, not just during development

---

## Security and Compliance Benefits

SDD directly addresses risks in AI-generated code:

- Research indicates ~2% of issues in AI-generated code are security vulnerabilities, with 56-93% rated Blocker or Critical severity
- By embedding security requirements in specifications **before** code generation, SDD prevents vulnerabilities at the source
- Compliance requirements (SOC 2, GDPR, ISO/IEC 42001, EU AI Act) are captured in the spec and enforced automatically
- Audit-ready evidence is generated naturally: the spec proves that security requirements were by-design, not retrofitted

---

## Comparison: SDD vs Other Approaches

| Aspect | Waterfall | TDD | BDD | SDD |
|--------|-----------|-----|-----|-----|
| Starting artifact | Requirements doc | Failing test | Feature scenario | Executable spec |
| Feedback loop | Long (months) | Short (minutes) | Medium (days) | Medium (hours) |
| AI integration | Low | Medium | Medium | Native |
| Drift prevention | Manual | Partial | Partial | Automated |
| Documentation | Separate, stale | None | Living docs | Auto-generated |
| Scale | Enterprise-only | Any | Any | Any |
| Audience | Analysts | Developers | Cross-functional | Cross-functional |

---

## Recommendations

**For individual developers:** Start with the Spec-First maturity level. Before prompting any AI agent to write code, write a short spec covering: what the feature does, its inputs and outputs, edge cases, and acceptance criteria. This alone dramatically improves output quality.

**For small teams:** Adopt GitHub Spec-Kit with a simple constitution document. Integrate Spectral linting in your CI pipeline. Treat spec review as mandatory before implementation begins.

**For enterprise teams:** Invest in Spec-Anchored or Spec-as-Source approaches for stable, high-value subsystems. Maintain a specification repository separate from code. Implement full contract testing with Pact for service boundaries. Use SDD's compliance traceability to support audit requirements.

**For AI-native organizations:** Implement Context Engineering alongside SDD — AGENTS.md files, MCP servers, and curated context packages ensure AI agents have the right information to honor specifications faithfully.

**General principles:**
- Treat specifications as living documents, continuously updated
- Collaborate across roles (product, design, engineering, QA) on specification creation
- Automate documentation and test generation from specs — do not maintain them manually
- Prefer "spec + generate" over "code + document" as your default workflow
- Combine with TDD and BDD — they address complementary concerns, not competing ones

---

## References

- [Spec-Driven Development — Wikipedia](https://en.wikipedia.org/wiki/Spec-driven_development)
- [Understanding Spec-Driven-Development: Kiro, spec-kit, and Tessl — Martin Fowler](https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html)
- [Spec-Driven Development Explained — Thoughtworks](https://www.thoughtworks.com/en-us/insights/blog/agile-engineering-practices/spec-driven-development-unpacking-2025-new-engineering-practices)
- [Spec-Driven Development: When Architecture Becomes Executable — InfoQ](https://www.infoq.com/articles/spec-driven-development/)
- [What Is Spec-Driven Development? A Complete Guide — Augment Code](https://www.augmentcode.com/guides/what-is-spec-driven-development)
- [Spec-Driven Development with AI: Get Started with a New Open Source Toolkit — GitHub Blog](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/)
- [Spec-Driven Development: From Code to Contract in the Age of AI Coding Assistants — arXiv](https://arxiv.org/html/2602.00180v1)
- [Beyond TDD: Why Spec-Driven Development is the Next Step — Kinde](https://www.kinde.com/learn/ai-for-software-engineering/best-practice/beyond-tdd-why-spec-driven-development-is-the-next-step/)
- [spec-driven.md — GitHub Spec-Kit Repository](https://github.com/github/spec-kit/blob/main/spec-driven.md)
- [Spec-Driven Development: Unpacking 2025's Key New Practices — Nitor Infotech](https://www.nitorinfotech.com/blog/spec-driven-development-explained/)
- [TDD vs. BDD vs. SDD — testRigor](https://testrigor.com/blog/what-is-test-driven-development-tdd-vs-bdd-vs-sdd/)
- [Spec Driven Development — revenge of Waterfall or BDD taken to new level? — Gojko Adzic on LinkedIn](https://www.linkedin.com/pulse/spec-driven-development-revenge-waterfall-bdd-taken-gojko-adzic-imquf)
- [Beyond Vibe-Coding: A Practical Guide to Spec-Driven Development — Scalable Path](https://www.scalablepath.com/machine-learning/spec-driven-development-guide)
- [Using spec-driven development with Claude Code — Medium](https://heeki.medium.com/using-spec-driven-development-with-claude-code-4a1ebe5d9f29)
- [Diving Into Spec-Driven Development With GitHub Spec Kit — Microsoft for Developers](https://developer.microsoft.com/blog/spec-driven-development-spec-kit)
