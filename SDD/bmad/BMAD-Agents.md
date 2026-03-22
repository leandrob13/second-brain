# BMAD Agents Reference

#bmad #agents #reference #sdd

> **Source:** [Agents | BMAD Method](https://docs.bmad-method.org/reference/agents/)

## Overview

The BMAD framework ships with **9 default agents**, each representing a specific role in the software development lifecycle. Agents are invoked using their **Skill ID** (e.g., `bmad-pm`) or via short **trigger codes** from within an active agent's menu.

---

## Agent Roster

### 1. Analyst — *Mary*

- **Skill ID:** `bmad-analyst`
- **Trigger Codes:** `BP`, `RS`, `CB`, `DP`
- **Role:** Initial project exploration, competitive analysis, market/technical research, and product brief creation.

| Trigger | Workflow | Output |
|---|---|---|
| `BP` | `bmad-brainstorming` | Brainstorming report |
| `RS` | `bmad-research` | Research findings |
| `CB` | `bmad-create-product-brief` | `product-brief.md` |
| `DP` | `bmad-generate-project-context` | `project-context.md` |

---

### 2. Product Manager — *John*

- **Skill ID:** `bmad-pm`
- **Trigger Codes:** `CP`, `VP`, `EP`, `CE`, `IR`, `CC`
- **Role:** Manages product requirements, epic/story creation, and implementation readiness reviews.

| Trigger | Workflow | Output |
|---|---|---|
| `CP` | `bmad-create-prd` | `PRD.md` |
| `VP` | `bmad-validate-prd` | Validation report |
| `EP` | `bmad-edit-prd` | Updated `PRD.md` |
| `CE` | `bmad-create-epics-and-stories` | Epic + story files |
| `IR` | `bmad-check-implementation-readiness` | PASS / CONCERNS / FAIL |
| `CC` | `bmad-correct-course` | Updated plan |

---

### 3. Architect — *Winston*

- **Skill ID:** `bmad-architect`
- **Trigger Codes:** `CA`, `IR`
- **Role:** Designs system architecture and validates implementation feasibility.

| Trigger | Workflow | Output |
|---|---|---|
| `CA` | `bmad-create-architecture` | `architecture.md` + ADRs |
| `IR` | `bmad-check-implementation-readiness` | PASS / CONCERNS / FAIL |

---

### 4. UX Designer — *Sally*

- **Skill ID:** `bmad-ux-designer`
- **Trigger Codes:** `CU`
- **Role:** Produces user experience designs and interface specifications.

| Trigger | Workflow | Output |
|---|---|---|
| `CU` | `bmad-create-ux-design` | `ux-spec.md` |

---

### 5. Scrum Master — *Bob*

- **Skill ID:** `bmad-sm`
- **Trigger Codes:** `SP`, `CS`, `ER`, `CC`
- **Role:** Facilitates sprint ceremonies, story creation, and manages workflow adjustments.

| Trigger | Workflow | Output |
|---|---|---|
| `SP` | `bmad-sprint-planning` | `sprint-status.yaml` |
| `CS` | `bmad-create-story` | `story-[slug].md` |
| `ER` | `bmad-retrospective` | Retrospective notes |
| `CC` | `bmad-correct-course` | Updated sprint plan |

---

### 6. Developer — *Amelia*

- **Skill ID:** `bmad-dev`
- **Trigger Codes:** `DS`, `CR`
- **Role:** Executes development tasks and performs code reviews.

| Trigger | Workflow | Output |
|---|---|---|
| `DS` | `bmad-dev-story` | Working code + tests |
| `CR` | `bmad-code-review` | Review approval / feedback |

---

### 7. QA Engineer — *Quinn*

- **Skill ID:** `bmad-qa`
- **Trigger Codes:** `QA`
- **Role:** Lightweight test automation agent; generates tests for existing features.

| Trigger | Workflow | Output |
|---|---|---|
| `QA` | `bmad-automate` | Test suite files |

---

### 8. Quick Flow Solo Dev — *Barry*

- **Skill ID:** `bmad-master`
- **Trigger Codes:** `QD`, `CR`
- **Role:** Streamlined combined agent for solo developers on the Quick Flow track.

| Trigger | Workflow | Output |
|---|---|---|
| `QD` | `bmad-quick-dev` | `tech-spec.md` + code |
| `CR` | `bmad-code-review` | Review approval / feedback |

---

### 9. Technical Writer — *Paige*

- **Skill ID:** `bmad-tech-writer`
- **Trigger Codes:** `DP`, `WD`, `US`, `MG`, `VD`, `EC`
- **Role:** Creates documentation, diagrams, and technical standards.

| Trigger | Type | Workflow | Output |
|---|---|---|---|
| `DP` | Workflow | `bmad-document-project` | Project docs |
| `WD` | Conversational | `bmad-write-document` | Custom document |
| `US` | Conversational | `bmad-update-standards` | Updated standards |
| `MG` | Conversational | `bmad-mermaid-generate` | Mermaid diagrams |
| `VD` | Workflow | `bmad-validate-doc` | Doc validation report |
| `EC` | Conversational | `bmad-explain-concept` | Concept explanation |

---

## Trigger Types

- **Workflow Triggers** — Launch a structured workflow with no initial arguments. The agent prompts you step by step (e.g., `CP`, `DS`, `CA`).
- **Conversational Triggers** — Free-form interactions where you provide context alongside the trigger code (e.g., `WD`, `MG`, `EC`).

---

## Custom Agents

You can define your own agents by creating `.md` or `.yaml` files in `_bmad/agents/`. Once saved, custom agents can be invoked like any default agent and participate in workflows.

---

## Related Notes

- [[BMAD-Getting-Started]]
- [[BMAD-Workflows-Reference]]
- [[SDD-Index]]
