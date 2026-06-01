---
name: define-product
description: "Create or update a Product Requirements Document (PRD) — the authoritative source for what and why. Modular folder structure with vision, stories, requirements, and context. Use when defining product requirements, writing user stories, updating business goals, or kicking off a new feature with structured product thinking."
license: MIT
trigger: "/define-product, create PRD, update PRD, product requirements, user stories, product vision, requirements document, feature kickoff, define scope, business goals, PRD folder"
metadata:
  author: Joel Abreu Rojas
  version: "1.0.0"
  ssot_chapter: "07: PRD Standards"
  porter_note: "PRD structure is universal — adapt folder names and section depth to your organization."
---

# Define Product (PRD)

## Philosophy

The PRD defines **what** the system should do and **why** — never **how**. It is the contract between product and engineering. When technical design and business intent diverge, the PRD is the authority that resolves the conflict.

A PRD lives as a **modular folder**, not a single monolith. Each file covers one concern: vision, stories, requirements, context. This makes changes atomic — updating a single user story touches only one file, producing clean diffs and focused reviews. The folder structure enforces separation of concerns at the document level.

The PRD is a **living document**, but it has teeth: once a module enters the "NOW" column of the Roadmap, its requirements are **Locked**. This prevents scope creep during active development. If a requirement change is needed after lock, it must go through a formal PR process with status flip (Proposed → Accepted).

Traceability is non-negotiable. Every user story links to a business goal. Every RFD section traces back to a PRD story. Every view file references a story ID. If you cannot trace an artifact back to a PRD requirement, that artifact has no justification.

---

## Anti-Patterns

### 1. Mixing "What" and "How"

Writing implementation details in the PRD. "Use a Redis cache for session storage" is a how — it belongs in the RFD. "Users must experience sub-200ms page loads" is a what — it belongs in the PRD. When you find yourself naming technologies, frameworks, or file paths in the PRD, stop. Ask: "Is this a user need or an engineering decision?"

**Fix**: Move technical decisions to the RFD. Keep the PRD focused on behavior, goals, and constraints.

### 2. Skipping Non-Functional Requirements

Writing only functional stories (features, behaviors) and ignoring performance, security, scalability, and reliability targets. Non-functional requirements are the invisible architecture constraints. Without them, the RFD has no budget to design against.

**Fix**: Every PRD must include non-functional requirements in `03_requirements.md`. Define response time targets, security posture, scalability expectations, and reliability SLAs — even if they are aspirational.

### 3. Leaving Status as "Proposed" After Merge

Failing to flip the README status from "Proposed" to "Accepted" when the PR is approved. An eternal "Proposed" status signals either indecision or broken process. It erodes trust in the PRD as an authoritative source.

**Fix**: The "Accepted" flip is part of the merge ceremony. PR approved → status flipped to Accepted → Squash and Merge. No exceptions.

### 4. No Traceability to Business Goals

User stories that describe features without linking to a persona or business objective. Without traceability, you cannot justify the work, prioritize effectively, or measure impact after delivery.

**Fix**: Every user story in `02_user_stories.md` must reference a persona from `01_vision.md` and a business goal from the Executive Summary.

### 5. Single Monolith Document

Writing the entire PRD in one large markdown file. This creates merge conflicts, makes focused reviews impossible, and discourages updates because "touching the PRD" means editing a 500-line file.

**Fix**: Use the modular folder structure: `README.md`, `01_vision.md`, `02_user_stories.md`, `03_requirements.md`, `04_context.md`. Each file covers one concern.

### 6. Vague Acceptance Criteria

Writing acceptance criteria that are subjective or untestable. "The UI should be intuitive" is not an acceptance criterion — it is an opinion. "A new user can complete checkout in under 3 clicks without help documentation" is an acceptance criterion — it is testable.

**Fix**: Every criterion must be pass/fail. If you cannot write a test for it, rewrite until you can.

---

## Workflow

### Phase 1 — Initialize the PRD Folder

Create the modular PRD structure:

```
docs/PRD/
├── README.md              # Entry point + Executive Summary + Status
├── 01_vision.md           # Product vision, problem, personas
├── 02_user_stories.md     # User narratives + Acceptance Criteria
├── 03_requirements.md     # Functional + Non-Functional requirements
└── 04_context.md          # Constraints, risks, success metrics (KPIs)
```

- [ ] Create `docs/PRD/` directory
- [ ] Initialize `README.md` with Executive Summary, project description, modular index, and **Current Logical State** = `Proposed`
- [ ] Initialize each module file with section headers matching the structure above
- [ ] Verify no file exceeds 200 lines — if it does, split by concern

### Phase 2 — Write the Vision

Define the "why" in `01_vision.md`:

- [ ] State the **problem** being solved (one paragraph, no solutions)
- [ ] Define **User Personas** — who we are building for, their goals, their pain points
- [ ] Articulate the **product vision** — what success looks like from the user's perspective
- [ ] Link the vision to business objectives (if applicable)

**Quality check**: Can a new team member read only this file and understand *why* this project exists? If not, rewrite.

### Phase 3 — Draft User Stories

Write user-centric narratives in `02_user_stories.md`:

