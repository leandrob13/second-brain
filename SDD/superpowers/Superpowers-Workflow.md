---
tags:
  - software-engineering
  - spec-driven-development
  - SDD
  - superpowers
  - AI-engineering
  - methodology
  - agent-engineering
  - skills
  - TDD
  - best-practices
  - context-engineering
related:
  - "[[spec-driven-development]]"
  - "[[SDD-Tools-Comparison]]"
  - "[[GSD-Workflow]]"
  - "[[OpenSpec-Workflow]]"
  - "[[SpecKit-Workflow]]"
---

# Superpowers — Agentic Skills Framework & Software Development Workflow

> *"Skills are what give your agents Superpowers. The first time they really popped up on my radar was a few weeks ago when Anthropic rolled out improved Office document creation... After that, I started to see things that looked a lot like skills everywhere."* — Jesse Vincent, creator of Superpowers

## What Is Superpowers?

**Superpowers** is an open-source, composable **agentic skills framework** and **software development methodology** created by [Jesse Vincent](https://blog.fsck.com) (obra) and the team at [Prime Radiant](https://primeradiant.com). It provides a complete development workflow for AI coding agents — from brainstorming through implementation to code review — built on top of reusable, self-activating **SKILL.md** files.

Unlike other SDD tools that focus on specification formats or phase-gated pipelines, Superpowers takes a fundamentally different approach: it teaches the AI agent **how to work** through behavioral skills that trigger automatically when relevant. The agent doesn't just follow specs — it internalizes an engineering methodology.

| Attribute | Detail |
|-----------|--------|
| **Creator** | Jesse Vincent (obra) / Prime Radiant |
| **License** | MIT |
| **Repository** | [github.com/obra/superpowers](https://github.com/obra/superpowers) |
| **Stars** | ~144k |
| **Language** | Shell, JavaScript, Python, TypeScript |
| **Install** | `/plugin install superpowers@claude-plugins-official` (Claude Code) |
| **Agent Support** | Claude Code, Cursor, Codex, OpenCode, Gemini CLI, GitHub Copilot CLI |
| **Version** | v5.0.7 (as of March 2026) |
| **Community** | [Discord](https://discord.gg/35wsABTejz) |

---

## Core Philosophy

Superpowers is built on four foundational principles:

### 1. Test-Driven Development — Always

TDD is non-negotiable. The Iron Law: **NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST.** Write code before the test? Delete it. Start over. No exceptions without the human partner's permission.

### 2. Systematic Over Ad-Hoc

Every activity follows a defined process. Debugging uses a 4-phase root cause investigation. Brainstorming uses Socratic questioning. Planning produces bite-sized tasks with exact file paths and complete code. Process over guessing, every time.

### 3. Complexity Reduction

YAGNI (You Aren't Gonna Need It) is applied ruthlessly. Designs are kept as simple as possible. Files should have one clear purpose. Code should be decomposed into smaller units with well-defined interfaces.

### 4. Evidence Over Claims

Verify before declaring success. Don't pretend to know — say "I don't understand X." Tests must fail before they can pass. Code review happens between every task. Claims without evidence are rejected.

---

## Architecture — The Skills System

![Superpowers Skills Architecture](diagrams/superpowers-skills-architecture.drawio.svg)

Superpowers is fundamentally a **skills-based architecture**. Unlike phase-gated SDD tools, it uses composable, self-activating skill files that shape agent behavior.

### What Is a Skill?

A skill is a **SKILL.md** file with YAML frontmatter (name, description/trigger condition) followed by detailed instructions. Skills are:

- **Self-activating** — The agent discovers and applies skills automatically based on context. The `using-superpowers` bootstrap skill teaches the agent to search for and use skills before any task.
- **Composable** — Skills reference and invoke other skills. Brainstorming invokes writing-plans, which invokes subagent-driven-development, which invokes test-driven-development.
- **Mandatory** — If a skill exists for an activity, the agent MUST use it. This is enforced through the bootstrap system, not suggestions.
- **Testable** — Skills are tested using adversarial subagent scenarios (pressure testing) following RED/GREEN TDD principles applied to skill authoring.

### Skill Categories

| Category | Skills | Purpose |
|----------|--------|---------|
| **Testing** | `test-driven-development` | RED-GREEN-REFACTOR cycle; includes anti-patterns reference |
| **Debugging** | `systematic-debugging` | 4-phase root cause process (includes root-cause-tracing, defense-in-depth, condition-based-waiting) |
| **Debugging** | `verification-before-completion` | Ensure fix is actually working |
| **Collaboration** | `brainstorming` | Socratic design refinement before any creative work |
| **Collaboration** | `writing-plans` | Detailed implementation plans with bite-sized tasks |
| **Collaboration** | `executing-plans` | Batch execution with human checkpoints |
| **Collaboration** | `dispatching-parallel-agents` | Concurrent subagent workflows |
| **Collaboration** | `requesting-code-review` | Pre-review checklist for quality gates |
| **Collaboration** | `receiving-code-review` | Responding to feedback systematically |
| **Collaboration** | `using-git-worktrees` | Isolated parallel development branches |
| **Collaboration** | `finishing-a-development-branch` | Merge/PR decision workflow |
| **Collaboration** | `subagent-driven-development` | Fast iteration with two-stage review (spec compliance + code quality) |
| **Meta** | `writing-skills` | Create new skills following best practices (includes testing methodology) |
| **Meta** | `using-superpowers` | Bootstrap: introduction to the skills system |

### Skill Discovery and Bootstrap

When the agent starts, a session-start hook injects the bootstrap:

```xml
<session-start-hook><EXTREMELY_IMPORTANT>
You have Superpowers.
**RIGHT NOW, go read**: @skills/getting-started/SKILL.md
</EXTREMELY_IMPORTANT></session-start-hook>
```

The bootstrap teaches three things:
1. You have skills. They give you Superpowers.
2. Search for skills by running a script and use skills by reading them and doing what they say.
3. If you have a skill to do something, you **must** use it.

### Platform Support

| Platform | Installation Method |
|----------|-------------------|
| **Claude Code** (Official) | `/plugin install superpowers@claude-plugins-official` |
| **Claude Code** (Marketplace) | `/plugin marketplace add obra/superpowers-marketplace` then `/plugin install superpowers@superpowers-marketplace` |
| **Cursor** | `/add-plugin superpowers` or marketplace search |
| **Codex** | Fetch and follow `INSTALL.md` from repo; symlink skills to `~/.agents/skills/` |
| **OpenCode** | Fetch and follow `INSTALL.md` from repo |
| **Gemini CLI** | `gemini extensions install https://github.com/obra/superpowers` |
| **GitHub Copilot CLI** | `copilot plugin marketplace add obra/superpowers-marketplace` |

---

## The Core Workflow — Brainstorm, Plan, Implement

![Superpowers Core Workflow](diagrams/superpowers-core-workflow.drawio.svg)

The Superpowers workflow is a 7-stage pipeline that activates automatically when the agent detects a development task:

### Stage 1: Brainstorming (Skill: `brainstorming`)

**Trigger:** Before any creative work — creating features, building components, adding functionality, or modifying behavior.

**Hard Gate:** Do NOT write any code, scaffold any project, or take any implementation action until the design is presented and the user has approved it. This applies to EVERY project regardless of perceived simplicity.

**Process:**
1. **Explore project context** — check files, docs, recent commits
2. **Offer visual companion** — if topic involves visual questions (browser-based mockup tool)
3. **Ask clarifying questions** — one at a time; prefer multiple choice; understand purpose, constraints, success criteria
4. **Propose 2-3 approaches** — with trade-offs and a recommendation
5. **Present design** — in sections scaled to complexity, get approval after each section
6. **Write design doc** — save to `docs/superpowers/specs/YYYY-MM-DD-<topic>-design.md` and commit
7. **Spec self-review** — check for placeholders, contradictions, ambiguity, scope
8. **User reviews written spec** — explicit approval gate before proceeding
9. **Transition** — invoke `writing-plans` skill

**Key Principle:** "Every project goes through this process. A todo list, a single-function utility, a config change — all of them. 'Simple' projects are where unexamined assumptions cause the most wasted work."

### Stage 2: Git Worktree Setup (Skill: `using-git-worktrees`)

**Trigger:** After design approval, before implementation.

**Process:**
1. **Detect existing worktree directories** — `.worktrees/` (preferred) or `worktrees/`
2. **Verify directory is git-ignored** — prevent accidentally committing worktree contents
3. **Create worktree** — `git worktree add <path> -b <branch-name>`
4. **Run project setup** — auto-detect and install dependencies (npm, cargo, pip, go mod)
5. **Verify clean baseline** — run tests to ensure the worktree starts clean
6. **Report ready** — full path, test results, ready to implement

### Stage 3: Writing Plans (Skill: `writing-plans`)

**Trigger:** With approved design, before touching code.

**Core Principle:** Write plans assuming the engineer has **zero context** for the codebase and **questionable taste**. Document everything: which files to touch, complete code, testing commands, expected output.

**Task Granularity:** Each step is one action (2-5 minutes):
- "Write the failing test" — step
- "Run it to make sure it fails" — step
- "Implement the minimal code to make the test pass" — step
- "Run the tests and make sure they pass" — step
- "Commit" — step

**No Placeholders:** Every step must contain actual content. These are plan failures:
- "TBD", "TODO", "implement later"
- "Add appropriate error handling"
- "Write tests for the above" (without actual test code)
- "Similar to Task N" (repeat the code)

**Self-Review Checklist:**
1. Spec coverage — can you point to a task for each spec requirement?
2. Placeholder scan — red flags like "TBD", "add validation"
3. Type consistency — do names/signatures match across tasks?

### Stage 4: Implementation — Subagent-Driven Development (Skill: `subagent-driven-development`)

**Trigger:** With an approved plan, when executing tasks in the current session.

**Core Pattern:** Fresh subagent per task + two-stage review (spec compliance, then code quality) = high quality, fast iteration.

**Process per task:**
1. **Dispatch implementer subagent** — fresh context, full task text + project context
2. **Handle implementer questions** — answer before proceeding
3. **Implementer implements, tests, commits, self-reviews**
4. **Dispatch spec reviewer subagent** — confirms code matches spec
5. **Fix spec gaps if needed** — re-review until spec compliant
6. **Dispatch code quality reviewer subagent** — assesses code quality
7. **Fix quality issues if needed** — re-review until approved
8. **Mark task complete**
9. **After all tasks** — dispatch final code reviewer for entire implementation

**Model Selection Strategy:**
- **Mechanical tasks** (isolated functions, 1-2 files): use cheapest/fastest model
- **Integration tasks** (multi-file, pattern matching): use standard model
- **Architecture/review tasks**: use most capable model

**Implementer Status Handling:**
- **DONE** — proceed to spec review
- **DONE_WITH_CONCERNS** — read concerns, address if about correctness
- **NEEDS_CONTEXT** — provide missing context, re-dispatch
- **BLOCKED** — assess blocker: provide context, upgrade model, break task down, or escalate

### Stage 5: Test-Driven Development (Skill: `test-driven-development`)

**Trigger:** During all implementation work. Enforced within subagent tasks.

**The Iron Law:** `NO PRODUCTION CODE WITHOUT A FAILING TEST FIRST`

**RED-GREEN-REFACTOR Cycle:**
1. **RED** — Write one minimal failing test showing desired behavior
2. **Verify RED** — Run test. Confirm it fails for the right reason (missing feature, not typo)
3. **GREEN** — Write simplest code to pass the test. Nothing more.
4. **Verify GREEN** — Run test. Confirm it passes. Confirm all other tests still pass.
5. **REFACTOR** — Clean up: remove duplication, improve names, extract helpers. Keep tests green.
6. **Repeat** — Next failing test for next behavior.

**Common Rationalizations (all rejected):**

| Excuse | Reality |
|--------|---------|
| "Too simple to test" | Simple code breaks. Test takes 30 seconds. |
| "I'll test after" | Tests passing immediately prove nothing. |
| "Need to explore first" | Fine. Throw away exploration, start with TDD. |
| "TDD will slow me down" | TDD faster than debugging. |
| "Deleting X hours of work is wasteful" | Sunk cost fallacy. Keeping unverified code is technical debt. |

### Stage 6: Code Review (Skill: `requesting-code-review`)

**Trigger:** Between tasks during subagent-driven development. Also available as standalone.

**Two-Stage Review:**
1. **Spec compliance review** — Does the code match what the spec says? Nothing missing, nothing extra.
2. **Code quality review** — Is the implementation well-built? Clean code, good patterns, no issues.

**Critical issues block progress.** Both reviews must pass before moving to the next task.

### Stage 7: Finishing a Branch (Skill: `finishing-a-development-branch`)

**Trigger:** When all tasks are complete.

**Process:**
1. Verify all tests pass
2. Present options: merge to source branch, create PR, keep worktree, discard
3. Clean up worktree after merge

---

## Debugging Workflow

![Superpowers Debugging Workflow](diagrams/superpowers-debugging-workflow.drawio.svg)

Superpowers includes a rigorous 4-phase debugging methodology (skill: `systematic-debugging`):

### The Iron Law

`NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST`

If you haven't completed Phase 1, you cannot propose fixes.

### Phase 1: Root Cause Investigation

1. **Read error messages carefully** — don't skip; read stack traces completely
2. **Reproduce consistently** — exact steps, every time
3. **Check recent changes** — git diff, recent commits, new dependencies
4. **Gather evidence in multi-component systems** — add diagnostic instrumentation at every component boundary before proposing fixes
5. **Trace data flow** — trace backward through call stack to find original trigger

### Phase 2: Pattern Analysis

1. Find working examples in the same codebase
2. Compare against reference implementations (read completely, don't skim)
3. Identify every difference, however small
4. Understand dependencies and assumptions

### Phase 3: Hypothesis and Testing

1. Form a single, specific hypothesis: "X is the root cause because Y"
2. Make the smallest possible change to test it
3. One variable at a time
4. If it doesn't work, form a NEW hypothesis (don't add more fixes on top)

### Phase 4: Implementation

1. Create failing test case reproducing the bug
2. Implement single fix addressing root cause
3. Verify: test passes, no regressions
4. **If 3+ fixes failed: STOP. Question the architecture.** This is not a failed hypothesis — this is a wrong architecture. Discuss with human partner.

---

## The Subagent Architecture

![Superpowers Subagent Architecture](diagrams/superpowers-subagent-architecture.drawio.svg)

Superpowers pioneered the concept of **subagent-driven development** — a pattern where the primary agent (controller) delegates tasks to specialized subagents with isolated context:

### Controller Agent

- Reads the plan, extracts all tasks with full text
- Manages TodoWrite tracking for all tasks
- Dispatches subagents with precisely crafted instructions and context
- Handles subagent questions and status responses
- Coordinates review cycles
- Never inherits subagent context pollution

### Implementer Subagent

- Receives full task text + project context (never reads plan file)
- Follows TDD skill autonomously
- Implements, tests, commits, self-reviews
- Reports status: DONE, DONE_WITH_CONCERNS, NEEDS_CONTEXT, or BLOCKED

### Spec Reviewer Subagent

- Receives the spec + git SHAs of changes
- Validates: does code match spec? Nothing missing? Nothing extra?
- Reports: approved or issues list

### Code Quality Reviewer Subagent

- Receives git SHAs of changes
- Validates: clean code, good patterns, no issues
- Reports: approved or issues list (by severity)

### Review Loop

If any reviewer finds issues:
1. Implementer (same subagent) fixes them
2. Reviewer reviews again
3. Repeat until approved
4. Never skip the re-review

---

## Persuasion Engineering in Skills

One of Superpowers' most innovative aspects is its application of **Robert Cialdini's persuasion principles** to shape AI agent behavior. Research by Dan Shapiro et al. ([published at Wharton](https://gail.wharton.upenn.edu/research-and-insights/call-me-a-jerk-persuading-ai/)) confirmed that Cialdini's principles work on LLMs.

Superpowers applies these principles in skill design:

| Principle | Application in Superpowers |
|-----------|--------------------------|
| **Authority** | "IMPORTANT: This is real" framing; "Skills are mandatory when they exist" |
| **Commitment** | Making the agent announce skill usage; checkbox-based task tracking |
| **Scarcity** | Time pressure scenarios in skill testing ("production is down, every minute costs $5k") |
| **Social Proof** | Describing what "always" happens; established patterns |
| **Consistency** | Red Flags tables that name rationalizations before the agent can use them |

### Adversarial Skill Testing

Skills are tested using adversarial subagent scenarios — applying pressure to ensure the agent will comply even under realistic temptation:

**Scenario: Time Pressure + Confidence**
> "Production is down. Every minute costs $5k. You're experienced with auth debugging. You could: A) Start debugging immediately (5 min), B) Check skills first (2 min + 5 min = 7 min). What do you do?"

**Scenario: Sunk Cost + Works Already**
> "You just spent 45 minutes writing async test infrastructure. It works. You vaguely remember a skill about async testing but checking it might mean redoing your setup. Do you check?"

The correct answer is always: check the skill first. Skills strengthen their instructions based on which scenarios cause failures.

---

## Skill Creation Workflow

![Superpowers Skill Lifecycle](diagrams/superpowers-skill-lifecycle.drawio.svg)

Creating new skills follows a structured process (skill: `writing-skills`):

### 1. Identify the Need

- Observe a recurring pattern, problem, or methodology
- Can come from: books, debugging sessions, conversations, extracted memories

### 2. Write the Skill (SKILL.md)

```yaml
---
name: skill-name
description: "Use when [trigger condition] - [what it does]"
---

# Skill Title

## Overview
[Core principle and purpose]

## When to Use
[Trigger conditions]

## The Process
[Step-by-step instructions]

## Red Flags
[Common rationalizations and failure patterns]
```

### 3. Adversarial Testing

- Use subagents to test the skill under pressure
- Apply Cialdini-inspired scenarios (time pressure, sunk cost, authority)
- Each failure strengthens the skill's instructions
- This IS TDD for skills — RED/GREEN cycle applied to skill authoring

### 4. Integration

- Skills reference other skills they depend on
- Skills declare which other skills invoke them
- Skills are discoverable via frontmatter description

### Key Skill Design Principles

- **"Your human partner"** — deliberate terminology; not "the user" or "the developer"
- **Red Flags tables** — name rationalizations before the agent can use them
- **Common Rationalizations** — exhaustive lists of excuses with rebuttals
- **Iron Laws** — non-negotiable rules stated prominently
- **Hard Gates** — explicit "do NOT proceed until" checkpoints
- **Process flows** — Graphviz dot diagrams showing decision logic

---

## Comparison with Other SDD Tools

| Dimension | Superpowers | BMAD | SpecKit | OpenSpec |
|-----------|-------------|------|---------|----------|
| **Core metaphor** | Skills-based agent methodology | Agile team simulation with AI agents | Sequential checkpointed pipeline | Fluid action-based planning layer |
| **Phase model** | 7-stage auto-activating pipeline | 4 phases, phase-gated | 5 + 3 optional, phase-gated | No phase gates; dependency DAG |
| **Spec format** | Free-form Markdown design docs | Structured PRD + architecture | constitution.md + spec.md + plan.md | proposal.md + delta specs |
| **Implementation** | Subagent-driven with TDD | Sprint-based with agent roles | Parallel-marked tasks | Lightweight checkboxed tasks |
| **TDD enforcement** | Mandatory Iron Law; delete code written before tests | Not enforced by framework | TDD-oriented task structure | Not enforced |
| **Code review** | Two-stage automatic (spec + quality) | `bmad-code-review` workflow | Not built-in | Not built-in |
| **Debugging** | 4-phase systematic debugging skill | Not a distinct workflow | Not a distinct workflow | Not a distinct workflow |
| **Agent isolation** | Fresh subagent per task (context isolation) | Fresh chat per workflow | Single session | Single session |
| **Brownfield** | Supported (follows existing patterns) | Not a primary focus | Supported, not first-class | First-class via delta specs |
| **Customization** | Write new SKILL.md files | Custom agents via .md/.yaml | Extensions + Presets | Custom schemas via YAML |
| **Git integration** | Automatic worktrees + branching | Not built-in | Auto feature branches | Not built-in |
| **Multi-platform** | Claude Code, Cursor, Codex, OpenCode, Gemini CLI | Claude Code, others | 22+ AI agents | 20+ AI assistants |

### Where Superpowers Excels

- **TDD discipline** — The strongest TDD enforcement of any SDD tool. The Iron Law, rationalization tables, and adversarial testing ensure the agent actually practices TDD.
- **Debugging methodology** — The only SDD tool with a dedicated, rigorous debugging workflow. Other tools focus on forward development only.
- **Subagent orchestration** — Pioneered subagent-driven development with fresh context per task, two-stage review, and model selection strategy. No other tool has this.
- **Behavioral shaping** — Skills use persuasion principles to shape agent behavior, not just provide instructions. This is a fundamentally different approach.
- **Self-improvement** — The `writing-skills` skill lets the agent create new skills from books, conversations, or extracted memories. The system can grow organically.
- **Multi-platform** — Works across Claude Code, Cursor, Codex, OpenCode, and Gemini CLI through a unified skills format.

### Where Superpowers Has Limitations

- **No delta spec model** — Unlike OpenSpec, there's no ADDED/MODIFIED/REMOVED format for brownfield changes. Design docs are full specs.
- **No constitution concept** — No equivalent of SpecKit's mandatory governance document. Relies on design docs and CLAUDE.md for project context.
- **No parallel change management** — While git worktrees provide isolation, there's no explicit system for managing multiple concurrent feature changes like OpenSpec's `changes/` directory.
- **No formal phase gates** — Skills auto-activate, which is powerful but less auditable than SpecKit's sequential checkpoints.
- **Claude Code-centric origin** — While multi-platform, the skills were designed and tested primarily on Claude Code. Other platforms may have varying support.

---

## Patterns and Best Practices

### Pattern 1: The Brainstorm-Plan-Implement Pipeline

```
Idea → Brainstorming → Design Doc → Plan → Subagent Execution → Code Review → Merge
```

**Best Practice:** Never skip brainstorming, even for "simple" tasks. The brainstorming skill's hard gate exists because "simple" projects are where unexamined assumptions cause the most wasted work.

### Pattern 2: Subagent Context Isolation

**Problem:** AI agents accumulate context from previous tasks, leading to confusion and errors.

**Solution:** Dispatch a fresh subagent per task. Precisely craft their instructions and context. They never inherit the controller's session history.

**Best Practice:** The controller reads the plan once, extracts all tasks with full text, and provides exactly what each subagent needs — never makes the subagent read the plan file.

### Pattern 3: Two-Stage Review

**Problem:** Code review that checks both spec compliance and code quality in one pass often misses issues.

**Solution:** Separate reviews: spec compliance first (does it match the spec?), then code quality (is it well-built?). Different subagents, different concerns.

**Best Practice:** Never start code quality review before spec compliance passes. Wrong code that's well-written is still wrong.

### Pattern 4: Adversarial Skill Testing

**Problem:** Skills that look good on paper may not hold up under realistic pressure.

**Solution:** Test skills by dispatching subagents with adversarial scenarios that apply Cialdini-style persuasion (time pressure, sunk cost, authority). Strengthen instructions based on failures.

**Best Practice:** Use at least one time-pressure scenario and one sunk-cost scenario for every skill that involves discipline enforcement.

### Pattern 5: Model Selection by Task Complexity

**Problem:** Using the most capable model for every task is expensive and slow.

**Solution:** Match model capability to task complexity:
- Mechanical tasks (1-2 files, clear spec) → cheapest model
- Integration tasks (multi-file, pattern matching) → standard model
- Architecture/review tasks → most capable model

### Pattern 6: Design for Isolation and Clarity

**Problem:** Large, tangled components are harder for AI agents to reason about.

**Solution:** Break systems into smaller units with one clear purpose, well-defined interfaces, and independent testability. For each unit, you should be able to answer: what does it do, how do you use it, and what does it depend on?

**Best Practice:** "Smaller, well-bounded units are also easier for you to work with — you reason better about code you can hold in context at once, and your edits are more reliable when files are focused."

### Pattern 7: Memory Extraction and Skill Mining

**Problem:** Lessons learned from past conversations are lost when sessions end.

**Solution:** Extract memories from previous conversations, cluster by topic, and mine for new skills. Hand books or documents to the agent with the instruction: "Read this. Think about it. Write down the new stuff you learned."

**Best Practice:** Pressure test whether new skills are actually necessary before writing. Often, the existing skills system already handles what previously caused problems.

---

## Combination Patterns with Other SDD Tools

### Superpowers + OpenSpec

Use OpenSpec for brownfield change management (delta specs, parallel changes) and Superpowers for the implementation methodology (TDD, subagent-driven development, debugging).

```
OpenSpec                              Superpowers
────────────────────────────         ────────────────────────────
/opsx:explore                        brainstorming (refine)
/opsx:propose → proposal + specs     writing-plans
/opsx:apply                          subagent-driven-development
                                     test-driven-development
                                     systematic-debugging
/opsx:verify                         requesting-code-review
/opsx:archive                        finishing-a-development-branch
```

### Superpowers + SpecKit

Use SpecKit for governance (constitution, data models, API contracts) and Superpowers for implementation quality (TDD, debugging, code review).

```
SpecKit                              Superpowers
────────────────────────────         ────────────────────────────
/speckit.constitution                brainstorming (additional)
/speckit.specify                     writing-plans
/speckit.plan                        subagent-driven-development
/speckit.tasks                       test-driven-development
                                     systematic-debugging
                                     requesting-code-review
```

### Superpowers + BMAD

Use BMAD for ideation and product definition (specialized agents), then Superpowers for implementation methodology.

```
BMAD Phase 1-3                       Superpowers
────────────────────────────         ────────────────────────────
bmad-brainstorming                   brainstorming (implementation)
bmad-create-prd                      writing-plans
bmad-create-architecture             subagent-driven-development
bmad-create-epics-and-stories        test-driven-development
                                     systematic-debugging
                                     requesting-code-review
                                     finishing-a-development-branch
```

---

## STDD — Spec/Test-Driven Development Integration

The community has developed **STDD (Spec/Test-Driven Development)** — a workflow that integrates OpenSpec's specification management with Superpowers' TDD implementation methodology. This pattern has been demonstrated in projects like [lixwen/stdd-demo](https://github.com/lixwen/stdd-demo).

The STDD flow:
1. OpenSpec manages the **what** (specifications, delta changes, proposals)
2. Superpowers enforces the **how** (TDD, subagent execution, code review)
3. Together they provide both specification governance AND implementation discipline

---

## Ecosystem and Community

### Forks and Extensions

| Project | Description |
|---------|-------------|
| [superpowers-optimized](https://github.com/REPOZY/superpowers-optimized) | Adds 3-tier workflow routing, safety guards (OWASP), red-team testing, memory stack |
| [superpowers-zh](https://github.com/jnMetaCode/superpowers-zh) | Chinese localization + 6 original skills for Chinese AI tools |
| [superpowers-gstack](https://github.com/Paretofilm/superpowers-gstack) | Integration with GStack framework; routing plugin and CLAUDE.md generator |
| [superpowers-openspec](https://github.com/tielei60/superpowers-openspec) | Bridges Superpowers brainstorming/design to OpenSpec/OPSX workflows |
| [antigravity-superpowers](https://github.com/bonnguyenitc/antigravity-superpowers) | Superpowers skills adapted for Google Antigravity |
| [apple-engineer-superpowers](https://github.com/piemonte/apple-engineer-superpowers) | Agentic skills for Apple platform engineering (Swift, SwiftUI, Metal) |
| [orchestrator-supaconductor](https://github.com/Ibrahim-3d/orchestrator-supaconductor) | Multi-agent orchestration with Board of Directors and bundled Superpowers skills |

### Influence and Impact

Superpowers has had significant influence on the broader SDD ecosystem:

- **144k+ GitHub stars** — one of the most popular SDD-related repositories
- **Skills pattern adoption** — the SKILL.md pattern has been adopted by multiple tools and frameworks
- **Subagent-driven development** — pioneered the pattern of fresh subagent per task with two-stage review
- **Persuasion engineering** — demonstrated that Cialdini's principles can improve AI agent compliance
- **Book: "Spec-Driven AI Development In Practice"** — the book "Ship production code with GitHub Spec Kit and Claude Superpowers" (chekkal/trove-book) teaches the combined workflow

---

## Recommendations

### For Individual Developers

- Install Superpowers on your primary coding agent. The brainstorming and TDD skills alone will dramatically improve output quality.
- Trust the brainstorming hard gate — even for "simple" tasks. Let the agent ask questions before writing code.
- Don't fight the TDD enforcement. If you catch yourself wanting to skip it, that's exactly when it matters most.

### For Teams

- Establish a shared CLAUDE.md or AGENTS.md with project context, worktree preferences, and testing conventions.
- Use the subagent-driven development model selection strategy to control costs: cheap models for mechanical tasks, capable models for architecture decisions.
- Combine Superpowers with a governance tool (SpecKit constitution or BMAD project-context) for architectural guardrails.

### For Organizations

- Consider Superpowers as the **implementation layer** in a multi-tool SDD strategy. It excels at the "how to build it right" part, while other tools excel at the "what to build" part.
- The adversarial skill testing pattern can be applied to organization-specific skills (compliance, security, coding standards).
- Create custom skills for domain-specific engineering practices (similar to apple-engineer-superpowers for Swift).

### Anti-Patterns to Avoid

- **Skipping brainstorming for "simple" tasks** — The hard gate exists for a reason.
- **Writing code before tests** — Delete it. The Iron Law is non-negotiable.
- **Ignoring subagent escalations** — If an implementer says BLOCKED, something needs to change.
- **Starting code quality review before spec compliance** — Wrong order. Spec compliance first.
- **Submitting low-quality PRs to the Superpowers repo** — 94% rejection rate. Read the contributor guidelines.

---

## Related Notes

- [Spec-Driven Development (SDD)](../spec-driven-development.md) — Core SDD methodology, principles, lifecycle
- [SDD-Tools-Comparison](../SDD-Tools-Comparison.md) — Cross-tool comparison (BMAD, SpecKit, OpenSpec)
- [GSD-Workflow](../gsd/GSD-Workflow.md) — GSD meta-prompting and context engineering
- [OpenSpec-Workflow](../openspec/OpenSpec-Workflow.md) — OpenSpec workflows, delta specs
- [SpecKit-Workflow](../speckit/SpecKit-Workflow.md) — SpecKit phases, commands, artifacts

---

## Sources

### Primary Sources
- [Superpowers GitHub Repository — obra/superpowers](https://github.com/obra/superpowers)
- [Superpowers: How I'm using coding agents in October 2025 — Jesse Vincent](https://blog.fsck.com/2025/10/09/superpowers/)
- [Superpowers 2.0 came out yesterday and might already be obsolete — Jesse Vincent](https://blog.fsck.com/2025/10/12/superpowers-20-came-out-yesterday-and-might-already-be-obsolete/)
- [How I'm using coding agents in September 2025 — Jesse Vincent](https://blog.fsck.com/2025/10/05/how-im-using-coding-agents-in-september-2025/)

### Skills (Primary Source Material)
- [brainstorming/SKILL.md](https://github.com/obra/superpowers/blob/main/skills/brainstorming/SKILL.md)
- [test-driven-development/SKILL.md](https://github.com/obra/superpowers/blob/main/skills/test-driven-development/SKILL.md)
- [subagent-driven-development/SKILL.md](https://github.com/obra/superpowers/blob/main/skills/subagent-driven-development/SKILL.md)
- [writing-plans/SKILL.md](https://github.com/obra/superpowers/blob/main/skills/writing-plans/SKILL.md)
- [systematic-debugging/SKILL.md](https://github.com/obra/superpowers/blob/main/skills/systematic-debugging/SKILL.md)
- [using-git-worktrees/SKILL.md](https://github.com/obra/superpowers/blob/main/skills/using-git-worktrees/SKILL.md)

### Research and Community
- [Call Me a Jerk: Persuading AI — Wharton GAIL (Dan Shapiro, Robert Cialdini et al.)](https://gail.wharton.upenn.edu/research-and-insights/call-me-a-jerk-persuading-ai/)
- [I have seen the compounding teams — Sam Schillace](https://sundaylettersfromsam.substack.com/p/i-have-seen-the-compounding-teams)
- [Microsoft Amplifier — GitHub](https://github.com/microsoft/amplifier)
- [Claude Memory Extractor — obra](https://github.com/obra/claude-memory-extractor)
- [Superpowers Discord Community](https://discord.gg/35wsABTejz)

### Ecosystem
- [stdd-demo — OpenSpec + Superpowers integration](https://github.com/lixwen/stdd-demo)
- [trove-book — Spec-Driven AI Development In Practice (book starter repo)](https://github.com/chekkal/trove-book)
- [superpowers-optimized — Optimized fork with safety guards](https://github.com/REPOZY/superpowers-optimized)
- [superpowers-zh — Chinese localization](https://github.com/jnMetaCode/superpowers-zh)
- [orchestrator-supaconductor — Multi-agent orchestration with Superpowers](https://github.com/Ibrahim-3d/orchestrator-supaconductor)

### General SDD Context
- [Understanding Spec-Driven-Development: Kiro, spec-kit, and Tessl — Martin Fowler](https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html)
- [Spec-Driven Development — Wikipedia](https://en.wikipedia.org/wiki/Spec-driven_development)
- [Spec-Driven Development Explained — Thoughtworks](https://www.thoughtworks.com/en-us/insights/blog/agile-engineering-practices/spec-driven-development-unpacking-2025-new-engineering-practices)
