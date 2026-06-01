---
name: define-milestone
description: "Define and manage milestones as domain-based epics with Definition of Done and vertical slice progression. Group related issues into coherent delivery units that cut across all architectural layers. Use when organizing implementation work, setting delivery targets, planning sprints, or defining done criteria for a module."
license: MIT
trigger: "/define-milestone, create milestone, define milestone, epic planning, definition of done, vertical slice, milestone structure, domain grouping, milestone blueprint"
metadata:
  author: Joel Abreu Rojas
  version: "1.0.0"
  ssot_chapter: "11: Milestone Standards"
  porter_note: "Milestones are domain-based, not time-based. Group by business capability, not calendar weeks."
---

# Define Milestone

## Philosophy

A Milestone is a high-level **Epic**. It represents the complete development of a single **Business Module** or **Core Feature set** (e.g., User Management, Billing, Analytics). Unlike traditional time-based sprints, milestones group work by **Domain** — we focus entirely on one area of the system until it is functional across all architectural layers.

Domain-based grouping prevents the most common delivery failure: spreading the team thin across multiple unrelated features, delivering nothing completely. A milestone concentrates effort on one business capability and drives it through every layer — data, logic, interface, UI, and quality assurance — before moving to the next.

Every milestone is built using **Vertical Slice Progression**: we build through the architectural layers in a defined sequence. This ensures each layer has a solid foundation before the next one is built. Data schemas come before business logic. Business logic comes before API endpoints. API endpoints come before UI. And everything is verified before delivery.

The **Definition of Done (DoD)** is the milestone's completion contract. It answers: "What does it mean for this milestone to be finished?" Without a DoD, milestones drift — developers call it done when the code compiles, testers call it done when the tests pass, and stakeholders call it done when they see a demo. None of these are sufficient individually. The DoD unifies all perspectives.

---

## Anti-Patterns

### 1. Milestones Without Definition of Done

Creating a milestone with a name and a list of issues but no DoD. Without a completion contract, every stakeholder has a different definition of "done." Developers declare victory when the API works. Designers when the UI matches the mockups. Product when the user story is demoable. The milestone never reaches consensus.

**Fix**: Every milestone must have a written Definition of Done covering: functional outcome, vertical slice checklist, technical integrity (tests, ADRs), and release criteria. The DoD must be agreed upon before any implementation starts.

### 2. Mixing Domains in One Milestone

Grouping unrelated business capabilities in the same milestone. "Implement user authentication and analytics dashboard" is two domains forced into one container. This creates split focus, unclear ownership, and a milestone that cannot be evaluated as complete or incomplete.

**Fix**: One milestone = one business domain. "User Authentication" is a milestone. "Analytics Dashboard" is a separate milestone. If two domains share a dependency, sequence them — don't merge them.

### 3. Missing Release Criteria

Defining a milestone without specifying what "shippable" means. The milestone's code might be complete, but is it ready for production? Without release criteria (performance targets, security review, documentation updates), the milestone stalls between "code complete" and "actually shipped."

**Fix**: Every milestone must define release criteria: What performance targets must be met? What security review must pass? What documentation must be updated? What rollout process is required?

### 4. Time-Boxed Milestones

Setting a milestone's scope by calendar duration ("this sprint's work") instead of by business domain. Time-boxed milestones mix unrelated features to fill the sprint, creating a patchwork of half-implemented capabilities that no single stakeholder can validate.

**Fix**: Milestones are domain-based, not time-based. If a milestone takes multiple sprints, that is fine — it is one coherent business capability. If it fits in one sprint, even better. But never merge unrelated work just to hit a timeline.

### 5. Skipping Vertical Slice Layers

Jumping directly to UI implementation without completing the data and logic layers first. "Start with the frontend, figure out the backend later" produces UI that cannot connect to real data, leading to rework, integration delays, and frustrated developers.

**Fix**: Follow the vertical slice progression in order: Data → Logic → Interface → UI → QA. Each layer must be demonstrably complete before the next layer starts.

### 6. Unlimited Active Milestones

Starting work on three or four milestones simultaneously. Context-switching between multiple domains slows everyone down, increases cycle time, and reduces quality across the board. Nothing ships, but everything is "in progress."

**Fix**: Enforce a maximum of two active milestones at any time. This aligns with the roadmap's "NOW" limit. If the team cannot focus on two, reduce to one.

---

## Workflow

