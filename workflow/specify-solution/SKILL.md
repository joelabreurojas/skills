---
name: specify-solution
description: "Create or update a Request for Design (RFD) — the authoritative technical specification. Modular folder with structure, data model, interfaces, logic, and operations. Use when designing system architecture, writing technical specs, defining data models, planning API contracts, or documenting deployment and security strategy."
license: MIT
trigger: "/specify-solution, create RFD, update RFD, technical design, architecture spec, data model, API contract, system design, technical specification, RFD folder, design document"
metadata:
  author: Joel Abreu Rojas
  version: "1.0.0"
  ssot_chapter: "09: RFD Standards"
  porter_note: "RFD structure is universal — adapt sections to your architecture style (microservices, monolith, serverless)."
---

# Specify Solution (RFD)

## Philosophy

The RFD is the **technical source of truth**. If the code and the RFD disagree, the RFD wins. The code must be fixed to match the design, or the design must be formally updated via a PR. This is not bureaucracy — it is the only way to prevent architectural drift in a growing codebase.

RFDs follow **JIT (Just-In-Time) design**: technical specifications are authored only for modules currently in the "NOW" column of the Roadmap. Designing modules in "NEXT" or "LATER" is wasted effort — requirements will change, and the design will rot before it is used. Resist the urge to be thorough for things that do not exist yet.

Architecture is about **contracts and boundaries**, not implementation. Signatures, data flows, API shapes, error handling, deployment topology. Large code blocks in an RFD are a smell — they belong in the codebase. Use pseudocode, Mermaid diagrams, and high-level descriptions to convey intent without coupling the spec to a specific implementation.

All diagrams use **Mermaid.js** — diagrams-as-code that are version-controlled, searchable, and produce clean diffs in PRs. A picture that cannot be diffed is a liability.

---

## Anti-Patterns

### 1. Pasting Code Blocks Instead of Contracts

Writing implementation code directly in the RFD. Code goes stale the moment it is written. Contracts (signatures, types, payloads) go stale only when the design changes. The RFD should describe *what* the code does, not *how* it does it.

**Fix**: Replace code blocks with type definitions, API signatures, pseudocode, or Mermaid flowcharts. If a code snippet encodes a critical decision (state machine, schema shape), inline it and note it is from a prototype — trim to the decision-rich parts.

### 2. Designing Modules in "NEXT" or "LATER"

Writing detailed technical specs for modules that are not in the active "NOW" Roadmap column. By the time those modules are built, the requirements will have changed and the design will be wrong. You have created documentation that actively misleads.

**Fix**: Only author RFD sections for modules in "NOW". For "NEXT" modules, capture one-paragraph intent only. Detailed design happens when the module is promoted.

### 3. No Fallback or Contingency Plan

Designing only the happy path. Every external dependency, every network call, every third-party service has failure modes. An RFD without a contingency plan is an architecture that only works in demos.

**Fix**: Every critical dependency in `05_ops_and_security.md` must have a documented fallback: circuit breaker, cached fallback, graceful degradation, or explicit "fail the entire operation" decision.

### 4. Diagrams Without Context

Dropping a Mermaid diagram without explaining what it shows, why it matters, and what the boxes represent. A diagram without context is decoration, not documentation.

**Fix**: Every diagram has a caption explaining what it illustrates, a legend if symbols are non-obvious, and a link to the relevant PRD user story or business goal.

### 5. Cross-Sync Violation

Updating the RFD with a technical constraint that changes product behavior, without updating the PRD first. The PRD is the authority on *what* the system does. If technical discovery changes requirements, the PRD must be updated and approved before the RFD reflects the change.

**Fix**: When RFD work reveals a product constraint change, stop RFD work, update the PRD, get approval, then resume RFD with the new requirements.

### 6. Missing Mermaid Diagrams

Describing architecture in paragraphs of prose when a diagram would be clearer. "The system has three services that communicate via gRPC" is harder to parse than a Mermaid diagram showing the three boxes and their connections. Prose is for context and rationale; diagrams are for structure and flow.

