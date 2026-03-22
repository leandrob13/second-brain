# BMAD Method — Getting Started Workflow

#bmad #workflow #agile #ai-driven-development #sdd #getting-started

> **Source:** [Getting Started | BMAD Method](https://docs.bmad-method.org/tutorials/getting-started/)

## What is BMAD?

The **Breakthrough Method for Agile AI-Driven Development (BMAD)** is a structured framework for building software from ideation through implementation using specialized AI agents. It provides guided workflows, domain-expert agents, and adaptive planning that scales to project complexity.

---

## Installation

```bash
npx bmad-method install
# or for prerelease:
npx bmad-method@next install
```

This creates two folders in your project:

- `_bmad/` — agents, workflows, tasks, and configuration
- `_bmad-output/` — all generated artifacts (PRDs, architecture docs, stories, etc.)

---

## Planning Tracks

Choose the right track based on project complexity before starting:

| Track | Best For | Story Count | Key Artifacts |
|---|---|---|---|
| **Quick Flow** | Simple features | 1–15 stories | Tech-spec only |
| **BMad Method** | Products / platforms | 10–50+ stories | PRD + Architecture + UX |
| **Enterprise** | Complex multi-tenant systems | 30+ stories | PRD + Architecture + Security + DevOps |

---

## The 4-Phase Workflow (Step-by-Step)

### Phase 1 — Explore / Analysis *(Optional)*

> Goal: Validate the idea and understand the problem space before committing to planning.

| Step | Agent | Skill / Command | Output |
|---|---|---|---|
| 1 | Any | `bmad-brainstorming` | Structured ideas |
| 2 | Any | `bmad-research` | Research findings |
| 3 | Any | `bmad-create-product-brief` | `product-brief.md` |

---

### Phase 2 — Planning *(Required)*

> Goal: Define *what* to build and *for whom*.

#### BMad Method / Enterprise Track

| Step | Invoke Agent | Run Skill | Output |
|---|---|---|---|
| 1 | `bmad-pm` | `bmad-create-prd` | `PRD.md` |
| 2 *(optional)* | `bmad-ux-designer` | `bmad-create-ux-design` | UX design doc |

#### Quick Flow Track

| Step | Invoke Agent | Run Skill | Output |
|---|---|---|---|
| 1 | `bmad-dev` | `bmad-quick-dev` | Combined tech-spec + implementation |

---

### Phase 3 — Solutioning *(BMad Method / Enterprise only)*

> Goal: Decide *how* to build it and break work into stories.

| Step | Invoke Agent | Run Skill | Output |
|---|---|---|---|
| 1 | `bmad-architect` | `bmad-create-architecture` | `architecture.md` |
| 2 | `bmad-analyst` | `bmad-generate-project-context` | `project-context.md` |
| 3 | `bmad-pm` | `bmad-create-epics-and-stories` | Epic + story files |
| 4 *(recommended)* | `bmad-architect` | `bmad-check-implementation-readiness` | Validation report |

> **Note (V6):** Epics and stories are created *after* architecture so that all work breakdown is technically informed.

---

### Phase 4 — Implementation

> Goal: Build it, one story at a time.

#### Sprint Initialization

| Step | Invoke Agent | Run Skill | Output |
|---|---|---|---|
| 1 | `bmad-sm` | `bmad-sprint-planning` | `sprint-status.yaml` |

#### Per-Story Build Cycle *(repeat for each story)*

| Step | Invoke Agent | Run Skill | Output |
|---|---|---|---|
| 1 — Create | `bmad-sm` | `bmad-create-story` | Story file |
| 2 — Develop | `bmad-dev` | `bmad-dev-story` | Implemented code |
| 3 — Review | `bmad-dev` | `bmad-code-review` | Review report |

#### Epic Completion

| Step | Invoke Agent | Run Skill | Output |
|---|---|---|---|
| 1 | `bmad-sm` | `bmad-retrospective` | Retrospective notes |

---

## Complete Skill / Command Reference

| Command | Agent | Purpose |
|---|---|---|
| `bmad-help` ⭐ | Any | Intelligent context-aware guide — **always start here** |
| `bmad-brainstorming` | Any | Guided ideation |
| `bmad-research` | Any | Market / technical research |
| `bmad-create-product-brief` | Any | Foundation brief |
| `bmad-create-prd` | `bmad-pm` | Product Requirements Document |
| `bmad-create-ux-design` | `bmad-ux-designer` | UX design artifacts |
| `bmad-quick-dev` | `bmad-dev` | Quick Flow combined track |
| `bmad-create-architecture` | `bmad-architect` | Technical architecture document |
| `bmad-generate-project-context` | `bmad-analyst` | Implementation rules & context |
| `bmad-create-epics-and-stories` | `bmad-pm` | Work breakdown structure |
| `bmad-check-implementation-readiness` | `bmad-architect` | Planning validation check |
| `bmad-sprint-planning` | `bmad-sm` | Sprint tracking file |
| `bmad-create-story` | `bmad-sm` | Individual story file |
| `bmad-dev-story` | `bmad-dev` | Story implementation |
| `bmad-code-review` | `bmad-dev` | Code quality review |
| `bmad-retrospective` | `bmad-sm` | Epic retrospective |

---

## bmad-help — Intelligent Guide

`bmad-help` is the primary interface. Run it at any point:

```
bmad-help             # General guidance for what to do next
bmad-help [question]  # Context-aware answer to a specific question
```

It inspects your project state, detects installed modules, and recommends the next required or optional workflow. It runs **automatically** at the end of each workflow so you never have to guess what comes next.

---

## Project Artifact Structure

```
your-project/
├── _bmad/                          # Agents, workflows, tasks
└── _bmad-output/
    ├── planning-artifacts/
    │   ├── product-brief.md
    │   ├── PRD.md
    │   ├── architecture.md
    │   └── epics/
    ├── implementation-artifacts/
    │   └── sprint-status.yaml
    └── project-context.md
```

---

## Key Rules

- Always use **fresh chat sessions** for each workflow
- Epics and stories are always created *after* architecture (ensures technical alignment)
- Story counts in track definitions are guidance, not strict limits
- Quick Flow skips architecture entirely

---

## Related Notes

- [[BMAD-Agents]]
- [[BMAD-Workflows-Reference]]
- [[SDD-Index]]
 