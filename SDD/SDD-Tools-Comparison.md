---
tags:
  - software-engineering
  - spec-driven-development
  - SDD
  - bmad
  - speckit
  - openspec
  - superpowers
  - gsd
  - AI-engineering
  - methodology
  - comparison
  - best-practices
  - agent-engineering
  - context-engineering
  - skills
  - TDD
related:
  - "[[spec-driven-development]]"
  - "[[BMAD-Getting-Started]]"
  - "[[SpecKit-Workflow]]"
  - "[[OpenSpec-Workflow]]"
  - "[[Superpowers-Workflow]]"
  - "[[GSD-Workflow]]"
---

# SDD Tools Comparison — BMAD vs SpecKit vs OpenSpec vs Superpowers vs GSD

> A comprehensive comparison of the five leading Spec-Driven Development frameworks for AI-assisted software engineering. Each tool embodies SDD principles differently, targeting different scales, workflows, and team structures.

---

## Tool Profiles at a Glance

| Attribute | BMAD | SpecKit | OpenSpec | Superpowers | GSD |
|-----------|------|---------|----------|-------------|-----|
| **Full Name** | Breakthrough Method for Agile AI-Driven Development | GitHub Spec-Kit | OpenSpec | Superpowers | Get Shit Done |
| **Creator** | BMAD Method / Community | GitHub | Fission AI | Jesse Vincent (obra) / Prime Radiant | TACHES |
| **License** | Open Source | Open Source (MIT) | MIT | MIT | MIT |
| **Language** | Node.js | Python (uv) | TypeScript (Node.js) | Shell, JavaScript, Python, TypeScript | JavaScript / TypeScript |
| **Install** | `npx bmad-method install` | `uv tool install specify-cli` | `npm install -g @fission-ai/openspec` | `/plugin install superpowers@claude-plugins-official` | `npx get-shit-done-cc@latest` |
| **Init** | Creates `_bmad/` + `_bmad-output/` | `specify init` creates `.specify/` | `openspec init` creates `openspec/` | Plugin bootstrap via session-start hook | `/gsd-new-project` creates `.planning/` |
| **Agent Support** | 9 built-in specialized agents | 22+ AI agents via slash commands | 20+ AI assistants via slash commands | Claude Code, Cursor, Codex, OpenCode, Gemini CLI, Copilot CLI | 12+ (Claude Code, OpenCode, Gemini CLI, Codex, Copilot, Cursor, Windsurf, others) |
| **Docs** | [docs.bmad-method.org](https://docs.bmad-method.org) | [github.com/github/spec-kit](https://github.com/github/spec-kit) | [openspec.dev](https://openspec.dev) | [github.com/obra/superpowers](https://github.com/obra/superpowers) | [github.com/gsd-build/get-shit-done](https://github.com/gsd-build/get-shit-done) |

---

## Philosophy Comparison

| Principle | BMAD | SpecKit | OpenSpec | Superpowers | GSD |
|-----------|------|---------|----------|-------------|-----|
| **Core metaphor** | Agile team simulation with AI agents | Sequential checkpointed pipeline | Fluid action-based planning layer | Composable skills that shape agent behavior | Anti-enterprise meta-prompting; complexity in system, not workflow |
| **Phase model** | 4 phases, phase-gated | 5 phases + 3 optional, phase-gated | No phase gates; dependency-based DAG | 7-stage auto-activating pipeline | Milestone > phases > plans; 5-step cycle per phase |
| **Iteration** | Fresh chat per workflow; order matters | Phase-locked; re-running required to go back | Update any artifact anytime | Fresh subagent per task; context isolation | Fresh 200K context per plan; pause/resume across sessions |
| **Brownfield** | Not a primary focus; full artifacts per phase | Supported but not first-class; full specs | First-class via delta specs (ADDED/MODIFIED/REMOVED) | Supported (follows existing patterns) | First-class via `/gsd-map-codebase` (4 parallel analysis agents) |
| **Scaling model** | 3 tracks: Quick Flow / BMad / Enterprise | Single linear pipeline for all sizes | Single schema-driven approach; custom schemas | Single skills-based approach; add custom skills | 3 granularity levels (coarse/standard/fine); workstreams for parallel milestones |
| **Opinionation** | High (roles, phases, tracks, ceremonies) | High (phases, constitution, checkpoints) | Low (fluid, minimal ceremony, customizable) | High on execution quality (mandatory TDD, mandatory code review); low on spec format | Medium (structured phases but configurable agents, modes, and profiles) |

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

### Superpowers

**Pros:**

- **Mandatory TDD enforcement** — The Iron Law: no production code without a failing test first. Code written before tests gets deleted. The strongest TDD discipline of any SDD tool.
- **Subagent-driven development** — Fresh subagent per task with two-stage review (spec compliance + code quality). Prevents context pollution across tasks and enables hours of autonomous work.
- **Systematic debugging methodology** — The only SDD tool with a dedicated 4-phase debugging workflow (root cause investigation, pattern analysis, hypothesis testing, implementation). Other tools focus on forward development only.
- **Persuasion-engineered skills** — Skills use Cialdini's influence principles (authority, commitment, scarcity, social proof, consistency) to ensure agent compliance, tested via adversarial subagent scenarios.
- **Self-improving system** — The `writing-skills` skill lets the agent create new skills from books, conversations, or extracted memories. The system grows organically.
- **Git worktree isolation** — Each feature gets its own worktree automatically, preventing branch conflicts and providing clean baselines.
- **Multi-platform support** — Works across Claude Code, Cursor, Codex, OpenCode, Gemini CLI, and Copilot CLI through a unified skills format.
- **Zero-friction activation** — Skills trigger automatically based on context. No commands to memorize; the agent discovers what skill to use.

**Cons:**

- **No delta/brownfield spec model** — Unlike OpenSpec, there's no ADDED/MODIFIED/REMOVED format. Design docs are full specs, not incremental deltas.
- **No constitution or governance concept** — No equivalent of SpecKit's mandatory governance document. Relies on design docs and CLAUDE.md for project context.
- **No parallel change management** — While git worktrees provide isolation, there's no explicit system for managing multiple concurrent feature changes like OpenSpec's `changes/` directory.
- **No formal phase gates** — Skills auto-activate, which is powerful but less auditable than SpecKit's sequential checkpoints or BMAD's readiness gate.
- **High token cost** — Running multiple agents (controller + implementer + spec reviewer + code quality reviewer) burns tokens fast. ~3x expected cost.
- **Platform dependency** — Requires subagent-capable platforms. Does not work with plain chat interfaces or basic LLM setups.
- **No independent CLI** — Not a standalone CLI tool. It's a set of SKILL.md files delivered as a plugin. The agent reads and follows them.

---

### GSD (Get Shit Done)

**Pros:**

- **Context rot prevention** — Core innovation. Every plan executes in a fresh 200K context window. Quality doesn't degrade as work progresses. Main context stays at 30-40%.
- **Multi-agent orchestration** — 4 parallel researchers, planner + plan-checker loop, wave-based parallel executors, and automated verifiers. Every stage uses specialized agents.
- **Brownfield-first design** — `/gsd-map-codebase` dispatches 4 parallel agents (stack, architecture, conventions, concerns) to analyze existing codebases before planning.
- **Built-in quality gates** — 4 canonical gate types: pre-flight (plan-checker), revision (plan iteration), escalation (verifier), and abort (critical failure). Schema drift and scope reduction detection built in.
- **Session management** — Pause/resume/handoff with `STATE.md` and `HANDOFF.json`. `/gsd-next` auto-detects the next step. No other SDD tool handles session continuity this well.
- **Speed vs quality presets** — 3 model profiles (quality/balanced/budget) and 3 granularity levels (coarse/standard/fine) let you tune the trade-off per project.
- **UI design system** — `/gsd-ui-phase` generates UI-SPEC.md contracts; `/gsd-ui-review` provides 6-pillar visual audits (copywriting, visuals, color, typography, spacing, experience).
- **Comprehensive command set** — 30+ commands covering the full workflow: project init, phase management, milestones, brownfield mapping, backlog, threads, seeds, workstreams, security, and debugging.
- **Security built-in** — Path traversal prevention, prompt injection detection, shell argument validation, and CI-ready injection scanning.

**Cons:**

- **No delta spec model** — Like BMAD and SpecKit, full specifications per feature. No mechanism for specifying only what changed in an existing system.
- **No formal specification format** — Plans use XML-structured task files, not behavioral specifications. Less suitable for teams that need queryable spec knowledge bases.
- **Anti-enterprise philosophy** — Explicitly rejects ceremonies, story points, and retrospectives. Not suitable for teams that value agile rituals.
- **Solo-developer optimized** — Designed for individuals and small teams. Less structured support for multi-team coordination than BMAD's Enterprise track.
- **Claude-centric model profiles** — Model profiles reference Opus/Sonnet/Haiku. Non-Anthropic providers require `inherit` mode, losing the optimization.
- **No constitution concept** — `PROJECT.md` and `STATE.md` provide project context but no mandatory governance principles like SpecKit's constitution.
- **No specialized agent roles** — Unlike BMAD's 9 named agents, GSD's agents are generic (researcher, planner, executor, verifier) without domain-specific personas.

---

## Dimension-by-Dimension Comparison

### Workflow Model

| Dimension | BMAD | SpecKit | OpenSpec | Superpowers | GSD |
|-----------|------|---------|----------|-------------|-----|
| **Phases** | 4 (Explore > Planning > Solutioning > Implementation) | 5 + 3 optional (Constitution > Specify > Plan > Tasks > Implement) | No phases; fluid actions with dependency DAG | 7-stage auto-activating (Brainstorm > Worktree > Plan > Implement > TDD > Review > Finish) | Milestone > Phases > Plans; 5-step cycle (Discuss > Plan > Execute > Verify > Ship) |
| **Commands** | 34+ workflows via slash commands or trigger codes | 8 commands (5 core + 3 optional) | 4 core / 11 expanded commands | No commands; skills auto-activate based on context | 30+ slash commands across 9 categories |
| **Phase gating** | Yes, order matters | Yes, strictly sequential | No; dependencies are enablers | Skills auto-trigger; hard gates on brainstorming and TDD | Yes; discuss > plan > execute per phase, but configurable |
| **Exploration** | Phase 1 (brainstorming, research) | None built-in | `/opsx:explore` | `brainstorming` skill (Socratic Q&A with design doc) | `/gsd-explore` (Socratic ideation) + `/gsd-discuss-phase` |
| **Fast path** | Quick Flow track (skips phases 2-3) | None; all 5 phases required | `/opsx:propose` (creates all artifacts at once) | `executing-plans` (batch execution, lower overhead) | `/gsd-fast` (inline trivial tasks) + `/gsd-quick` (ad-hoc with GSD guarantees) |

### Artifacts

| Artifact Type | BMAD | SpecKit | OpenSpec | Superpowers | GSD |
|---------------|------|---------|----------|-------------|-----|
| **Product brief / PRD** | `product-brief.md`, `PRD.md` | Not a distinct artifact | `proposal.md` (intent, scope, approach) | Design doc from brainstorming (free-form Markdown) | `PROJECT.md` (vision) + `REQUIREMENTS.md` (scoped v1/v2) |
| **Specification** | Part of PRD | `spec.md` (full feature spec) | `specs/` (delta specs with ADDED/MODIFIED/REMOVED) | Part of design doc | XML-structured `PLAN.md` per task (action, verify, done) |
| **Architecture** | `architecture.md` + ADRs | Part of `plan.md` | `design.md` (technical approach) | Part of design doc | `codebase/ARCHITECTURE.md` (from `/gsd-map-codebase`) |
| **Tech research** | Via `bmad-research` workflows | `research.md` | Not a distinct artifact | Part of brainstorming (explores alternatives) | `RESEARCH.md` per phase (4 parallel researcher agents) |
| **Data model** | Part of architecture | `data-model.md` | Part of design | Part of design doc | Part of research/plan |
| **API contracts** | Part of architecture | `contracts/api-spec.json`, `*-spec.md` | Part of design | Part of design doc | Part of research/plan |
| **Task breakdown** | Epic + story files | `tasks.md` (with `[P]` markers, file paths) | `tasks.md` (checkboxed list) | Plans with bite-sized tasks (2-5 min each, exact file paths, complete code) | `XX-YY-PLAN.md` (XML tasks with wave-based parallelism) |
| **UX design** | `ux-spec.md` | Not a distinct artifact | Not a distinct artifact | Not a distinct artifact | `XX-UI-SPEC.md` + `XX-UI-REVIEW.md` (6-pillar audit) |
| **Project context** | `project-context.md` | `constitution.md` + `CLAUDE.md` | `config.yaml` (context + rules) | `CLAUDE.md` / `AGENTS.md` / `GEMINI.md` | `PROJECT.md` + `STATE.md` (decisions, blockers, memory) |
| **Sprint tracking** | `sprint-status.yaml` | Not applicable | Not applicable | Not applicable | `ROADMAP.md` + `MILESTONES.md` (milestone-based tracking) |

### Governance & Quality

| Dimension | BMAD | SpecKit | OpenSpec | Superpowers | GSD |
|-----------|------|---------|----------|-------------|-----|
| **Architectural governance** | `project-context.md` as constitution | `constitution.md` (mandatory first phase) | `config.yaml` context + rules (optional) | Design doc + `CLAUDE.md` (no formal constitution) | `PROJECT.md` + `STATE.md` (no formal constitution) |
| **Pre-implementation gate** | `bmad-check-implementation-readiness` (PASS/CONCERNS/FAIL) | `/speckit.checklist` (optional) | None (verify is post-implementation) | Design approval + plan approval (hard gates) | Plan-checker loop validates against requirements (max 3x) |
| **Post-implementation validation** | `bmad-code-review` | None built-in | `/opsx:verify` (completeness, correctness, coherence) | Two-stage code review (spec compliance + code quality) | `/gsd-verify-work` (manual UAT) + automated verifier |
| **Ambiguity resolution** | Agent-guided per workflow | `/speckit.clarify` (optional) | Part of `/opsx:explore` or `/opsx:continue` | Brainstorming skill (Socratic Q&A) | `/gsd-discuss-phase` (interview or assumptions mode) |
| **Cross-artifact consistency** | `bmad-check-implementation-readiness` | `/speckit.analyze` (optional) | `/opsx:verify` | Spec reviewer subagent checks code against design | Plan-checker validates plans against requirements; Nyquist validation maps tests to requirements |
| **Spec drift prevention** | Manual (fresh chats) | Phase checkpoints | Archive merges deltas into source of truth | Manual (fresh subagents per task) | `SUMMARY.md` per plan committed to history; `STATE.md` tracks decisions |

### Customization

| Dimension | BMAD | SpecKit | OpenSpec | Superpowers | GSD |
|-----------|------|---------|----------|-------------|-----|
| **Workflow customization** | Custom agents via `.md`/`.yaml` files | Extensions (add commands) + Presets (customize templates) | Custom schemas via YAML + template overrides | Write new `SKILL.md` files; composable skill references | `config.json` (mode, granularity, model profile, toggleable workflow agents) |
| **Template system** | Agent persona files | Template files in `.specify/templates/` | Templates in `schemas/<name>/templates/` | SKILL.md files with YAML frontmatter | XML-structured plan templates with task types |
| **Extensibility model** | Add agents to `_bmad/agents/` | `specify extension add` / `specify preset add` | `openspec schema init` / `openspec schema fork` | Add skills to `skills/` directory; `writing-skills` skill teaches the agent | `/gsd-settings` for workflow agents; model profiles; branching strategies |
| **Per-project config** | Agent configuration in `_bmad/` | `.specify/` structure | `openspec/config.yaml` with context + per-artifact rules | `CLAUDE.md` / `AGENTS.md` / `GEMINI.md` per project | `.planning/config.json` + `PROJECT.md` + `STATE.md` |

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

### Choose Superpowers When...

- **TDD discipline is non-negotiable** — Superpowers has the strongest TDD enforcement of any SDD tool. The Iron Law, rationalization tables, and adversarial testing ensure the agent actually practices RED-GREEN-REFACTOR.
- **You want autonomous multi-hour coding sessions** — subagent-driven development with fresh context per task and two-stage review enables hours of reliable autonomous work.
- **Code quality matters more than spec management** — Superpowers excels at the "how to build it right" part. Two-stage review (spec compliance + code quality) catches issues other tools miss.
- **You need a debugging methodology** — the 4-phase systematic debugging skill is unique to Superpowers. No other SDD tool has a dedicated, rigorous debugging workflow.
- **You work across multiple AI platforms** — skills work across Claude Code, Cursor, Codex, OpenCode, Gemini CLI, and Copilot CLI through a unified SKILL.md format.
- **You value self-improving systems** — the `writing-skills` skill lets the agent create new skills from books, conversations, or experience. The system grows with your team.
- **You want implementation methodology, not spec format** — Superpowers teaches the agent how to work, not what format to use. Combine with other tools for spec management.

### Choose GSD When...

- **Context rot is your biggest problem** — GSD's core innovation is preventing quality degradation. Every plan executes in a fresh 200K context. Your main window stays clean.
- **You're a solo developer wanting reliable AI output** — GSD is explicitly designed for individuals and small teams. Describe what you want; the system handles research, planning, execution, and verification.
- **You want the fastest path from idea to PR** — `/gsd-new-project` + `/gsd-next` (auto-detect next step) provides a streamlined pipeline from project init through shipping.
- **Brownfield analysis is critical** — `/gsd-map-codebase` dispatches 4 parallel agents to analyze stack, architecture, conventions, and concerns. The deepest brownfield analysis of any SDD tool.
- **You need configurable quality/speed trade-offs** — 3 model profiles, 3 granularity levels, and toggleable workflow agents let you tune from "yolo prototyping" to "production-grade."
- **Session continuity matters** — pause/resume/handoff with `STATE.md` and `HANDOFF.json`. No other SDD tool handles multi-session work this well.
- **UI design is part of your workflow** — `/gsd-ui-phase` and `/gsd-ui-review` provide structured UI design contracts and 6-pillar visual audits.
- **You reject enterprise ceremony** — GSD explicitly avoids sprints, story points, and retrospectives. If you find those wasteful, GSD is built for you.

---

## How They Could Be Combined

These tools are not mutually exclusive. Each excels at a different part of the development lifecycle, and combining them creates a more complete system than any single tool provides. Superpowers and GSD particularly shine as **implementation layers** that pair with spec-management tools.

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

### Combination 5: OpenSpec for Specs + Superpowers for Implementation (STDD)

The community-developed **STDD (Spec/Test-Driven Development)** pattern integrates OpenSpec's specification management with Superpowers' TDD implementation methodology:

```
OpenSpec (what to build)              Superpowers (how to build it)
────────────────────────────         ────────────────────────────
/opsx:explore                        brainstorming (refine approach)
/opsx:propose → proposal + specs     writing-plans (bite-sized tasks)
/opsx:apply                          subagent-driven-development
                                     test-driven-development
                                     systematic-debugging
/opsx:verify                         requesting-code-review
/opsx:archive                        finishing-a-development-branch
```

**Why:** OpenSpec manages the **what** (delta specs, parallel changes, proposals). Superpowers enforces the **how** (TDD, subagent execution, two-stage review). Together they provide both specification governance AND implementation discipline.

### Combination 6: GSD for Execution + OpenSpec for Spec Management

Use OpenSpec for lightweight spec management and delta tracking, with GSD's multi-agent execution engine for implementation:

```
OpenSpec (spec layer)                 GSD (execution layer)
────────────────────────────         ────────────────────────────
/opsx:explore                        /gsd-explore
/opsx:propose → proposal + specs     /gsd-discuss-phase + /gsd-plan-phase
/opsx:apply                          /gsd-execute-phase (wave-based parallel)
/opsx:verify                         /gsd-verify-work (manual UAT)
/opsx:archive                        /gsd-ship (PR creation)
```

**Why:** OpenSpec's delta specs track what's changing across brownfield iterations. GSD's multi-agent execution (fresh 200K contexts, parallel waves, plan-checker loops) ensures implementation quality. OpenSpec's archive preserves the audit trail.

### Combination 7: Full-Stack with All Five Tools

A comprehensive approach using each tool at its strongest:

| Phase | Tool | Why |
|-------|------|-----|
| **Ideation & Research** | BMAD | Analyst agent, brainstorming, market research |
| **Product Definition** | BMAD | PM agent creates PRD, UX designer creates UX spec |
| **Architecture & Governance** | SpecKit + BMAD | Constitution + Architect agent with ADRs, readiness gate |
| **Brownfield Analysis** | GSD | `/gsd-map-codebase` with 4 parallel analysis agents |
| **Ongoing Spec Management** | OpenSpec | Delta specs, parallel changes, lightweight iteration |
| **Implementation Quality** | Superpowers | TDD enforcement, subagent-driven development, debugging |
| **Execution Engine** | GSD | Wave-based parallel execution, fresh 200K contexts |
| **UI Design** | GSD | `/gsd-ui-phase` + `/gsd-ui-review` (6-pillar audit) |
| **Sprint Ceremonies** | BMAD | Sprint planning, story creation, retros |

**Why:** Each tool covers a distinct gap. BMAD for the full SDLC and specialized agents. SpecKit for governance. OpenSpec for brownfield spec evolution. Superpowers for implementation discipline. GSD for execution orchestration.

### Combination 8: Shared Multi-Tool Repository

All five tools store artifacts as files in the repository:

```
project/
├── _bmad-output/               # BMAD planning artifacts (PRD, architecture)
├── .specify/                   # SpecKit governance (constitution, contracts)
├── openspec/
│   ├── specs/                  # Living behavioral specs (OpenSpec source of truth)
│   └── changes/                # Active changes (OpenSpec delta workflow)
├── .planning/                  # GSD execution artifacts (plans, research, state)
│   ├── PROJECT.md              # Project vision
│   ├── ROADMAP.md              # Phase tracking
│   └── phases/                 # Per-phase plans, summaries, verification
├── skills/                     # Superpowers SKILL.md files
│   ├── brainstorming/
│   ├── test-driven-development/
│   └── subagent-driven-development/
└── src/                        # Implementation
```

**Why:** BMAD produces initial planning artifacts. SpecKit establishes governance. OpenSpec manages ongoing spec evolution. GSD provides the execution engine with session state. Superpowers shapes agent behavior throughout.

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
| Analyze existing codebase | GSD `/gsd-map-codebase` | `codebase/STACK.md`, `ARCHITECTURE.md`, `CONVENTIONS.md`, `CONCERNS.md` |
| Initialize spec repo | OpenSpec `openspec init` | `openspec/` directory with `config.yaml` |
| Install implementation skills | Superpowers plugin install | Skills auto-activate on session start |
| Inject governance context | Manual | Copy constitution principles into `openspec/config.yaml` context |

### 2. Feature Development (Ongoing, Repeating)

| Activity | Tool | Output |
|----------|------|--------|
| Explore unclear requirements | OpenSpec `/opsx:explore` or GSD `/gsd-explore` | Clarified understanding (no artifacts) |
| Propose a feature change | OpenSpec `/opsx:propose` | proposal.md, specs/ (deltas), design.md, tasks.md |
| Review spec delta | Team (PR review) | Approved or revised delta specs |
| Refine implementation approach | Superpowers `brainstorming` skill | Design doc with alternatives and trade-offs |
| Plan execution tasks | GSD `/gsd-plan-phase` or Superpowers `writing-plans` | Atomic plans with verification steps |
| Implement with TDD | Superpowers `subagent-driven-development` + `test-driven-development` | Working code with tests (RED-GREEN-REFACTOR) |
| Execute in parallel waves | GSD `/gsd-execute-phase` | Fresh 200K context per plan, atomic commits |
| Code review | Superpowers two-stage review (spec + quality) | Review report |
| Validate implementation | OpenSpec `/opsx:verify` + GSD `/gsd-verify-work` | Completeness/correctness/coherence + UAT report |
| Archive completed change | OpenSpec `/opsx:archive` | Specs merged, change archived with audit trail |
| Ship | GSD `/gsd-ship` | PR with auto-generated body |

### 3. Sprint Ceremonies (Periodic)

| Activity | Tool | Output |
|----------|------|--------|
| Sprint planning | BMAD `bmad-sprint-planning` | `sprint-status.yaml` |
| Story creation from epics | BMAD `bmad-create-story` | Story files |
| Code review | BMAD `bmad-code-review` or GSD `/gsd-code-review` | Review report |
| Retrospective | BMAD `bmad-retrospective` | Retrospective notes |
| Milestone audit | GSD `/gsd-audit-milestone` | Definition-of-done verification |

### 4. Quality Gates (As Needed)

| Activity | Tool | Output |
|----------|------|--------|
| Pre-implementation readiness | BMAD `bmad-check-implementation-readiness` | PASS/CONCERNS/FAIL |
| Plan verification | GSD plan-checker loop (automatic, max 3x) | Validated plans against requirements |
| Cross-artifact consistency | SpecKit `/speckit.analyze` | Coverage report |
| Completion audit | SpecKit `/speckit.checklist` | Quality checklist |
| TDD enforcement | Superpowers `test-driven-development` Iron Law | Tests before code; delete code written without tests |
| Two-stage code review | Superpowers spec compliance + code quality reviewers | Separate spec and quality review reports |
| Post-implementation validation | OpenSpec `/opsx:verify` | Validation report |
| User acceptance testing | GSD `/gsd-verify-work` | UAT walkthrough with auto-diagnosis |
| Security enforcement | GSD `/gsd-secure-phase` | Threat models and security audit |

### 5. Governance Rules

- **Spec changes require PR review** — treat spec deltas with the same rigor as code changes.
- **Constitution is immutable without team consensus** — changes to `constitution.md` require explicit team approval.
- **Archive before merging code** — OpenSpec archive ensures specs and code stay in sync.
- **Readiness gates for high-risk changes** — use BMAD's implementation readiness check for architectural changes; SpecKit's analyze/checklist for compliance-critical features.
- **TDD is non-negotiable** — Superpowers' Iron Law applies: no production code without a failing test first. Code written before tests gets deleted.
- **Context hygiene** — fresh chat sessions for BMAD workflows; fresh 200K contexts for GSD execution; fresh subagents for Superpowers implementation.
- **Specs live in the repository** — all specification artifacts are version-controlled alongside code. No external systems.
- **Session state persists** — GSD's `STATE.md` and `HANDOFF.json` preserve decisions and progress across sessions.

---

## Summary Decision Matrix

| Scenario | Recommended Tool(s) |
|----------|---------------------|
| Greenfield product, 0 to 1 | BMAD (full lifecycle) or SpecKit (thorough scaffolding) |
| Brownfield feature iteration | OpenSpec or GSD (`/gsd-map-codebase` + execution) |
| Quick bug fix or small feature | OpenSpec (core profile: propose/apply/archive) or GSD (`/gsd-fast`) |
| Enterprise compliance project | SpecKit (constitution + checkpoints) + BMAD (readiness gates) |
| Solo developer, rapid iteration | GSD or OpenSpec |
| Team with agile ceremonies | BMAD |
| Multi-feature parallel development | OpenSpec or GSD (workstreams) |
| API-first development | SpecKit (contract generation) |
| Exploratory / unclear requirements | OpenSpec (`/opsx:explore`) or BMAD (Phase 1 research) or GSD (`/gsd-explore`) |
| Strict TDD enforcement | Superpowers (Iron Law, adversarial testing) |
| Autonomous multi-hour coding | Superpowers (subagent-driven development) or GSD (wave-based execution) |
| Context rot prevention | GSD (fresh 200K contexts per plan) |
| Debugging methodology | Superpowers (4-phase systematic debugging) or GSD (`/gsd-debug`) |
| UI design workflow | GSD (`/gsd-ui-phase` + `/gsd-ui-review`) or BMAD (UX designer agent) |
| Session continuity (multi-session work) | GSD (pause/resume/handoff with `STATE.md`) |
| Implementation quality layer | Superpowers (combine with any spec tool) |
| Full team governance at scale | All five combined (see Proposed Governance Model) |

---

## Related Notes

- [Spec-Driven Development (SDD)](spec-driven-development.md) — Core SDD methodology, principles, lifecycle, and integration with TDD/BDD
- [BMAD-Getting-Started](bmad/BMAD-Getting-Started.md) — BMAD workflow, phases, planning tracks
- [BMAD-Agents](bmad/BMAD-Agents.md) — All 9 BMAD agents with roles and triggers
- [BMAD-Workflows-Reference](bmad/BMAD-Workflows-Reference.md) — Complete BMAD workflow map
- [SpecKit-Workflow](speckit/SpecKit-Workflow.md) — SpecKit phases, commands, artifacts
- [OpenSpec-Workflow](openspec/OpenSpec-Workflow.md) — OpenSpec workflows, delta specs, schemas, CLI
- [Superpowers-Workflow](superpowers/Superpowers-Workflow.md) — Superpowers skills framework, TDD enforcement, subagent architecture
- [GSD-Workflow](gsd/GSD-Workflow.md) — GSD meta-prompting, context engineering, multi-agent orchestration

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

### Superpowers (Prime Radiant)
- [Superpowers GitHub Repository — obra/superpowers](https://github.com/obra/superpowers)
- [Superpowers: How I'm using coding agents in October 2025 — Jesse Vincent](https://blog.fsck.com/2025/10/09/superpowers/)
- [Superpowers 2.0 came out yesterday and might already be obsolete — Jesse Vincent](https://blog.fsck.com/2025/10/12/superpowers-20-came-out-yesterday-and-might-already-be-obsolete/)
- [How I'm using coding agents in September 2025 — Jesse Vincent](https://blog.fsck.com/2025/10/05/how-im-using-coding-agents-in-september-2025/)
- [Call Me a Jerk: Persuading AI — Wharton GAIL (Dan Shapiro, Robert Cialdini et al.)](https://gail.wharton.upenn.edu/research-and-insights/call-me-a-jerk-persuading-ai/)
- [Superpowers Discord Community](https://discord.gg/35wsABTejz)

### GSD (Get Shit Done)
- [GSD GitHub Repository](https://github.com/gsd-build/get-shit-done)
- [GSD npm Package](https://www.npmjs.com/package/get-shit-done-cc)
- [GSD User Guide](https://github.com/gsd-build/get-shit-done/blob/main/docs/USER-GUIDE.md)
- [GSD Discord](https://discord.gg/mYgfVNfA2r)
- [GSD on X/Twitter](https://x.com/gsd_foundation)

### General SDD
- [Understanding Spec-Driven-Development: Kiro, spec-kit, and Tessl — Martin Fowler](https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html)
- [Spec-Driven Development Explained — Thoughtworks](https://www.thoughtworks.com/en-us/insights/blog/agile-engineering-practices/spec-driven-development-unpacking-2025-new-engineering-practices)
- [Spec-Driven Development: From Code to Contract in the Age of AI Coding Assistants — arXiv](https://arxiv.org/html/2602.00180v1)
- [Beyond TDD: Why Spec-Driven Development is the Next Step — Kinde](https://www.kinde.com/learn/ai-for-software-engineering/best-practice/beyond-tdd-why-spec-driven-development-is-the-next-step/)