**Fix**: Every architecture decision, data relationship, and orchestration flow must have a Mermaid diagram. Use prose only to explain *why*, not *what the structure looks like*.

---

## Workflow

### Phase 1 — Initialize the RFD Folder

Create the modular RFD structure:

```
docs/RFD/
├── README.md              # Entry point + High-Level Architecture + Status
├── 01_structure.md        # Architecture pattern, directory tree, dependencies
├── 02_data_model.md       # Storage strategy, data dictionary, ERD, indexing
├── 03_interface.md        # API contracts, payload shapes, boundary security
├── 04_logic.md            # Domain services, business rules, background jobs
└── 05_ops_and_security.md # Testing strategy, deployment, monitoring, secrets
```

- [ ] Create `docs/RFD/` directory
- [ ] Initialize `README.md` with High-Level Architecture (Mermaid C4 Context diagram) and **Current Logical State** = `Proposed`
- [ ] Initialize each module file with section headers
- [ ] Verify the RFD references the correct PRD folder (`docs/PRD/`)

### Phase 2 — Define System Structure

Document the architectural skeleton in `01_structure.md`:

- [ ] **Architecture Pattern**: MVC, Hexagonal, Clean, Microservices — name it and justify why
- [ ] **Directory Tree**: Top-level project structure with annotations
- [ ] **Internal Dependencies**: Which modules can communicate, which imports are prohibited
- [ ] **External Dependencies**: Third-party libraries, APIs, services — with version pins
- [ ] **Dependency Boundary Rules**: Explicit rules preventing circular dependencies

**Quality check**: Can a new developer understand the system's skeleton from this file alone? If you need to explain it verbally, the file is incomplete.

### Phase 3 — Model the Data

Define the storage strategy in `02_data_model.md`:

- [ ] **Storage Strategy**: Database type, caching layer, search engine — with justification
- [ ] **Data Dictionary**: Every entity with its fields, types, constraints, and defaults
- [ ] **Entity Relationships**: Mermaid ERD showing relationships (1:1, 1:N, M:N)
- [ ] **Indexing Strategy**: Which fields are indexed and why (query patterns drive indexes)
- [ ] **Migration Plan**: How existing data is handled if this is not a greenfield project

**Diagram requirement**: Use Mermaid ERD syntax. Never paste a screenshot — diagrams must be diffable.

**Indexing rule**: An index without a documented query pattern is a guess. Every index entry must answer: "Which query uses this index, and what is the expected query frequency?"

### Phase 4 — Define Interfaces

Specify all communication contracts in `03_interface.md`:

- [ ] **Communication Protocols**: REST, GraphQL, gRPC, message queues — per boundary
- [ ] **API Contracts**: Endpoint signatures, request/response payloads, status codes
- [ ] **Data Contracts**: Shared types between modules (TypeScript interfaces, protobuf schemas)
- [ ] **Boundary Security**: Authentication and authorization at each interface boundary
- [ ] **Error Envelope**: Standardized error response format (see `standards/design-api` skill)

**Contract format**: Use type definitions or pseudocode for payload shapes. Avoid full implementation code — it goes stale.

### Phase 5 — Document Logic and Services

Outline core business logic in `04_logic.md`:

- [ ] **Domain Services**: Key services and their responsibilities (one sentence each)
- [ ] **Business Rules**: Constraints and validations that enforce domain invariants
- [ ] **Background Jobs**: Async tasks, their triggers, retry policies, and failure handling
- [ ] **State Machines**: If entities have lifecycle states, document transitions (Mermaid stateDiagram)
- [ ] **Orchestration Flow**: Mermaid sequence diagram for the primary user journey

**Rule**: Business rules are the *what* (invariant: "order total must be positive"), not the *how* (implementation: "use a check constraint on the orders table").

### Phase 6 — Plan Operations and Security