- [ ] Each story follows: "As a [persona], I want [action], so that [outcome]"
- [ ] Each story has **Acceptance Criteria** — objective, testable conditions for "done"
- [ ] Stories are traceable to personas defined in `01_vision.md`
- [ ] Stories are prioritized (MoSCoW or numerical priority)
- [ ] No story mixes multiple unrelated behaviors — if you need "and," split it

**Story splitting heuristic**: If a story takes more than one PR to implement, it is too large. Split by user value, not by technical layer.

### Phase 4 — Define Requirements

Catalog all requirements in `03_requirements.md`:

**Functional Requirements:**
- [ ] Specific capabilities the system must have
- [ ] Each requirement maps to one or more user stories
- [ ] Requirements are testable — you can verify them with a pass/fail check

**Non-Functional Requirements:**
- [ ] **Performance**: Response time targets, throughput expectations
- [ ] **Security**: Authentication posture, data classification, compliance needs
- [ ] **Scalability**: User count targets, data volume projections
- [ ] **Reliability**: Uptime SLAs, recovery time objectives (RTO/RPO)
- [ ] **Accessibility**: WCAG compliance level, assistive technology support

**Quality check**: Can you write a test for every functional requirement? If not, the requirement is too vague. Rewrite until it is testable.

### Phase 5 — Document Context and Constraints

Capture external factors in `04_context.md`:

- [ ] **Project Constraints**: Timeline, budget, team size, technology mandates
- [ ] **Known Risks**: What could go wrong, with mitigation plans for each
- [ ] **Success Metrics (KPIs)**: How you will measure impact after delivery
- [ ] **Dependencies**: External systems, team dependencies, third-party services
- [ ] **Out of Scope**: What this project explicitly does NOT do (prevents scope creep)

**Risk mitigation format**: Each risk needs a concrete action, not "we'll figure it out." Bad: "Performance might be an issue." Good: "If response times exceed 200ms under load, we add a caching layer (documented in RFD `05_ops_and_security.md`)."

### Phase 6 — Handle the Ripple Effect

When the PRD changes after initial authoring, check downstream impacts:

- [ ] **Roadmap check**: Does the change affect the module's position or priority?
- [ ] **RFD check**: Does the change invalidate technical design decisions?
- [ ] **Views check**: Does the change affect UI specifications or user flows?
- [ ] **Stakeholder notification**: If the change affects scope or timeline, notify affected parties
- [ ] **Status flip**: If changing a content file, set README status back to `Proposed`

**The Ripple Effect rule**: A PRD change is never isolated. Every requirement change propagates to the RFD, Views, and potentially the Roadmap. Check all three before merging.

### Phase 7 — Set Status and Validate

Finalize the PRD before handing off to technical design:

- [ ] All 5 files are populated and internally consistent
- [ ] Executive Summary in `README.md` is accurate and current
- [ ] Status in `README.md` = `Proposed` (ready for review)
- [ ] Every user story has Acceptance Criteria
- [ ] Non-functional requirements have measurable targets
- [ ] Risk mitigations are concrete, not "we'll figure it out"
- [ ] Every user story references a persona from `01_vision.md`
- [ ] No file exceeds 200 lines — if it does, split by concern
- [ ] The PRD is ready to hand off to `specify-solution` for technical design

**Pre-handoff review**: Read the PRD as if you are the RFD author. Can you identify the technical constraints? Can you map stories to architectural components? If the RFD author would need to ask clarifying questions, the PRD is not ready.

---

## Glossary

| Term | Definition | Full Reference |
|------|-----------|---------------|
| [PRD](standards/GLOSSARY.md#prd-product-requirements-document) | Product requirements document covering vision, stories, and context. Focus on what and why, not how. | Product documentation |
| [User Story](standards/GLOSSARY.md#user-story) | "As a [persona], I want [action], so that [outcome]" — user-centric narrative with Acceptance Criteria. | Requirement unit |
| [Non-Functional Requirements](standards/GLOSSARY.md#non-functional-requirements) | Performance, security, scalability, reliability targets that constrain technical design. | Architecture constraints |
| [Lock Rule](standards/GLOSSARY.md#lock-rule) | Requirements for a module are frozen once it enters the "NOW" Roadmap column. Prevents scope creep. | Scope management |
| [Status Flip](standards/GLOSSARY.md#status-flip) | Proposed → Accepted ceremony. Status changes only when PR is approved. | Document lifecycle |
| [KPI](standards/GLOSSARY.md#kpi-key-performance-indicator) | Success metric measuring post-delivery impact. Defined in PRD context. | Measurement |
| [Atomic](standards/GLOSSARY.md#atomic) | One concern per file. PRD files are split by category (vision, stories, requirements, context). | Documentation principle |

---

## Chaining

**Upstream**:
- `standards/apply-principles` — Problem-over-solution and JIT principles ground PRD writing
- `workflow/plan-roadmap` — Roadmap position determines when PRD modules are Locked

**Downstream**:
- `workflow/specify-solution` — PRD requirements become RFD technical specifications
- `workflow/design-views` — User stories drive visual design specifications

**Cross-cutting**: When the PRD changes after a module is Locked, use the same Proposed → Accepted status flip. Document the rationale in the PR description, not in the requirements files.

**Handoff**: After Phase 6, pass the PRD folder to the `specify-solution` skill for technical design. The RFD author reads the PRD as their primary input.
