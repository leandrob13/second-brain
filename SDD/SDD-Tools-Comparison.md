---
tags:
  - software-engineering
  - spec-driven-development
  - SDD
  - bmad
  - speckit
  - openspec
  - AI-engineering
  - methodology
  - comparison
  - best-practices
  - agent-engineering
related:
  - "[[spec-driven-development]]"
  - "[[BMAD-Getting-Started]]"
  - "[[SpecKit-Workflow]]"
  - "[[OpenSpec-Workflow]]"
---

# SDD Tools Comparison — BMAD vs SpecKit vs OpenSpec

> A comprehensive comparison of the three leading Spec-Driven Development frameworks for AI-assisted software engineering. Each tool embodies SDD principles differently, targeting different scales, workflows, and team structures.

---

## Tool Profiles at a Glance

| Attribute | BMAD | SpecKit | OpenSpec |
|-----------|------|---------|----------|
| **Full Name** | Breakthrough Method for Agile AI-Driven Development | GitHub Spec-Kit | OpenSpec |
| **Creator** | BMAD Method / Community | GitHub | Fission AI |
| **License** | Open Source | Open Source (MIT) | MIT |
| **Language** | Node.js | Python (uv) | TypeScript (Node.js) |
| **Install** | `npx bmad-method install` | `uv tool install specify-cli` | `npm install -g @fission-ai/openspec` |
| **Init** | Creates `_bmad/` + `_bmad-output/` | `specify init` creates `.specify/` | `openspec init` creates `openspec/` |
| **Agent Support** | 9 built-in specialized agents | 22+ AI agents via slash commands | 20+ AI assistants via slash commands |
| **Docs** | [docs.bmad-method.org](https://docs.bmad-method.org) | [github.com/github/spec-kit](https://github.com/github/spec-kit) | [openspec.dev](https://openspec.dev) |

---

## Philosophy Comparison

| Principle | BMAD | SpecKit | OpenSpec |
|-----------|------|---------|----------|
| **Core metaphor** | Agile team simulation with AI agents | Sequential checkpointed pipeline | Fluid action-based planning layer |
| **Phase model** | 4 phases, phase-gated | 5 phases + 3 optional, phase-gated | No phase gates; dependency-based DAG |
| **Iteration** | Fresh chat per workflow; order matters | Phase-locked; re-running required to go back | Update any artifact anytime |
| **Brownfield** | Not a primary focus; full artifacts per phase | Supported but not first-class; full specs | First-class via delta specs (ADDED/MODIFIED/REMOVED) |
| **Scaling model** | 3 tracks: Quick Flow / BMad / Enterprise | Single linear pipeline for all sizes | Single schema-driven approach; custom schemas |
| **Opinionation** | High (roles, phases, tracks, ceremonies) | High (phases, constitution, checkpoints) | Low (fluid, minimal ceremony, customizable) |

---

## Detailed Pros and Cons

### BMAD Method

**Pros:**

- **Full SDLC coverage** — Only tool that spans from ideation/brainstorming through sprints, retros, and code review. No gaps between "planning" and "building."
- **Specialized agent roles** — 9 named agents (Analyst, PM, Architect, UX Designer, Scrum Master, Developer, QA, Quick Flow Dev, Tech Writer) provide domain-appropriate output. Each agent brings focused expertise to its workflow.
- **Adaptive complexity** — 3 planning tracks (Quick Flow for 1-15 stories, BMad Method for 10-50+, Enterprise for 30+) let you right-size process to project complexity.
- **Implementation readiness gate** — `bmad-check-implementation-readiness` returns PASS/CONCERNS/FAIL before coding begins, catching misalignment early.
- **Built-in agile ceremonies** — Sprint planning, story creation, code review, and retrospectives are first-class workflows, not afterthoughts.
- **Context-aware help** — `bmad-help` inspects project state and recommends the next step automatically after every workflow.
- **UX design integration** — Dedicated UX designer agent produces `ux-spec.md`, which no other tool provides.
- **Documentation and QA agents** — Test automation (`bmad-automate`) and documentation (`bmad-document-project`) available at any phase.

**Cons:**

- **High ceremony overhead** — Fresh chat sessions required per workflow. 34+ workflows to learn, even if many are optional.
- **Phase rigidity** — Workflows build on prior artifacts and order matters. Difficult to go back and revise earlier decisions without re-running workflows.
- **No delta/brownfield concept** — Every artifact is generated fresh. No mechanism for specifying only what changed in an existing system.
- **No parallel change management** — Sprint-based model assumes sequential story execution. No built-in support for juggling multiple independent changes simultaneously.
- **Learning curve** — 9 agents, 34+ workflows, trigger codes, and 3 planning tracks create a steep initial learning curve.
- **Overkill for small changes** — Even the Quick Flow track may be heavier than needed for a single feature or bug fix.

---

### SpecKit (GitHub)

**Pros:**

- **Constitution-first governance** — Mandatory `constitution.md` as the very first phase establishes non-negotiable project standards before anything else. Strong architectural governance from day one.
- **Thorough artifact generation** — Plan phase produces 8+ artifacts (plan, data-model, research, quickstart, API contracts), giving comprehensive technical documentation.
- **Three optional quality gates** — `/speckit.clarify` (resolve ambiguities), `/speckit.analyze` (cross-artifact consistency), `/speckit.checklist` (completion audit) catch issues before implementation.
- **Git branch integration** — Automatically creates feature branches per spec (`001-<name>`), linking specs directly to git workflow.
- **TDD-oriented task structure** — `tasks.md` includes parallel execution markers `[P]`, file path specifications, dependency ordering, and checkpoint validations built in.
- **Backed by GitHub** — Strong institutional support, active development, integration with GitHub Copilot ecosystem.
- **Extensions and Presets** — Extensibility system allows adding new commands and customizing templates.
- **Agent context file** — `CLAUDE.md` generated at repo root provides persistent project context for AI agents.

**Cons:**

- **Phase rigidity** — 5 required sequential phases. Going back to revise an earlier phase requires re-running previous commands. Feels waterfall-like for iterative work.
- **Heavy Markdown burden** — 8+ artifacts per feature can produce more Markdown to review than actual code, especially for smaller changes.
- **Python dependency** — Requires Python/uv ecosystem, which may not align with JavaScript/TypeScript projects.
- **No parallel change management** — One feature per branch. No built-in system for managing multiple concurrent changes or resolving spec conflicts.
- **No archive/audit trail** — Specs persist in `.specify/specs/NNN-<name>/` but there is no explicit archive workflow, no date-stamped history, no formal completion ceremony.
- **No delta spec model** — Full specs per feature. On brownfield projects, you must re-specify the full context rather than just what changed.
- **No built-in exploration** — No equivalent of OpenSpec's `/opsx:explore` for investigating problem spaces before committing to a spec.

---

### OpenSpec

**Pros:**

- **Fluid, non-linear workflow** — No phase gates. Actions (propose, apply, archive) can be taken in any order. Dependencies are enablers, not barriers. Matches how work actually happens.
- **Delta specs for brownfield** — ADDED/MODIFIED/REMOVED sections let you specify only what's changing, not restate the entire system. Key innovation for existing codebases.
- **Lightweight and fast** — 4 core artifacts (proposal, specs, design, tasks). `npm install` + `openspec init` and you're working in seconds.
- **Native parallel change support** — Multiple changes can coexist in `changes/`. `bulk-archive` resolves spec conflicts automatically. Context-switch freely between changes.
- **Archive with audit trail** — Changes move to `changes/archive/YYYY-MM-DD-<name>/` preserving full context (proposal, design, specs, tasks) for history.
- **Custom schemas** — Define your own artifact types and dependencies via YAML. Fork built-in schemas. Create team-specific workflows.
- **Verify command** — Three-dimensional validation (completeness, correctness, coherence) checks implementation against specs before archiving.
- **Explore mode** — `/opsx:explore` lets you investigate ideas without creating any artifacts. Think first, commit later.
- **Universal tool support** — Works with 20+ AI tools. No IDE lock-in. No model lock-in.
- **Two workflow profiles** — Core (3 commands for simplicity) and Expanded (11 commands for control) let you choose your level of structure.

**Cons:**

- **No full SDLC coverage** — Covers planning and implementation only. No brainstorming, market research, UX design, sprint ceremonies, or retrospectives.
- **No specialized agent roles** — Treats all AI assistants generically. No PM, Architect, or QA agent specialization. Output quality depends on the model and prompt, not role-based guidance.
- **No implementation readiness gate** — No formal PASS/CONCERNS/FAIL check before coding begins (though `/opsx:verify` covers post-implementation).
- **No constitution concept** — `config.yaml` with context and rules is lighter than a full constitution document. Less governance for compliance-heavy projects.
- **No git branch integration** — Specs live in `openspec/` directory. No automatic feature branch creation or branch-scoped spec management.
- **No TDD structure in tasks** — `tasks.md` uses simple checkboxes. No parallel markers, file path specifications, or checkpoint validations built into the task format.
- **Relatively new** — While popular (~38.6k stars), the project is younger than SpecKit. Some features (workspaces, multi-repo) are still coming.

---

## Dimension-by-Dimension Comparison

### Workflow Model

| Dimension | BMAD | SpecKit | OpenSpec |
|-----------|------|---------|----------|
| **Phases** | 4 (Explore > Planning > Solutioning > Implementation) | 5 + 3 optional (Constitution > Specify > Plan > Tasks > Implement) | No phases; fluid actions with dependency DAG |
| **Commands** | 34+ workflows via slash commands or trigger codes | 8 commands (5 core + 3 optional) | 4 core / 11 expanded commands |
| **Phase gating** | Yes, order matters | Yes, strictly sequential | No; dependencies are enablers |
| **Exploration** | Phase 1 (brainstorming, research) | None built-in | `/opsx:explore` |
| **Fast path** | Quick Flow track (skips phases 2-3) | None; all 5 phases required | `/opsx:propose` (creates all artifacts at once) |

### Artifacts

| Artifact Type | BMAD | SpecKit | OpenSpec |
|---------------|------|---------|----------|
| **Product brief / PRD** | `product-brief.md`, `PRD.md` | Not a distinct artifact | `proposal.md` (intent, scope, approach) |
| **Specification** | Part of PRD | `spec.md` (full feature spec) | `specs/` (delta specs with ADDED/MODIFIED/REMOVED) |
| **Architecture** | `architecture.md` + ADRs | Part of `plan.md` | `design.md` (technical approach) |
| **Tech research** | Via `bmad-research` workflows | `research.md` | Not a distinct artifact |
| **Data model** | Part of architecture | `data-model.md` | Part of design |
| **API contracts** | Part of architecture | `contracts/api-spec.json`, `*-spec.md` | Part of design |
| **Task breakdown** | Epic + story files | `tasks.md` (with `[P]` markers, file paths) | `tasks.md` (checkboxed list) |
| **UX design** | `ux-spec.md` | Not a distinct artifact | Not a distinct artifact |
| **Project context** | `project-context.md` | `constitution.md` + `CLAUDE.md` | `config.yaml` (context + rules) |
| **Sprint tracking** | `sprint-status.yaml` | Not applicable | Not applicable |

### Governance & Quality

| Dimension | BMAD | SpecKit | OpenSpec |
|-----------|------|---------|----------|
| **Architectural governance** | `project-context.md` as constitution | `constitution.md` (mandatory first phase) | `config.yaml` context + rules (optional) |
| **Pre-implementation gate** | `bmad-check-implementation-readiness` (PASS/CONCERNS/FAIL) | `/speckit.checklist` (optional) | None (verify is post-implementation) |
| **Post-implementation validation** | `bmad-code-review` | None built-in | `/opsx:verify` (completeness, correctness, coherence) |
| **Ambiguity resolution** | Agent-guided per workflow | `/speckit.clarify` (optional) | Part of `/opsx:explore` or `/opsx:continue` |
| **Cross-artifact consistency** | `bmad-check-implementation-readiness` | `/speckit.analyze` (optional) | `/opsx:verify` |
| **Spec drift prevention** | Manual (fresh chats) | Phase checkpoints | Archive merges deltas into source of truth |

### Customization

| Dimension | BMAD | SpecKit | OpenSpec |
|-----------|------|---------|----------|
| **Workflow customization** | Custom agents via `.md`/`.yaml` files | Extensions (add commands) + Presets (customize templates) | Custom schemas via YAML + template overrides |
| **Template system** | Agent persona files | Template files in `.specify/templates/` | Templates in `schemas/<name>/templates/` |
| **Extensibility model** | Add agents to `_bmad/agents/` | `specify extension add` / `specify preset add` | `openspec schema init` / `openspec schema fork` |
| **Per-project config** | Agent configuration in `_bmad/` | `.specify/` structure | `openspec/config.yaml` with context + per-artifact rules |

---

## Use Cases — When to Choose Each Tool

### Choose BMAD When...

- **Building a full product from scratch** — ideation, market research, PRD, architecture, UX design, stories, sprints, retros. BMAD covers the entire lifecycle.
- **Teams want role-based AI guidance** — having a "PM agent" write the PRD and an "Architect agent" design the system produces more focused, role-appropriate output than a generic prompt.
- **Enterprise or complex multi-tenant systems** — the Enterprise track with 30+ stories, security considerations, and DevOps workflows provides the structure needed.
- **You value agile ceremonies** — sprint planning, story creation, code review, and retrospectives are built in, not bolted on.
- **Project complexity is high and needs adaptive planning** — choosing between Quick Flow (simple), BMad Method (medium), and Enterprise (complex) right-sizes the process.

### Choose SpecKit When...

- **Constitution-driven governance is critical** — if your project needs mandatory architectural principles established before any feature work, SpecKit's constitution-first approach is the strongest.
- **Thorough technical documentation is required** — the Plan phase produces data models, API contracts, research notes, and quickstart guides automatically.
- **Compliance and audit readiness matter** — the sequential, checkpointed workflow with optional Clarify/Analyze/Checklist gates creates a documented decision trail.
- **You want git-integrated spec management** — automatic feature branching ties specs directly to your version control workflow.
- **Full-stack greenfield builds** — when starting from zero, SpecKit's comprehensive artifact generation gives maximum scaffolding.
- **Teams already in the GitHub/Copilot ecosystem** — institutional backing and tight Copilot integration make adoption smooth.

### Choose OpenSpec When...

- **Brownfield development dominates your work** — delta specs (ADDED/MODIFIED/REMOVED) are purpose-built for modifying existing systems without restating everything.
- **You need lightweight, minimal-ceremony planning** — `npm install`, `openspec init`, `/opsx:propose`, and you're working. No phases to learn, no gates to pass through.
- **You juggle multiple features simultaneously** — native parallel change support with bulk-archive and automatic conflict detection is unique to OpenSpec.
- **Iteration speed matters more than thoroughness** — update any artifact anytime. No phase locks. Discover something wrong during implementation? Fix the design and continue.
- **Tool independence is important** — works with 20+ tools, no IDE lock-in, no model lock-in, no API keys, no MCP required.
- **You want exploration before commitment** — `/opsx:explore` lets you investigate and think without creating artifacts or committing to a change.
- **Custom workflows are needed** — YAML-driven schemas let you define exactly the artifacts and dependencies your team needs.

---

## How They Could Be Combined

These tools are not mutually exclusive. Each excels at a different part of the development lifecycle, and combining them creates a more complete system than any single tool provides.

### Combination 1: BMAD for Ideation + OpenSpec for Feature Delivery

Use BMAD's Phase 1-3 (Explore, Planning, Solutioning) to produce the PRD, architecture, and project context. Then switch to OpenSpec for ongoing feature-level changes against the established codebase.

```
BMAD Phase 1-3                    OpenSpec ongoing
─────────────────────────────     ─────────────────────────────
bmad-brainstorming                /opsx:propose add-dark-mode
bmad-create-prd                   /opsx:apply
bmad-create-architecture          /opsx:archive
bmad-create-epics-and-stories     /opsx:propose fix-auth-bug
         │                        /opsx:apply
         ▼                        /opsx:archive
  Initial codebase built          ...iterative brownfield work
```

**Why:** BMAD excels at the "0 to 1" phase with specialized agents. OpenSpec excels at the "1 to N" phase with lightweight, delta-based iterations.

### Combination 2: BMAD for Governance + OpenSpec for Feature Work

Use BMAD's planning agents to establish project governance and context, then use OpenSpec for ongoing feature-level iteration. BMAD's `project-context.md` (generated by the Analyst agent) acts as the constitution, and its contents are injected into OpenSpec's `config.yaml` so every change respects the established principles.

```
BMAD (once)                       OpenSpec (ongoing)
─────────────────────────────     ─────────────────────────────
bmad-analyst                      openspec/config.yaml:
  bmad-generate-project-context     context: |
  → project-context.md                [paste project-context principles]
                                    rules:
bmad-architect                        specs:
  bmad-create-architecture              - Follow architecture.md decisions
  → architecture.md                   design:
                                        - Reference ADRs for trade-offs
                                  /opsx:propose
                                  /opsx:apply
                                  /opsx:archive
```

**Why:** BMAD's specialized agents (Analyst, Architect) produce richer governance artifacts than writing a config file by hand. OpenSpec's fluid iteration model is better for ongoing feature work. Both use Node.js, so there's no extra runtime dependency.

### Combination 3: BMAD for Product + SpecKit for Contracts + OpenSpec for Iteration

A full-stack approach using each tool where it excels:

| Phase | Tool | Why |
|-------|------|-----|
| **Ideation & Research** | BMAD | Analyst agent, brainstorming, market research |
| **Product Definition** | BMAD | PM agent creates PRD, UX designer creates UX spec |
| **Architecture** | BMAD | Architect agent with ADRs, implementation readiness gate |
| **API Contracts** | SpecKit | Plan phase generates data-model, API contracts, research |
| **Ongoing Features** | OpenSpec | Delta specs, parallel changes, lightweight iteration |
| **Sprint Ceremonies** | BMAD | Sprint planning, story creation, code review, retros |

**Why:** Each tool covers a different gap. No single tool does ideation AND contract generation AND brownfield iteration well.

### Combination 4: Shared Spec Repository

All three tools store specs as Markdown files in the repository. A shared `specs/` directory could serve as the single source of truth:

```
project/
├── _bmad-output/               # BMAD planning artifacts (PRD, architecture)
├── .specify/                   # SpecKit governance (constitution, contracts)
├── openspec/
│   ├── specs/                  # Living behavioral specs (OpenSpec source of truth)
│   └── changes/                # Active changes (OpenSpec delta workflow)
└── src/                        # Implementation
```

BMAD produces the initial planning artifacts. SpecKit establishes governance and contracts. OpenSpec manages the ongoing evolution of behavioral specs through delta changes.

---

## Proposed Governance Model

For teams adopting SDD at scale, the following governance model leverages the strengths of each tool:

### 1. Foundation Phase (One-Time)

| Activity | Tool | Output |
|----------|------|--------|
| Establish project principles | SpecKit `/speckit.constitution` | `constitution.md` |
| Research and ideate | BMAD `bmad-brainstorming`, `bmad-research` | Research findings, product brief |
| Define product requirements | BMAD `bmad-create-prd` | `PRD.md` |
| Design architecture | BMAD `bmad-create-architecture` | `architecture.md`, ADRs |
| Generate project context | BMAD `bmad-generate-project-context` | `project-context.md` |
| Initialize spec repo | OpenSpec `openspec init` | `openspec/` directory with `config.yaml` |
| Inject governance context | Manual | Copy constitution principles into `openspec/config.yaml` context |

### 2. Feature Development (Ongoing, Repeating)

| Activity | Tool | Output |
|----------|------|--------|
| Explore unclear requirements | OpenSpec `/opsx:explore` | Clarified understanding (no artifacts) |
| Propose a feature change | OpenSpec `/opsx:propose` | proposal.md, specs/ (deltas), design.md, tasks.md |
| Review spec delta | Team (PR review) | Approved or revised delta specs |
| Implement tasks | OpenSpec `/opsx:apply` | Working code |
| Validate implementation | OpenSpec `/opsx:verify` | Completeness/correctness/coherence report |
| Archive completed change | OpenSpec `/opsx:archive` | Specs merged, change archived with audit trail |

### 3. Sprint Ceremonies (Periodic)

| Activity | Tool | Output |
|----------|------|--------|
| Sprint planning | BMAD `bmad-sprint-planning` | `sprint-status.yaml` |
| Story creation from epics | BMAD `bmad-create-story` | Story files |
| Code review | BMAD `bmad-code-review` | Review report |
| Retrospective | BMAD `bmad-retrospective` | Retrospective notes |

### 4. Quality Gates (As Needed)

| Activity | Tool | Output |
|----------|------|--------|
| Pre-implementation readiness | BMAD `bmad-check-implementation-readiness` | PASS/CONCERNS/FAIL |
| Cross-artifact consistency | SpecKit `/speckit.analyze` | Coverage report |
| Completion audit | SpecKit `/speckit.checklist` | Quality checklist |
| Post-implementation validation | OpenSpec `/opsx:verify` | Validation report |

### 5. Governance Rules

- **Spec changes require PR review** — treat spec deltas with the same rigor as code changes.
- **Constitution is immutable without team consensus** — changes to `constitution.md` require explicit team approval.
- **Archive before merging code** — OpenSpec archive ensures specs and code stay in sync.
- **Readiness gates for high-risk changes** — use BMAD's implementation readiness check for architectural changes; SpecKit's analyze/checklist for compliance-critical features.
- **Context hygiene** — fresh chat sessions for BMAD workflows; clean context windows for OpenSpec implementation.
- **Specs live in the repository** — all specification artifacts are version-controlled alongside code. No external systems.

---

## Summary Decision Matrix

| Scenario | Recommended Tool(s) |
|----------|---------------------|
| Greenfield product, 0 to 1 | BMAD (full lifecycle) or SpecKit (thorough scaffolding) |
| Brownfield feature iteration | OpenSpec |
| Quick bug fix or small feature | OpenSpec (core profile: propose/apply/archive) |
| Enterprise compliance project | SpecKit (constitution + checkpoints) + BMAD (readiness gates) |
| Solo developer, rapid iteration | OpenSpec |
| Team with agile ceremonies | BMAD |
| Multi-feature parallel development | OpenSpec |
| API-first development | SpecKit (contract generation) |
| Exploratory / unclear requirements | OpenSpec (`/opsx:explore`) or BMAD (Phase 1 research) |
| Full team governance at scale | All three combined (see Proposed Governance Model) |

---

## Related Notes

- [Spec-Driven Development (SDD)](spec-driven-development.md) — Core SDD methodology, principles, lifecycle, and integration with TDD/BDD
- [BMAD-Getting-Started](bmad/BMAD-Getting-Started.md) — BMAD workflow, phases, planning tracks
- [BMAD-Agents](bmad/BMAD-Agents.md) — All 9 BMAD agents with roles and triggers
- [BMAD-Workflows-Reference](bmad/BMAD-Workflows-Reference.md) — Complete BMAD workflow map
- [SpecKit-Workflow](speckit/SpecKit-Workflow.md) — SpecKit phases, commands, artifacts
- [OpenSpec-Workflow](openspec/OpenSpec-Workflow.md) — OpenSpec workflows, delta specs, schemas, CLI

---

## Sources

### BMAD Method
- [Getting Started | BMAD Method](https://docs.bmad-method.org/tutorials/getting-started/)
- [Agents | BMAD Method](https://docs.bmad-method.org/reference/agents/)
- [Workflows Reference | BMAD Method](https://docs.bmad-method.org/reference/workflows/)
- [Workflow Map | BMAD Method](https://docs.bmad-method.org/reference/workflow-map/)

### SpecKit (GitHub)
- [GitHub — spec-kit repository](https://github.com/github/spec-kit)
- [Spec-Driven Development with AI: Get Started with a New Open Source Toolkit — GitHub Blog](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/)
- [Diving Into Spec-Driven Development With GitHub Spec Kit — Microsoft for Developers](https://developer.microsoft.com/blog/spec-driven-development-spec-kit)
- [Putting Spec Kit Through Its Paces — Scott Logic](https://blog.scottlogic.com/2025/11/26/putting-spec-kit-through-its-paces-radical-idea-or-reinvented-waterfall.html)
- [Spec-Driven Development: 0 to 1 with Spec-kit & Cursor — Mad Devs](https://maddevs.io/writeups/project-creation-using-spec-kit-and-cursor/)

### OpenSpec (Fission AI)
- [OpenSpec GitHub Repository](https://github.com/Fission-AI/OpenSpec)
- [OpenSpec Website](https://openspec.dev)
- [OpenSpec Discord](https://discord.gg/YctCnvvshC)
- [OpenSpec Docs: Getting Started](https://github.com/Fission-AI/OpenSpec/blob/main/docs/getting-started.md)
- [OpenSpec Docs: Workflows](https://github.com/Fission-AI/OpenSpec/blob/main/docs/workflows.md)
- [OpenSpec Docs: Concepts](https://github.com/Fission-AI/OpenSpec/blob/main/docs/concepts.md)
- [OpenSpec Docs: OPSX Deep Dive](https://github.com/Fission-AI/OpenSpec/blob/main/docs/opsx.md)

### General SDD
- [Understanding Spec-Driven-Development: Kiro, spec-kit, and Tessl — Martin Fowler](https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html)
- [Spec-Driven Development Explained — Thoughtworks](https://www.thoughtworks.com/en-us/insights/blog/agile-engineering-practices/spec-driven-development-unpacking-2025-new-engineering-practices)
- [Spec-Driven Development: From Code to Contract in the Age of AI Coding Assistants — arXiv](https://arxiv.org/html/2602.00180v1)
- [Beyond TDD: Why Spec-Driven Development is the Next Step — Kinde](https://www.kinde.com/learn/ai-for-software-engineering/best-practice/beyond-tdd-why-spec-driven-development-is-the-next-step/)