Define deployment, monitoring, and security in `05_ops_and_security.md`:

- [ ] **Testing Strategy**: Unit, integration, E2E coverage targets per module
- [ ] **Deployment Pipeline**: How code reaches production (CI → staging → production)
- [ ] **Monitoring**: Key metrics, alerting thresholds, dashboards
- [ ] **Secrets Management**: How secrets are stored, rotated, and accessed (see `standards/shift-left-security` skill)
- [ ] **Fallback Plans**: For every critical external dependency, document the contingency
- [ ] **Rollback Strategy**: How to undo a bad deployment safely

**Fallback plan format**: Each critical dependency needs a named pattern:
| Dependency | Failure Mode | Pattern | User Impact |
|-----------|-------------|---------|-------------|
| Payment API | Timeout (>5s) | Circuit breaker → queue for retry | "Payment processing — try again in a few minutes" |
| Search engine | Unavailable | Graceful degradation → chronological list | Reduced relevance, full functionality |

### Phase 7 — Validate and Set Status

Finalize the RFD before breaking into issues:

- [ ] All 6 files are populated and internally consistent
- [ ] High-Level Architecture diagram in `README.md` is accurate
- [ ] Status in `README.md` = `Proposed` (ready for review)
- [ ] Every external dependency has a fallback plan
- [ ] All diagrams use Mermaid syntax (no screenshots)
- [ ] No code blocks longer than 15 lines — if longer, it belongs in the codebase
- [ ] Every API endpoint has a defined error envelope
- [ ] Every entity in the data model has a documented indexing strategy
- [ ] The RFD is ready to hand off to `design-views` and `plan-issues` for implementation planning

**Pre-handoff review**: Read the RFD as if you are the issue author. Can you identify the modules, their dependencies, and the implementation sequence? If the issue author would need to ask clarifying questions, the RFD is not ready.

---

## Glossary

| Term | Definition | Full Reference |
|------|-----------|---------------|
| [RFD](standards/GLOSSARY.md#rfd-request-for-design) | Technical design document covering structure, data, interfaces, logic, and operations. Source of truth for architecture. | Architecture documentation |
| [JIT](standards/GLOSSARY.md#just-in-time-jit) | Design architecture only for the module being built today. Prevents design rot. | Anti-paralysis principle |
| [C4 Model](standards/GLOSSARY.md#c4-model) | Hierarchical system visualization: Context → Containers → Components. Never combine in one diagram. | Architecture visualization |
| [Dependency Boundary](standards/GLOSSARY.md#dependency-boundary) | Rules defining which modules may communicate and which imports are prohibited. | Architecture constraint |
| [Stateless](standards/GLOSSARY.md#stateless) | No session state stored between requests. Default design principle for APIs. | Architecture default |
| [Mermaid](standards/GLOSSARY.md#mermaid) | Diagrams-as-code syntax. Version-controlled, searchable, diffable. | Visualization tool |
| [Status Flip](standards/GLOSSARY.md#status-flip) | Proposed → Accepted ceremony. Status changes only when PR is approved. | Document lifecycle |

---

## Chaining

**Upstream**:
- `workflow/define-product` — PRD requirements are the primary input for RFD technical design
- `standards/design-api` — API contracts and error envelopes inform RFD interface sections
- `workflow/plan-roadmap` — Only modules in "NOW" get RFD-level design

**Downstream**:
- `workflow/design-views` — RFD data contracts and interfaces drive visual design specifications
- `workflow/plan-issues` — RFD modules break into implementation tasks with execution order
- `standards/write-tests` — RFD logic and contracts define what needs test coverage

**Cross-cutting**: When the RFD changes after a module is Locked, use the same Proposed → Accepted status flip. If the change affects product behavior, update the PRD first.

**Handoff**: After Phase 7, pass the RFD to `design-views` (for UI specs) and `plan-issues` (for task breakdown). Both skills read the RFD as their primary input.
