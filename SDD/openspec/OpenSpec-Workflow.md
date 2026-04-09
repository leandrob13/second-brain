---
tags:
  - software-engineering
  - spec-driven-development
  - SDD
  - openspec
  - AI-engineering
  - methodology
  - agent-engineering
  - best-practices
  - context-engineering
  - planning
related:
  - "[[spec-driven-development]]"
  - "[[SpecKit-Workflow]]"
  - "[[BMAD-Getting-Started]]"
---

# OpenSpec — Spec-Driven Development Framework

> *"Agree before you build. OpenSpec adds a lightweight spec layer so you and your AI coding assistant align on what to build before any code is written."*

## What Is OpenSpec?

OpenSpec is an **open-source, lightweight spec-driven development (SDD) framework** created by Fission AI. It provides a universal planning layer for AI coding assistants, enabling developers to define, organize, and evolve software specifications alongside their code — independent of any specific IDE or AI model.

| Attribute | Detail |
|-----------|--------|
| **Creator** | Fission AI |
| **License** | MIT |
| **Language** | TypeScript |
| **Package** | `@fission-ai/openspec` (npm) |
| **Requires** | Node.js >= 20.19.0 |
| **GitHub** | [Fission-AI/OpenSpec](https://github.com/Fission-AI/OpenSpec) |
| **Website** | [openspec.dev](https://openspec.dev) |
| **Stars** | ~38.6k |
| **Supported Tools** | 20+ (Claude Code, Cursor, Codex, Copilot, OpenCode, Windsurf, Gemini CLI, Cline, RooCode, Kilo Code, Amazon Q, and more) |

---

## Philosophy & Core Principles

OpenSpec is built on four foundational principles:

```
fluid not rigid         — no phase gates, work on what makes sense
iterative not waterfall — learn as you build, refine as you go
easy not complex        — lightweight setup, minimal ceremony
brownfield-first        — works with existing codebases, not just greenfield
```

### Why These Principles Matter

- **Fluid not rigid.** Traditional spec systems lock you into phases. OpenSpec lets you create artifacts in any order that makes sense for your work.
- **Iterative not waterfall.** Requirements change. Understanding deepens. OpenSpec embraces this reality.
- **Easy not complex.** Initialize in seconds, start working immediately, customize only if needed.
- **Brownfield-first.** Most software work modifies existing systems. OpenSpec's delta-based approach makes specifying changes to existing behavior first-class.

---

## Architecture Overview

![OpenSpec Core Workflow](diagrams/openspec-core-workflow.drawio.svg)

### The Two Key Directories

OpenSpec organizes work into two main areas:

```
openspec/
├── specs/              # Source of truth (current system behavior)
│   └── <domain>/
│       └── spec.md
├── changes/            # Proposed modifications (one folder per change)
│   ├── <change-name>/
│   │   ├── proposal.md
│   │   ├── design.md
│   │   ├── tasks.md
│   │   ├── .openspec.yaml
│   │   └── specs/      # Delta specs (what's changing)
│   │       └── <domain>/
│   │           └── spec.md
│   └── archive/        # Completed changes (audit trail)
│       └── YYYY-MM-DD-<name>/
├── schemas/            # Custom workflow definitions (optional)
│   └── <schema-name>/
│       ├── schema.yaml
│       └── templates/
└── config.yaml         # Project configuration (optional)
```

- **`specs/`** — The source of truth. Describes how your system currently behaves. Organized by domain (e.g., `specs/auth/`, `specs/payments/`).
- **`changes/`** — Proposed modifications. Each change gets its own folder with all related artifacts. When complete, delta specs merge into `specs/`.

---

## The Virtuous Cycle

![OpenSpec Artifact Flow](diagrams/openspec-artifact-flow.drawio.svg)

The core lifecycle is a continuous loop:

1. **Specs** describe current behavior
2. **Changes** propose modifications (as deltas)
3. **Implementation** makes the changes real
4. **Archive** merges deltas into specs
5. **Specs** now describe the new behavior
6. Next change builds on updated specs

---

## Artifacts — The Documents Inside a Change

Each change folder contains artifacts that guide the work:

| Artifact | Purpose | Focus |
|----------|---------|-------|
| `proposal.md` | The "why" and "what" | Intent, scope, approach |
| `specs/` | Delta specs | ADDED/MODIFIED/REMOVED requirements |
| `design.md` | The "how" | Technical approach, architecture decisions |
| `tasks.md` | Implementation checklist | Checkboxed task list |

### Artifact Dependency Graph

Artifacts form a directed acyclic graph (DAG):

```
                    proposal
                   (root node)
                       │
         ┌─────────────┴─────────────┐
         │                           │
         ▼                           ▼
      specs                       design
   (requires:                  (requires:
    proposal)                   proposal)
         │                           │
         └─────────────┬─────────────┘
                       │
                       ▼
                    tasks
                (requires:
                specs, design)
```

Dependencies are **enablers, not gates**. They show what's possible to create, not what you must create next.

### Artifact State Transitions

```
BLOCKED ──────────► READY ──────────► DONE
  │                   │                 │
Missing            All deps          File exists
dependencies       are DONE          on filesystem
```

---

## Specs — The Source of Truth

### Spec Format

Specs contain requirements, and each requirement has scenarios:

```markdown
# Auth Specification

## Purpose
Authentication and session management for the application.

## Requirements

### Requirement: User Authentication
The system SHALL issue a JWT token upon successful login.

#### Scenario: Valid credentials
- GIVEN a user with valid credentials
- WHEN the user submits login form
- THEN a JWT token is returned
- AND the user is redirected to dashboard

#### Scenario: Invalid credentials
- GIVEN invalid credentials
- WHEN the user submits login form
- THEN an error message is displayed
- AND no token is issued
```

### Key Elements

| Element | Purpose |
|---------|---------|
| `## Purpose` | High-level description of spec domain |
| `### Requirement:` | A specific behavior the system must have |
| `#### Scenario:` | A concrete, testable example (Given/When/Then) |
| SHALL/MUST/SHOULD/MAY | RFC 2119 keywords for requirement strength |

### What a Spec IS vs IS NOT

**Good spec content:**
- Observable behavior users or downstream systems rely on
- Inputs, outputs, and error conditions
- External constraints (security, privacy, reliability)
- Scenarios that can be tested

**Avoid in specs:**
- Internal class/function names
- Library or framework choices
- Step-by-step implementation details
- Execution plans (those belong in `design.md` or `tasks.md`)

> **Quick test:** If implementation can change without changing externally visible behavior, it does not belong in the spec.

### Progressive Rigor

| Level | When to Use | Content |
|-------|-------------|---------|
| **Lite** (default) | Most changes | Short behavior-first requirements, clear scope, acceptance checks |
| **Full** | High risk | Cross-team changes, API/contract changes, migrations, security/privacy concerns |

---

## Delta Specs — Brownfield-First Design

Delta specs are the key innovation that makes OpenSpec work for existing codebases. They describe **what's changing** rather than restating the entire spec.

### Delta Format

```markdown
# Delta for Auth

## ADDED Requirements

### Requirement: Two-Factor Authentication
The system MUST support TOTP-based two-factor authentication.

#### Scenario: 2FA enrollment
- GIVEN a user without 2FA enabled
- WHEN the user enables 2FA in settings
- THEN a QR code is displayed for authenticator app setup

## MODIFIED Requirements

### Requirement: Session Expiration
The system MUST expire sessions after 15 minutes of inactivity.
(Previously: 30 minutes)

## REMOVED Requirements

### Requirement: Remember Me
(Deprecated in favor of 2FA)
```

### Delta Sections

| Section | Meaning | On Archive |
|---------|---------|------------|
| `## ADDED Requirements` | New behavior | Appended to main spec |
| `## MODIFIED Requirements` | Changed behavior | Replaces existing requirement |
| `## REMOVED Requirements` | Deprecated behavior | Deleted from main spec |

### Why Deltas Instead of Full Specs

- **Clarity.** A delta shows exactly what's changing.
- **Conflict avoidance.** Two changes can touch the same spec file without conflicting, as long as they modify different requirements.
- **Review efficiency.** Reviewers see the change, not the unchanged context.
- **Brownfield fit.** Modifications are first-class, not an afterthought.

---

## Workflows — Two Modes

![OpenSpec OPSX Command Flow](diagrams/openspec-opsx-commands.drawio.svg)

### Default Quick Path (`core` profile)

The default for new installs:

```
/opsx:propose ──► /opsx:apply ──► /opsx:archive
```

| Command | Purpose |
|---------|---------|
| `/opsx:propose` | Create change + all planning artifacts in one step |
| `/opsx:explore` | Think through ideas before committing |
| `/opsx:apply` | Implement tasks from the change |
| `/opsx:archive` | Archive completed change |

### Expanded Workflow (custom selection)

For more granular control, enable with `openspec config profile`:

```
/opsx:new ──► /opsx:ff or /opsx:continue ──► /opsx:apply ──► /opsx:verify ──► /opsx:archive
```

| Command | Purpose |
|---------|---------|
| `/opsx:new` | Start a new change scaffold |
| `/opsx:continue` | Create next artifact based on dependencies |
| `/opsx:ff` | Fast-forward: create all planning artifacts at once |
| `/opsx:apply` | Implement tasks |
| `/opsx:verify` | Validate implementation against artifacts |
| `/opsx:sync` | Merge delta specs into main specs (optional) |
| `/opsx:archive` | Archive the completed change |
| `/opsx:bulk-archive` | Archive multiple completed changes |
| `/opsx:onboard` | Guided interactive tutorial |

---

## Workflow Patterns

### Pattern 1: Quick Feature

When you know what to build and just need to execute:

```
/opsx:new ──► /opsx:ff ──► /opsx:apply ──► /opsx:verify ──► /opsx:archive
```

Best for: Small to medium features, bug fixes, straightforward changes.

### Pattern 2: Exploratory

When requirements are unclear or you need to investigate first:

```
/opsx:explore ──► /opsx:new ──► /opsx:continue ──► ... ──► /opsx:apply
```

Best for: Performance optimization, debugging, architectural decisions, unclear requirements.

### Pattern 3: Parallel Changes

Work on multiple changes at once:

```
Change A: /opsx:new ──► /opsx:ff ──► /opsx:apply (in progress)
                                        │
                                   context switch
                                        │
Change B: /opsx:new ──► /opsx:ff ──► /opsx:apply
```

Use `/opsx:bulk-archive` to archive multiple completed changes, with automatic spec conflict detection.

### Pattern 4: Completion Flow

The recommended completion pattern:

```
/opsx:apply ──► /opsx:verify ──► /opsx:archive
                    │                 │
              validates          prompts to sync
              implementation     if needed
```

---

## Verify — Three-Dimensional Validation

`/opsx:verify` validates implementation against artifacts across three dimensions:

| Dimension | What it Validates |
|-----------|-------------------|
| **Completeness** | All tasks done, all requirements implemented, scenarios covered |
| **Correctness** | Implementation matches spec intent, edge cases handled |
| **Coherence** | Design decisions reflected in code, patterns consistent |

Verify surfaces issues categorized as **CRITICAL**, **WARNING**, or **SUGGESTION**. It does not block archive.

---

## `/opsx:ff` vs `/opsx:continue`

| Situation | Use |
|-----------|-----|
| Clear requirements, ready to build | `/opsx:ff` |
| Exploring, want to review each step | `/opsx:continue` |
| Want to iterate on proposal before specs | `/opsx:continue` |
| Time pressure, need to move fast | `/opsx:ff` |
| Complex change, want control | `/opsx:continue` |

**Rule of thumb:** If you can describe the full scope upfront, use `/opsx:ff`. If figuring it out as you go, use `/opsx:continue`.

---

## Schemas — Custom Workflows

Schemas define artifact types and their dependencies.

### Built-In Schema: `spec-driven`

```yaml
name: spec-driven
artifacts:
  - id: proposal
    generates: proposal.md
    requires: []
  - id: specs
    generates: specs/**/*.md
    requires: [proposal]
  - id: design
    generates: design.md
    requires: [proposal]
  - id: tasks
    generates: tasks.md
    requires: [specs, design]
```

### Custom Schemas

Create custom workflows:

```bash
# Create from scratch
openspec schema init research-first

# Fork an existing one
openspec schema fork spec-driven my-workflow

# Validate
openspec schema validate my-workflow
```

**Example custom schema:**

```yaml
name: research-first
artifacts:
  - id: research
    generates: research.md
    requires: []
  - id: proposal
    generates: proposal.md
    requires: [research]
  - id: tasks
    generates: tasks.md
    requires: [proposal]
```

**Schema resolution order** (highest priority first):

1. CLI flag: `--schema <name>`
2. Change metadata (`.openspec.yaml`)
3. Project config (`openspec/config.yaml`)
4. Default (`spec-driven`)

---

## Project Configuration

```yaml
# openspec/config.yaml
schema: spec-driven

context: |
  Tech stack: TypeScript, React, Node.js
  API conventions: RESTful, JSON responses
  Testing: Vitest for unit tests, Playwright for e2e

rules:
  proposal:
    - Include rollback plan
    - Identify affected teams
  specs:
    - Use Given/When/Then format for scenarios
  design:
    - Include sequence diagrams for complex flows
```

| Field | Type | Description |
|-------|------|-------------|
| `schema` | string | Default schema for new changes |
| `context` | string | Project context injected into ALL artifact instructions |
| `rules` | object | Per-artifact rules, keyed by artifact ID |

---

## CLI Reference

### Setup Commands

| Command | Purpose |
|---------|---------|
| `openspec init` | Initialize OpenSpec in project |
| `openspec update` | Refresh AI instruction files after upgrade |

### Browsing Commands

| Command | Purpose |
|---------|---------|
| `openspec list` | List active changes or specs |
| `openspec view` | Interactive dashboard |
| `openspec show <name>` | Display change/spec details |

### Workflow Commands

| Command | Purpose |
|---------|---------|
| `openspec status --change <name>` | Artifact completion status |
| `openspec instructions <artifact>` | Get enriched instructions for creating an artifact |
| `openspec templates` | Show resolved template paths |
| `openspec schemas` | List available schemas |

### Schema Commands

| Command | Purpose |
|---------|---------|
| `openspec schema init <name>` | Create new schema |
| `openspec schema fork <source> [name]` | Fork existing schema |
| `openspec schema validate [name]` | Validate schema structure |
| `openspec schema which [name]` | Show where schema resolves from |

### Lifecycle Commands

| Command | Purpose |
|---------|---------|
| `openspec validate [item]` | Validate changes/specs |
| `openspec archive [name]` | Archive completed change |

### Config Commands

| Command | Purpose |
|---------|---------|
| `openspec config list` | Show all settings |
| `openspec config profile` | Configure workflow profile |
| `openspec config edit` | Open config in editor |

---

## Update vs Start Fresh — Heuristics

| Test | Update Existing | New Change |
|------|----------------|------------|
| **Identity** | "Same thing, refined" | "Different work" |
| **Scope overlap** | >50% overlaps | <50% overlaps |
| **Completion** | Can't be "done" without changes | Can finish original, new work stands alone |
| **Story** | Update chain tells coherent story | Patches would confuse more than clarify |

> **Update preserves context. New change provides clarity.**

---

## Naming Best Practices

```
Good:                          Avoid:
add-dark-mode                  feature-1
fix-login-redirect             update
optimize-product-query         changes
implement-2fa                  wip
```

---

## Command Syntax by AI Tool

| Tool | Syntax Example |
|------|----------------|
| Claude Code | `/opsx:propose`, `/opsx:apply` |
| Cursor | `/opsx-propose`, `/opsx-apply` |
| Windsurf | `/opsx-propose`, `/opsx-apply` |
| Copilot (IDE) | `/opsx-propose`, `/opsx-apply` |
| Trae | Skill-based: `/openspec-propose`, `/openspec-apply-change` |

---

## Human + Agent Collaboration Model

The intended loop for spec authoring:

1. **Human** provides intent, context, and constraints
2. **Agent** converts this into behavior-first requirements and scenarios
3. **Agent** keeps implementation detail in `design.md` and `tasks.md`, not `spec.md`
4. **Validation** confirms structure and clarity before implementation

---

## How OpenSpec Compares

![OpenSpec vs Other SDD Tools](diagrams/openspec-comparison.drawio.svg)

### vs SpecKit (GitHub)

| Aspect | OpenSpec | SpecKit |
|--------|----------|---------|
| **Philosophy** | Fluid actions, no phase gates | Sequential checkpointed phases (Constitution > Specify > Plan > Tasks > Implement) |
| **Setup** | Node.js, `npm install -g` + `openspec init` | Python/uv, `uv tool install` + `specify init` |
| **Workflow Model** | Actions you can take anytime; dependencies are enablers | 5 required phases + 3 optional steps; each phase gates the next |
| **Iteration** | Update any artifact anytime, no locks | Phase-locked; going back requires re-running previous commands |
| **Spec Model** | Delta specs (ADDED/MODIFIED/REMOVED) for brownfield | Full specs per feature; no delta concept |
| **Artifacts** | proposal, specs (deltas), design, tasks (4 artifacts) | constitution, spec, plan, data-model, research, quickstart, contracts, tasks (8+ artifacts) |
| **Constitution/Governance** | Optional `config.yaml` with context + rules | Dedicated `constitution.md` as mandatory first phase |
| **Git Integration** | Specs live in `openspec/` directory; no branch management | Creates feature branches per spec (`001-<name>`); branch-scoped specs |
| **Customization** | Custom schemas + templates (YAML-driven), forkable | Extensions and Presets system for adding commands and customizing templates |
| **Optional Quality Gates** | `/opsx:verify` (completeness, correctness, coherence) | `/speckit.clarify`, `/speckit.analyze`, `/speckit.checklist` (3 optional gates) |
| **Parallel Changes** | Native support; multiple changes in `changes/`; bulk-archive | One feature per branch; no built-in parallel change management |
| **Archive/History** | Changes archived to `changes/archive/` with full audit trail | Specs persist in `.specify/specs/NNN-<name>/`; no explicit archive workflow |
| **Best For** | Iterative feature work, brownfield codebases, quick planning cycles | Thorough greenfield builds, compliance-heavy projects, full-stack scaffolding |

### vs BMAD Method

| Aspect                  | OpenSpec                                            | BMAD                                                      |
| ----------------------- | --------------------------------------------------- | --------------------------------------------------------- |
| **Philosophy**          | Lightweight, fluid, no phase gates                  | Structured, agent-driven, phase-gated                     |
| **Scope**               | Spec planning layer (propose/apply/archive)         | Full SDLC (ideation through sprints and retros)           |
| **Agents**              | Works with any AI assistant generically             | 9 specialized named agents (PM, Architect, Dev, SM, etc.) |
| **Workflow**            | Customizable schemas, user-defined artifacts        | 34+ predefined workflows across 4 phases                  |
| **Planning Tracks**     | Single schema-driven approach                       | 3 tracks: Quick Flow, BMad Method, Enterprise             |
| **Artifacts**           | proposal, specs (deltas), design, tasks             | PRD, architecture, UX spec, epics, stories, sprint-status |
| **Spec Model**          | Delta specs (ADDED/MODIFIED/REMOVED) for brownfield | Full artifact generation per phase, no delta concept      |
| **Iteration**           | Update any artifact anytime, no phase locks         | Fresh chat session per workflow, phase ordering matters   |
| **Customization**       | Custom schemas + templates (YAML-driven)            | Custom agents via `.md`/`.yaml` files                     |
| **Context Persistence** | Specs live in repo as source of truth               | `project-context.md` acts as constitution for agents      |
| **Best For**            | Feature-level planning with AI assistants           | Full product builds from ideation to implementation       |
| **Setup**               | `npm install -g` + `openspec init`                  | `npx bmad-method install`                                 |

### vs No Spec Tool

| Aspect | With OpenSpec | Without Specs |
|--------|---------------|---------------|
| **Context** | Persists across sessions | Lost when chat ends |
| **Consistency** | Spec-guided generation | Vague prompts, unpredictable results |
| **Review** | Review intent, not just code | Only code review |
| **History** | Full audit trail | No change context |

---

## Best Practices

### Keep Changes Focused

One logical unit of work per change. If doing "add feature X and also refactor Y", consider two separate changes.

Why:
- Easier to review and understand
- Cleaner archive history
- Can ship independently
- Simpler rollback if needed

### Use Explore for Unclear Requirements

Before committing to a change, use `/opsx:explore` to think through the problem space. No artifacts are created during exploration.

### Verify Before Archiving

Run `/opsx:verify` to check implementation matches artifacts. Catches mismatches before you close out the change.

### Context Hygiene

OpenSpec benefits from a clean context window. Clear your context before starting implementation. Maintain good context hygiene throughout your session.

### Model Selection

OpenSpec works best with high-reasoning models. The project recommends Opus-class and GPT-5 class models for both planning and implementation.

### Spec Writing Guidelines

1. **Be explicit about external behavior** — inputs, outputs, constraints
2. **Use domain language** — business intent, not technology
3. **Structure with Given/When/Then** — enforces clarity
4. **Define failure modes explicitly** — happy paths alone are incomplete
5. **Keep specs DRY** — use references, avoid duplication
6. **Aim for determinism** — reduce ambiguity to reduce LLM hallucinations

---

## Installation & Setup

```bash
# Install globally
npm install -g @fission-ai/openspec@latest

# Initialize in your project
cd your-project
openspec init

# Start working
# Tell your AI: /opsx:propose <what-you-want-to-build>
```

Also works with pnpm, yarn, bun, and nix.

### Updating

```bash
# Upgrade the package
npm install -g @fission-ai/openspec@latest

# Refresh agent instructions in each project
openspec update
```

### Telemetry

Collects anonymous usage stats (command names and version only). No arguments, paths, content, or PII.

**Opt-out:** `export OPENSPEC_TELEMETRY=0` or `export DO_NOT_TRACK=1`

---

## Legacy Commands

Older "all-at-once" commands still work but OPSX commands are recommended:

| Legacy Command | OPSX Equivalent |
|----------------|-----------------|
| `/openspec:proposal` | `/opsx:propose` (or `/opsx:new` + `/opsx:ff`) |
| `/openspec:apply` | `/opsx:apply` |
| `/openspec:archive` | `/opsx:archive` |

Legacy changes can be continued with OPSX commands. The artifact structure is compatible.

---

## References

- [OpenSpec GitHub Repository](https://github.com/Fission-AI/OpenSpec)
- [OpenSpec Website](https://openspec.dev)
- [OpenSpec Discord](https://discord.gg/YctCnvvshC)
- [Getting Started Docs](https://github.com/Fission-AI/OpenSpec/blob/main/docs/getting-started.md)
- [Workflows Docs](https://github.com/Fission-AI/OpenSpec/blob/main/docs/workflows.md)
- [Commands Reference](https://github.com/Fission-AI/OpenSpec/blob/main/docs/commands.md)
- [Concepts Docs](https://github.com/Fission-AI/OpenSpec/blob/main/docs/concepts.md)
- [OPSX Deep Dive](https://github.com/Fission-AI/OpenSpec/blob/main/docs/opsx.md)
- [Customization Docs](https://github.com/Fission-AI/OpenSpec/blob/main/docs/customization.md)
- [CLI Reference](https://github.com/Fission-AI/OpenSpec/blob/main/docs/cli.md)
- [Spec-Driven Development (SDD)](../spec-driven-development.md) — General SDD methodology notes
- [SpecKit Workflow](../speckit/SpecKit-Workflow.md) — GitHub's SDD toolkit
- [@0xTab on X](https://x.com/0xTab) — OpenSpec creator updates
