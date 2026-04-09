---
tags:
  - software-engineering
  - spec-driven-development
  - SDD
  - gsd
  - AI-engineering
  - methodology
  - agent-engineering
  - best-practices
  - context-engineering
  - planning
  - meta-prompting
related:
  - "[[spec-driven-development]]"
  - "[[SpecKit-Workflow]]"
  - "[[OpenSpec-Workflow]]"
  - "[[BMAD-Getting-Started]]"
  - "[[SDD-Tools-Comparison]]"
---

# GSD (Get Shit Done) -- Meta-Prompting & Context Engineering System

> *"The complexity is in the system, not in your workflow. Behind the scenes: context engineering, XML prompt formatting, subagent orchestration, state management. What you see: a few commands that just work."* -- TACHES

## What Is GSD?

GSD (Get Shit Done) is a **lightweight, meta-prompting and context engineering system** for AI coding assistants. Created by TACHES, it solves **context rot** -- the quality degradation that happens as AI models fill their context window. Rather than emulating enterprise processes (sprints, ceremonies, retrospectives), GSD focuses on giving the AI everything it needs to produce reliable, high-quality code.

| Attribute | Detail |
|-----------|--------|
| **Creator** | TACHES |
| **License** | MIT |
| **Language** | JavaScript / TypeScript |
| **Package** | `get-shit-done-cc` (npm) |
| **Install** | `npx get-shit-done-cc@latest` |
| **GitHub** | [gsd-build/get-shit-done](https://github.com/gsd-build/get-shit-done) |
| **Stars** | ~49.9k |
| **Version** | v1.34.0+ |
| **Supported Tools** | 12+ (Claude Code, OpenCode, Gemini CLI, Kilo, Codex, Copilot, Cursor, Windsurf, Antigravity, Augment, Trae, Cline) |
| **Discord** | [discord.gg/mYgfVNfA2r](https://discord.gg/mYgfVNfA2r) |

---

## Philosophy & Core Principles

GSD is built on a clear anti-enterprise philosophy:

- **No enterprise theater** -- No sprint ceremonies, story points, stakeholder syncs, or retrospectives. Just commands that work.
- **Complexity in the system, not the workflow** -- Behind the scenes: context engineering, XML formatting, subagent orchestration. What you see: a few commands.
- **Context rot is the enemy** -- Quality degrades as the context window fills. GSD fights this by spawning fresh subagent contexts for every major task.
- **Brownfield-first** -- `/gsd-map-codebase` analyzes existing codebases before planning. Most work is on existing systems.
- **Trust the workflow** -- Built-in quality gates (plan-checker, verifier, UAT) catch problems automatically.
- **Solo-developer optimized** -- Designed for individuals and small teams who want to describe what they want and have it built correctly.

---

## Architecture Overview

![GSD Core Workflow](diagrams/gsd-core-workflow.drawio.svg)

### The Core Loop

GSD organizes work into **milestones** containing **phases**, where each phase follows a 5-step cycle:

```
/gsd-new-project
    |
    v
For each phase:
    /gsd-discuss-phase N    -- Lock in preferences
    /gsd-plan-phase N       -- Research + Plan + Verify
    /gsd-execute-phase N    -- Parallel wave execution
    /gsd-verify-work N      -- Manual UAT
    /gsd-ship N             -- Create PR
    |
    v
/gsd-complete-milestone
/gsd-new-milestone          -- Next version cycle
```

### Project Structure

```
.planning/
  PROJECT.md              # Project vision (always loaded)
  REQUIREMENTS.md         # Scoped v1/v2 requirements with IDs
  ROADMAP.md              # Phase breakdown with status tracking
  STATE.md                # Decisions, blockers, session memory
  config.json             # Workflow configuration
  MILESTONES.md           # Completed milestone archive
  HANDOFF.json            # Session handoff (from /gsd-pause-work)
  research/               # Domain research from /gsd-new-project
  todos/                  # Captured ideas
  threads/                # Persistent cross-session context
  seeds/                  # Forward-looking ideas with triggers
  codebase/               # Brownfield mapping (from /gsd-map-codebase)
  phases/
    XX-phase-name/
      XX-YY-PLAN.md       # Atomic execution plans (XML-structured)
      XX-YY-SUMMARY.md    # Execution outcomes
      CONTEXT.md           # Implementation preferences
      RESEARCH.md          # Ecosystem research findings
      VERIFICATION.md      # Post-execution verification
      XX-UI-SPEC.md        # UI design contract (frontend)
      XX-UI-REVIEW.md      # Visual audit scores (frontend)
```

---

## The 5-Step Phase Cycle

### Step 1: Discuss Phase (`/gsd-discuss-phase N`)

Captures your implementation preferences before anything gets researched or planned. The system identifies gray areas based on what's being built:

- **Visual features** -- Layout, density, interactions, empty states
- **APIs/CLIs** -- Response format, flags, error handling
- **Content systems** -- Structure, tone, depth, flow
- **Organization tasks** -- Grouping criteria, naming, exceptions

**Creates:** `{phase_num}-CONTEXT.md`

**Modes:**
- `discuss` (default) -- Interview-style questions
- `assumptions` -- Codebase-first: reads code, surfaces what it would do, asks you to correct

**Flags:** `--batch` (grouped questions), `--chain` (auto-chain into plan+execute)

### Step 2: Plan Phase (`/gsd-plan-phase N`)

The system:
1. **Researches** -- 4 parallel researcher agents investigate stack, features, architecture, pitfalls
2. **Plans** -- Creates 2-3 atomic task plans with XML structure
3. **Verifies** -- Plan-checker validates against requirements, loops until pass (max 3x)

Each plan is small enough to execute in a fresh context window. No degradation.

**Creates:** `{phase_num}-RESEARCH.md`, `{phase_num}-{N}-PLAN.md`

### Step 3: Execute Phase (`/gsd-execute-phase N`)

Plans execute in **waves** based on dependencies:

```
WAVE 1 (parallel)        WAVE 2 (parallel)        WAVE 3
Plan 01  |  Plan 02  ->  Plan 03  |  Plan 04  ->  Plan 05
User Model  Product Model   Orders API   Cart API    Checkout UI
```

- **Fresh 200K context** per plan -- zero accumulated garbage
- **Atomic git commits** per task -- each independently revertable
- **Wave-based parallelism** -- independent plans run simultaneously

**Creates:** `{phase_num}-{N}-SUMMARY.md`, `{phase_num}-VERIFICATION.md`

### Step 4: Verify Work (`/gsd-verify-work N`)

Manual User Acceptance Testing:
1. Extracts testable deliverables from the phase
2. Walks you through one at a time ("Can you log in with email?")
3. Diagnoses failures automatically via debug agents
4. Creates verified fix plans for re-execution

**Creates:** `{phase_num}-UAT.md`, fix plans if issues found

### Step 5: Ship (`/gsd-ship N`)

Creates a PR from verified phase work with auto-generated body.

---

## Multi-Agent Orchestration

![GSD Multi-Agent Architecture](diagrams/gsd-multi-agent.drawio.svg)

Every stage uses the same pattern: a thin orchestrator spawns specialized agents, collects results, and routes to the next step.

| Stage | Orchestrator | Agents |
|-------|-------------|--------|
| **Research** | Coordinates, presents findings | 4 parallel researchers (stack, features, architecture, pitfalls) |
| **Planning** | Validates, manages iteration | Planner creates plans, plan-checker verifies, loops until pass |
| **Execution** | Groups into waves, tracks progress | Executors implement in parallel, each with fresh 200K context |
| **Verification** | Presents results, routes next | Verifier checks codebase against goals, debuggers diagnose failures |

**Result:** An entire phase runs -- deep research, multiple plans, thousands of lines of code, automated verification -- and your main context window stays at 30-40%.

---

## Context Engineering

![GSD Context Engineering](diagrams/gsd-context-engineering.drawio.svg)

GSD's core innovation is managing context to prevent quality degradation:

| File | What it Does |
|------|-------------|
| `PROJECT.md` | Project vision, always loaded |
| `research/` | Ecosystem knowledge (stack, features, architecture, pitfalls) |
| `REQUIREMENTS.md` | Scoped v1/v2 requirements with phase traceability |
| `ROADMAP.md` | Where you're going, what's done |
| `STATE.md` | Decisions, blockers, position -- memory across sessions |
| `PLAN.md` | Atomic task with XML structure, verification steps |
| `SUMMARY.md` | What happened, what changed, committed to history |
| `todos/` | Captured ideas and tasks for later |
| `threads/` | Persistent context threads for cross-session work |
| `seeds/` | Forward-looking ideas that surface at the right milestone |

### XML Prompt Formatting

Every plan uses structured XML optimized for Claude:

```xml
<task type="auto">
  <name>Create login endpoint</name>
  <files>src/app/api/auth/login/route.ts</files>
  <action>
    Use jose for JWT (not jsonwebtoken - CommonJS issues).
    Validate credentials against users table.
    Return httpOnly cookie on success.
  </action>
  <verify>curl -X POST localhost:3000/api/auth/login returns 200 + Set-Cookie</verify>
  <done>Valid credentials return cookie, invalid return 401</done>
</task>
```

---

## Command Reference

### Core Workflow

| Command | Purpose |
|---------|---------|
| `/gsd-new-project [--auto]` | Full init: questions, research, requirements, roadmap |
| `/gsd-discuss-phase [N]` | Capture implementation decisions before planning |
| `/gsd-plan-phase [N]` | Research + plan + verify for a phase |
| `/gsd-execute-phase <N>` | Execute all plans in parallel waves |
| `/gsd-verify-work [N]` | Manual user acceptance testing |
| `/gsd-ship [N]` | Create PR from verified work |
| `/gsd-next` | Auto-detect and run next step |
| `/gsd-fast <text>` | Inline trivial tasks, skip planning |
| `/gsd-quick [--full]` | Ad-hoc task with GSD guarantees |

### Milestone Management

| Command | Purpose |
|---------|---------|
| `/gsd-audit-milestone` | Verify milestone achieved definition of done |
| `/gsd-complete-milestone` | Archive milestone, tag release |
| `/gsd-new-milestone [name]` | Start next version cycle |
| `/gsd-milestone-summary` | Generate comprehensive project summary |

### Phase Management

| Command | Purpose |
|---------|---------|
| `/gsd-add-phase` | Append phase to roadmap |
| `/gsd-insert-phase [N]` | Insert urgent work between phases |
| `/gsd-remove-phase [N]` | Remove future phase, renumber |
| `/gsd-list-phase-assumptions [N]` | Preview Claude's intended approach |
| `/gsd-plan-milestone-gaps` | Create phases for audit gaps |

### Brownfield

| Command | Purpose |
|---------|---------|
| `/gsd-map-codebase [area]` | Analyze existing codebase (4 parallel agents) |
| `/gsd-scan [--focus area]` | Quick single-focus codebase scan |
| `/gsd-intel [query]` | Queryable codebase intelligence index |

### Session Management

| Command | Purpose |
|---------|---------|
| `/gsd-pause-work` | Create handoff when stopping mid-phase |
| `/gsd-resume-work` | Restore from last session |
| `/gsd-progress` | Show status and next steps |
| `/gsd-session-report` | Generate session summary |

### Code Quality

| Command | Purpose |
|---------|---------|
| `/gsd-review` | Cross-AI peer review |
| `/gsd-code-review <N>` | Review source files changed in phase |
| `/gsd-code-review-fix <N>` | Auto-fix issues from review |
| `/gsd-secure-phase [N]` | Security enforcement with threat models |
| `/gsd-audit-uat` | Audit verification debt |
| `/gsd-docs-update` | Verified documentation generation |
| `/gsd-validate-phase [N]` | Retroactive test coverage audit |

### UI Design

| Command | Purpose |
|---------|---------|
| `/gsd-ui-phase [N]` | Generate UI design contract (UI-SPEC.md) |
| `/gsd-ui-review [N]` | Retroactive 6-pillar visual audit |

### Backlog, Threads & Seeds

| Command | Purpose |
|---------|---------|
| `/gsd-plant-seed <idea>` | Forward-looking ideas with trigger conditions |
| `/gsd-add-backlog <desc>` | Add to backlog parking lot (999.x numbering) |
| `/gsd-review-backlog` | Promote/keep/remove backlog items |
| `/gsd-thread [name]` | Persistent cross-session context threads |
| `/gsd-explore [topic]` | Socratic ideation before committing |

### Workstreams & Workspaces

| Command | Purpose |
|---------|---------|
| `/gsd-workstreams create <name>` | Namespaced parallel milestone work |
| `/gsd-workstreams switch <name>` | Switch active workstream |
| `/gsd-new-workspace` | Create isolated workspace with repo copies |

### Utilities

| Command | Purpose |
|---------|---------|
| `/gsd-settings` | Configure model profile and workflow agents |
| `/gsd-set-profile <profile>` | Switch model profile (quality/balanced/budget/inherit) |
| `/gsd-debug [desc]` | Systematic debugging with persistent state |
| `/gsd-do <text>` | Route freeform text to right command |
| `/gsd-health [--repair]` | Validate .planning/ directory integrity |
| `/gsd-forensics` | Post-mortem investigation of workflow failures |
| `/gsd-undo --last N` | Safe git revert using phase manifest |

---

## Configuration

### Core Settings

| Setting | Options | Default | Description |
|---------|---------|---------|-------------|
| `mode` | `yolo`, `interactive` | `interactive` | Auto-approve vs confirm at each step |
| `granularity` | `coarse`, `standard`, `fine` | `standard` | How finely scope is sliced (3-5 / 5-8 / 8-12 phases) |
| `model_profile` | `quality`, `balanced`, `budget`, `inherit` | `balanced` | Model tier for each agent |

### Model Profiles

| Profile | Planning | Execution | Verification |
|---------|----------|-----------|--------------|
| `quality` | Opus | Opus | Sonnet |
| `balanced` | Opus | Sonnet | Sonnet |
| `budget` | Sonnet | Sonnet | Haiku |
| `inherit` | Inherit | Inherit | Inherit |

### Workflow Agents (Toggleable)

| Setting | Default | What it Does |
|---------|---------|-------------|
| `workflow.research` | `true` | Domain investigation before planning |
| `workflow.plan_check` | `true` | Plan verification loop (max 3 iterations) |
| `workflow.verifier` | `true` | Post-execution verification |
| `workflow.nyquist_validation` | `true` | Maps test coverage to requirements pre-execution |
| `workflow.auto_advance` | `false` | Auto-chain discuss, plan, execute |
| `workflow.discuss_mode` | `discuss` | Interview vs assumptions-based discussion |
| `workflow.use_worktrees` | `true` | Git worktree isolation for execution |

### Git Branching Strategies

| Strategy | Description |
|----------|-------------|
| `none` (default) | Commits to current branch |
| `phase` | Branch per phase, merge at completion |
| `milestone` | Branch per milestone, merge at completion |

---

## Quality Gates

GSD includes 4 canonical gate types (v1.34+):

| Gate Type | When | What it Does |
|-----------|------|-------------|
| **Pre-flight** | Before execution | Plan-checker validates plans against requirements |
| **Revision** | During planning loop | Plan-checker feedback triggers plan revision (max 3x) |
| **Escalation** | After execution | Verifier surfaces issues for human review |
| **Abort** | Critical failure | Stops execution if fundamental problems detected |

### Built-in Detection

- **Schema drift detection** -- flags ORM changes missing migrations
- **Security enforcement** -- anchors verification to threat models
- **Scope reduction detection** -- prevents planner from silently dropping requirements
- **Nyquist validation** -- maps automated test coverage to each requirement before code is written

---

## Quick Mode

```
/gsd-quick
```

For ad-hoc tasks that don't need full planning:
- Same agents (planner + executor)
- Skips optional steps by default
- Separate tracking in `.planning/quick/`

**Composable flags:**
- `--discuss` -- Lightweight discussion first
- `--research` -- Spawn researcher before planning
- `--validate` -- Plan-checking + post-execution verification
- `--full` -- All phases (discussion + research + plan-checking + verification)

---

## Brownfield Workflow

```
/gsd-map-codebase           # 4 parallel agents analyze codebase
    |
    +-- Stack Mapper        -> codebase/STACK.md
    +-- Arch Mapper         -> codebase/ARCHITECTURE.md
    +-- Convention Mapper   -> codebase/CONVENTIONS.md
    +-- Concern Mapper      -> codebase/CONCERNS.md
    |
    v
/gsd-new-project            # Questions focus on what you're ADDING
```

---

## Speed vs Quality Presets

| Scenario | Mode | Granularity | Profile | Research | Plan Check | Verifier |
|----------|------|-------------|---------|----------|------------|----------|
| Prototyping | `yolo` | `coarse` | `budget` | off | off | off |
| Normal dev | `interactive` | `standard` | `balanced` | on | on | on |
| Production | `interactive` | `fine` | `quality` | on | on | on |

---

## UI Design System

GSD includes dedicated UI design commands:

- **`/gsd-ui-phase [N]`** -- Generates a UI-SPEC.md design contract covering spacing, typography, color, copywriting, and registry safety. Runs before planning for frontend phases.
- **`/gsd-ui-review [N]`** -- Retroactive 6-pillar visual audit (Copywriting, Visuals, Color, Typography, Spacing, Experience Design), each scored 1-4.

---

## Session Management

| Problem | Solution |
|---------|----------|
| Lost context / new session | `/gsd-resume-work` or `/gsd-progress` |
| Stopping mid-phase | `/gsd-pause-work` (writes HANDOFF.json) |
| Need session summary | `/gsd-session-report` |
| Don't know what's next | `/gsd-next` (auto-detect) |
| Context degradation | `/clear` then `/gsd-resume-work` |

---

## Security

GSD includes defense-in-depth security (v1.27+):

- **Path traversal prevention** -- All user-supplied file paths validated within project directory
- **Prompt injection detection** -- Centralized scanner for injection patterns in user text
- **PreToolUse prompt guard hook** -- Scans writes to `.planning/` for embedded injection vectors
- **Safe JSON parsing** -- Malformed arguments caught before state corruption
- **Shell argument validation** -- User text sanitized before shell interpolation
- **CI-ready injection scanner** -- Test suite scans all agent/command files for injection vectors

---

## Best Practices

### Planning

- **Always discuss before planning** -- `/gsd-discuss-phase` prevents Claude from making wrong assumptions. Most plan quality issues come from missing CONTEXT.md.
- **Keep plans atomic** -- 2-3 tasks per plan maximum. Larger plans exceed what a single context window can reliably produce.
- **Use assumptions mode** for established codebases -- codebase-first discussion is faster than open-ended questions.

### Execution

- **Clear context between major commands** -- `/clear` in Claude Code keeps quality high. GSD is designed around fresh contexts.
- **Use skip-permissions mode** -- `claude --dangerously-skip-permissions` is how GSD is intended to be used.
- **Vertical slices parallelize better** -- "User feature end-to-end" per plan is better than "all models in Plan 01, all APIs in Plan 02."

### Quality

- **Verify before shipping** -- `/gsd-verify-work` catches issues the automated verifier cannot.
- **Use `/gsd-code-review` after execution** -- Slots in between execute and verify for structured review.
- **Run `/gsd-audit-milestone` before completion** -- Catches requirement gaps and stubs.

### Model Selection

- **`balanced` for most work** -- Opus for planning (architecture decisions), Sonnet for execution and verification.
- **`budget` for familiar domains** -- Sonnet handles execution fine when patterns are established.
- **`inherit` for non-Anthropic providers** -- Avoids unexpected API costs with OpenRouter or local models.

---

## How GSD Compares

### vs Other SDD Tools

| Aspect | GSD | OpenSpec | SpecKit | BMAD |
|--------|-----|---------|---------|------|
| **Philosophy** | Anti-enterprise; complexity in system, not workflow | Fluid, no phase gates | Sequential checkpointed phases | Full SDLC with specialized agents |
| **Core Innovation** | Context rot prevention via subagent orchestration | Delta specs for brownfield | Constitution-first governance | 9 role-based AI agents |
| **Agent Model** | Multi-agent: researchers, planners, executors, verifiers | Generic AI assistant | Generic AI assistant | 9 named specialized agents |
| **Execution** | Parallel wave-based with fresh 200K contexts | Sequential apply | Sequential implement | Per-story build cycle |
| **Git Integration** | Atomic commits per task, branching strategies | Specs in directory, no branch mgmt | Feature branches per spec | No built-in git integration |
| **UI Design** | `/gsd-ui-phase` + `/gsd-ui-review` (6-pillar audit) | Not built-in | Not built-in | `bmad-ux-designer` agent |
| **Session Mgmt** | Pause/resume/handoff with STATE.md | None built-in | None built-in | Fresh chat per workflow |
| **Best For** | Solo devs / small teams wanting reliable AI output | Brownfield iteration | Greenfield compliance builds | Full product lifecycle |

---

## References

- [GSD GitHub Repository](https://github.com/gsd-build/get-shit-done)
- [GSD npm Package](https://www.npmjs.com/package/get-shit-done-cc)
- [GSD User Guide](https://github.com/gsd-build/get-shit-done/blob/main/docs/USER-GUIDE.md)
- [GSD Discord](https://discord.gg/mYgfVNfA2r)
- [GSD on X/Twitter](https://x.com/gsd_foundation)
- [Spec-Driven Development (SDD)](../spec-driven-development.md) -- General SDD methodology
- [OpenSpec Workflow](../openspec/OpenSpec-Workflow.md) -- Fission AI's SDD framework
- [SpecKit Workflow](../speckit/SpecKit-Workflow.md) -- GitHub's SDD toolkit
- [SDD Tools Comparison](../SDD-Tools-Comparison.md) -- Cross-tool comparison