### Phase 1 — Verify Roadmap Position

Before defining a milestone, confirm it is authorized:

- [ ] Check the **Roadmap** (`ROADMAP.md`) — is this module in the **🟢 NOW** column?
- [ ] If the module is in NEXT or LATER, STOP. A milestone cannot be created for a module that is not actively being designed or built.
- [ ] Verify the corresponding **PRD** is complete and status is `Accepted`
- [ ] Verify the corresponding **RFD** exists or is being written
- [ ] If the module is not in NOW, promote it first (see `workflow/plan-roadmap`)

**Gate rule**: No milestone without roadmap authorization. The roadmap gates all execution work.

### Phase 2 — Define the Milestone Structure

Create the milestone container:

- [ ] **Narrative Title**: Immediately identifies the business domain (e.g., `M2: User Authentication`)
- [ ] **Milestone Goal**: One sentence — what the completed module delivers to users
- [ ] **Linked PRD Story**: Which user story or stories does this milestone serve?
- [ ] **Linked RFD Section**: Which RFD module does this milestone implement?
- [ ] **Linked Roadmap Entry**: Which roadmap item does this milestone deliver?
- [ ] Create the milestone folder: `docs/MILESTONES/{M#_domain_name}/`

**Milestone template**:

```markdown
# {M#}: {Domain Name}

**Status**: Active | Complete | Blocked
**Goal**: [One sentence — what users can do when this milestone ships]

## Linked Artifacts
- **PRD**: [Link to `docs/PRD/README.md`]
- **RFD**: [Link to `docs/RFD/README.md`]
- **Roadmap**: [Link to roadmap entry]

## Definition of Done

- [ ] Functional outcome achieved: [concrete, verifiable state]
- [ ] Data layer: schemas, models, migrations complete
- [ ] Logic layer: business services and rules complete
- [ ] Interface layer: API endpoints/contracts complete
- [ ] Presentation layer: UI matches VIEWS specifications
- [ ] Quality: 100% test coverage for new logic
- [ ] Technical integrity: ADRs written for technical pivots
- [ ] Documentation: PRD, RFD, and VIEWS are synced

## Release Criteria

- [ ] [Performance target, e.g., "API responds <200ms p95"]
- [ ] [Security review completed]
- [ ] [Documentation updated]
- [ ] [Rollback plan documented]

## Issues

| Order | Issue | Description | Status |
|-------|-------|-------------|--------|
| 1 | [Link to issue] | Data schemas and migrations | 🟡 In Progress |
| 2 | [Link to issue] | Business logic services | ⚪ Not Started |
| ... | ... | ... | ... |
```

### Phase 3 — Define the Vertical Slice Sequence

Plan the build order across architectural layers:

- [ ] **Data Layer (Layer 1)**: Schemas, models, migrations, data access layer
  - [ ] Database schema changes
  - [ ] Data model definitions (entities, fields, types)
  - [ ] Migrations (create, alter, seed)
  - [ ] Data access layer (repositories, DAOs)
- [ ] **Logic Layer (Layer 2)**: Core business services and rules
  - [ ] Domain services (business operations)
  - [ ] Business rule validation
  - [ ] State machines and workflows
  - [ ] Background job handlers
- [ ] **Interface Layer (Layer 3)**: API endpoints, controllers, contracts
  - [ ] HTTP/gRPC/GraphQL endpoints
  - [ ] Request validation
  - [ ] Error handling (standard error envelope)
  - [ ] Authentication and authorization at boundaries
- [ ] **Presentation Layer (Layer 4)**: User interface matching VIEWS specs
  - [ ] Component implementation per VIEWS inventory
  - [ ] All four states: empty, loading, error, success
  - [ ] Responsive behavior (if applicable)
  - [ ] Integration with API layer
- [ ] **Quality Assurance (Layer 5)**: Automated tests and documentation
  - [ ] Unit tests for new logic (target: 100% coverage)
  - [ ] Integration tests for API endpoints
  - [ ] E2E tests for critical user paths
  - [ ] Documentation updates (PRD, RFD, VIEWS synced)

**Layer completion rule**: Each layer must be demonstrably complete before the next layer begins. "Demonstrably complete" means: code is merged, tests pass, and the implementation matches the RFD specification for that layer.

### Phase 4 — Define Definition of Done

Write the completion contract for the milestone:

- [ ] **Functional Outcome**: The tangible, real-world state described in the milestone goal is achieved (e.g., "Users can successfully log in with email and password")
- [ ] **Vertical Slice Checklist**: Every layer (Data → UI → QA) is operational and verified
- [ ] **Technical Integrity**:
  - [ ] All child Issues are closed
  - [ ] 100% of new logic is covered by tests
  - [ ] All technical pivots are recorded in ADRs
  - [ ] No known regressions in existing functionality
- [ ] **Documentation Sync**:
  - [ ] PRD updated if requirements changed during implementation
  - [ ] RFD reflects the as-built architecture
  - [ ] VIEWS match the delivered UI

**DoD quality check**: Can you read the DoD and determine definitively whether this milestone is complete? If the criteria are subjective or unverifiable, rewrite until they are pass/fail.

### Phase 5 — Break Down Into Issues

Decompose the milestone into executable tasks:

- [ ] Create issues for each layer of the vertical slice (see `workflow/plan-issues`)
- [ ] Assign Execution Orders following the vertical slice sequence (Data first, QA last)
- [ ] Set dependencies between layers (Logic depends on Data, Interface depends on Logic, etc.)
- [ ] Each issue has 3-7 testable acceptance criteria
- [ ] No issue bundles multiple layers — each layer gets its own issue(s)

**Issue-to-milestone mapping**: Every issue in the milestone must reference the milestone ID in its body. This allows the tracker to group issues by milestone automatically.

### Phase 6 — Track and Manage

Monitor milestone progress:

- [ ] Track completion by layer, not by individual issues
- [ ] If a layer is blocked, escalate immediately — do not start the next layer
- [ ] Review milestone progress at the regular roadmap cadence (every sprint)
- [ ] If the milestone's scope changes (requirements change, discovery reveals new work), update the milestone definition and get approval
- [ ] When all DoD criteria are met, mark the milestone as **Complete**

**Completion ceremony**: When a milestone is complete, update the Roadmap (promote the next candidate), archive the milestone folder, and celebrate the delivery. A completed milestone is a concrete, verifiable achievement — it represents a complete business capability, not a time interval.

---

## Glossary

| Term | Definition | Full Reference |
|------|-----------|---------------|
| [Milestone](standards/GLOSSARY.md#milestone) | Group of related issues implementing one RFD module. Has collective Definition of Done. | Work grouping |
| [Definition of Done](standards/GLOSSARY.md#definition-of-done) | Completion contract for a milestone: functional outcome, vertical slice checklist, technical integrity. | Quality gate |
| [Vertical Slice](standards/GLOSSARY.md#vertical-slice) | Building through architectural layers in sequence: Data → Logic → Interface → UI → QA. | Delivery strategy |
| [Epic](standards/GLOSSARY.md#epic) | Large body of work broken into smaller issues. Synonym for milestone in this context. | Work grouping |
| [Execution Order](standards/GLOSSARY.md#execution-order) | Numeric priority (1, 2, 3...) assigned to issues. Lowest number = highest priority. | Task prioritization |
| [PRD](standards/GLOSSARY.md#prd-product-requirements-document) | Product requirements document — the source of truth for what the system does. | Product documentation |
| [RFD](standards/GLOSSARY.md#rfd-request-for-design) | Technical design document — the source of truth for how the system works. | Architecture documentation |
| [ADR](standards/GLOSSARY.md#adr-architecture-decision-record) | Document capturing a significant technical decision with context, options, and consequences. | Decision documentation |

---

## Chaining

**Upstream**:
- `workflow/plan-roadmap` — Milestones can only be created for modules in the "NOW" column
- `workflow/define-product` — PRD user stories define what the milestone delivers
- `workflow/specify-solution` — RFD modules define the technical scope of the milestone

**Downstream**:
- `workflow/plan-issues` — Milestones are decomposed into issues with execution orders
- `workflow/enforce-branch-flow` — Each issue maps to one branch
- `standards/write-tests` — QA layer requires comprehensive test coverage

**Cross-cutting**: Milestones are the execution counterpart to the Roadmap. When roadmap priorities change, active milestones may be paused or demoted. When a PRD or RFD changes, the milestone's scope and DoD may need updating. Milestones bridge strategic planning (roadmap) and tactical execution (issues).

**Handoff**: After Phase 4, hand off to `plan-issues` for task breakdown. After Phase 5, implementation begins following the vertical slice sequence. Verify each layer is demonstrably complete before starting the next.
