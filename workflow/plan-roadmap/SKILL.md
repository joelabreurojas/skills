---
name: plan-roadmap
description: "Create and maintain the strategic roadmap using the Now/Next/Later framework. Sequence high-level business modules, enforce JIT design gates, and keep the team focused on immediate priorities. Use when defining product sequencing, setting strategic priorities, planning quarters, or deciding what to build next."
license: MIT
trigger: "/plan-roadmap, create roadmap, update roadmap, now next later, strategic planning, product sequencing, priority framework, roadmap planning, feature sequencing, project roadmap"
metadata:
  author: Joel Abreu Rojas
  version: "1.0.0"
  ssot_chapter: "08: Roadmap Standards"
  porter_note: "Roadmap is a living document — update it as business context changes, not on a fixed calendar schedule."
---

# Plan Roadmap (Now/Next/Later)

## Philosophy

The Roadmap is the bridge between Product Requirements (PRD) and Technical Specification (RFD). Its purpose is to sequence high-level business modules so the team focuses engineering resources only on the immediate priority. It is a **status-based sequence**, not a fixed-date timeline.

We reject traditional Gantt charts and date-driven roadmaps. Fixed dates create false certainty, encourage scope creep to meet deadlines, and become obsolete within weeks. Instead, we use the **Now/Next/Later** framework: a three-column prioritization that answers three questions — what are we building right now, what is queued up next, and what is on the horizon.

The Roadmap is a **living document**. It changes as business context changes, as technical discovery reveals new constraints, and as market conditions shift. The status flip mechanism (Proposed → Accepted) ensures changes are deliberate and reviewed, not arbitrary. A static roadmap is an outdated roadmap — if you have not touched it in a month, it is wrong.

The Roadmap enforces the **Strategic Gate Rule**: no module enters technical design (RFD) until it is in the "NOW" column. This is not ceremony — it is the most important constraint in the SSOT workflow. Premature design creates waste, rot, and rework. The Roadmap gate protects engineering from designing things that will change before they are built.

---

## Anti-Patterns

### 1. Designing Everything Upfront

Writing RFDs or creating detailed issue breakdowns for modules in "NEXT" or "LATER." By the time those modules are promoted to "NOW," the requirements will have changed, the codebase will have evolved, and the design will be wrong. You have created documentation that actively misleads future developers.

**Fix**: Enforce the Strategic Gate Rule. No RFD, VIEWS, or issues for modules outside "NOW." For "NEXT" items, capture one paragraph of intent in the PRD only. Detailed design happens at promotion time.

### 2. Skipping the Roadmap Entirely

Jumping from a PRD straight to issues without a roadmap. Without a roadmap, there is no strategic sequencing. Developers work on whatever seems urgent, priorities shift weekly, and the most important modules get delayed by the loudest voices.

**Fix**: Every PRD module must be placed on the roadmap before work begins. The roadmap answers "what are we doing *first*?" If you cannot sequence the modules, you do not understand the dependencies well enough to start.

### 3. Overcommitting LATER Items

Documenting "LATER" items with detailed requirements, dates, or resource allocations. LATER items are abstract concepts — they have not been validated against current business context, technical constraints, or market conditions. Committing to them creates artificial deadlines and panic when they do not materialize as expected.

**Fix**: LATER items are one-sentence concepts in the PRD. No details, no dates, no resource assignments. When they move to NEXT, write the PRD. When they move to NOW, design the RFD. This is JIT applied to the roadmap itself.

### 4. Roadmap as a Fixed Calendar

Treating the roadmap as an annual plan that is set in stone. Business priorities change, technical discoveries invalidate assumptions, and team capacity fluctuates. A roadmap that cannot change is a roadmap that will be ignored.

**Fix**: Review the roadmap every sprint or every two weeks. Move items between columns as context changes. Use the status flip (Proposed → Accepted) to make changes deliberate. The roadmap is a living strategic tool, not a contract.

### 5. More Than Two Items in "NOW"

Having three or more milestones in the "NOW" column simultaneously. Too many active items split the team's focus, create context-switching overhead, and slow down delivery on all fronts. The team appears busy but nothing ships.

**Fix**: Enforce the two-milestone limit for "NOW." If the team cannot focus on two things, reduce to one. Quality and delivery speed are inversely proportional to the number of active work streams.

### 6. No Linkage to PRD

Roadmap entries without links to the corresponding PRD modules. Without traceability, no one can answer "what exactly is in scope for this item?" or "what are the business requirements behind this priority?"

**Fix**: Every roadmap entry must link to its PRD folder. The PRD is the authoritative source for what the item actually is. The roadmap only tracks sequencing and status.

---

## Workflow

### Phase 1 — Initialize the Roadmap

Create the roadmap document:

- [ ] Create `ROADMAP.md` at the project root or in `docs/`
- [ ] Set up the three-column structure: **🟢 NOW**, **🟡 NEXT**, **🔴 LATER**
- [ ] Set **Current Logical State** = `Proposed`
- [ ] Each column lists modules with: name, one-line description, and link to the PRD folder
- [ ] Verify the roadmap has no more than 2 items in NOW, 3-5 in NEXT, and unlimited in LATER

**Roadmap template**:

```markdown
# Roadmap

**Current Logical State**: Proposed | Accepted

## 🟢 NOW (Active & Designing)

| Module | Description | PRD |
|--------|-------------|-----|
| [Module A] | One-line description | [Link to PRD folder] |
| [Module B] | One-line description | [Link to PRD folder] |

## 🟡 NEXT (The Queue)

| Module | Description | PRD |
|--------|-------------|-----|
| [Module C] | One-line description | [Link to PRD folder] |

## 🔴 LATER (The Horizon)

| Module | Description | PRD |
|--------|-------------|-----|
| [Module D] | Intended direction | [Link to PRD folder] |
```

### Phase 2 — Sequence the Modules

Determine the strategic order of execution:

- [ ] Identify **dependencies** between modules (does Module B depend on Module A?)
- [ ] Identify **business value** (which module delivers the most user or revenue impact?)
- [ ] Identify **technical risk** (which module has the highest uncertainty — start it sooner)
- [ ] Identify **team readiness** (does the team have the skills for the next module?)
- [ ] Place modules in NOW → NEXT → LATER based on the above analysis

**Sequencing heuristics**:
| Factor | Weight |
|--------|--------|
| Blocked by another module | High — must sequence first |
| High business value | High — prioritize |
| High technical risk | High — start early to discover unknowns |
| Team already has context | Medium — leverage momentum |
| Low uncertainty, low value | Low — defer |

### Phase 3 — Enforce the JIT Design Gate

Before moving items between columns, check the gate rules:

- [ ] **NOW** items are actively being designed (RFD) or implemented (code)
- [ ] **NEXT** items have a PRD but NO RFD, NO VIEWS, NO issues
- [ ] **LATER** items are one-sentence concepts with no detailed requirements
- [ ] No module enters NOW without a complete PRD (requirements are Locked on entry)
- [ ] No module enters RFD design until it is in NOW

**Gate enforcement rule**: If you see an RFD, VIEWS, or issue breakdown for a module in NEXT or LATER, that is a process violation. Move the module to NOW first, or delete the premature design work.

### Phase 4 — Handle Promotions and Demotions

When a module moves between columns:

**Promotion (LATER → NEXT → NOW):**
- [ ] **LATER → NEXT**: Write the PRD (see `workflow/define-product`). Requirements are not yet locked.
- [ ] **NEXT → NOW**: Set status to `Proposed` on the roadmap branch. Begin RFD design.
- [ ] **On PR merge**: Flip roadmap status to `Accepted`. Requirements are now Locked.
- [ ] Update the affected PRD's `README.md` status to reflect the roadmap position

**Demotion (NOW → NEXT, NEXT → LATER):**
- [ ] Set roadmap status to `Proposed` on the feature branch
- [ ] Archive any in-progress RFD work (do not delete — it may be reused)
- [ ] Move all issues back to the backlog
- [ ] Communicate the change to stakeholders
- [ ] On PR merge: Flip status to `Accepted`

**Lock Rule**: When a module enters NOW, its requirements are Locked. No scope changes without a formal PRD update and re-approval. This protects the engineering team from moving targets during active development.

### Phase 5 — Review and Update Regularly

Keep the roadmap alive:

- [ ] Review the roadmap every sprint (or every two weeks)
- [ ] Check assumptions: are the NEXT items still the right priorities?
- [ ] Check capacity: are there still ≤2 items in NOW?
- [ ] Check progress: is each NOW item making measurable progress?
- [ ] Update the roadmap PR, get it reviewed, and merge with status flip

**Review triggers (review outside the regular cadence when any of these occur):**
- A critical business objective changes
- A technical discovery fundamentally changes a module's approach
- A team member leaves or joins, changing capacity
- A module in NOW is completed (promote the next candidate)

### Phase 6 — Validate Roadmap Health

Before finalizing a roadmap change:

- [ ] Status is `Proposed` on the feature branch
- [ ] No more than 2 items in NOW
- [ ] Every NOW item has a complete PRD with Locked requirements
- [ ] Every entry links to its PRD folder
- [ ] No RFD work exists for anything outside NOW
- [ ] The sequencing makes strategic sense (value > risk > dependencies)
- [ ] The roadmap reflects current business context, not last quarter's plan
- [ ] Stakeholders have visibility into NEXT priorities

**Health check**: If you cannot explain why Module A is in NOW and Module B is in NEXT to a non-technical stakeholder, the roadmap is not clear enough. Rewrite until the rationale is obvious.

---

## Glossary

| Term | Definition | Full Reference |
|------|-----------|---------------|
| [Now/Next/Later](standards/GLOSSARY.md#now-next-later) | Three-column prioritization framework: active development, queued, and horizon. | Strategic sequencing |
| [Lock Rule](standards/GLOSSARY.md#lock-rule) | Requirements are frozen when a module enters "NOW." Prevents scope creep. | Scope management |
| [Status Flip](standards/GLOSSARY.md#status-flip) | Proposed → Accepted ceremony. Status changes only when PR is approved. | Document lifecycle |
| [JIT](standards/GLOSSARY.md#just-in-time-jit) | Design architecture only for the module being built today. | Anti-paralysis principle |
| [PRD](standards/GLOSSARY.md#prd-product-requirements-document) | Product requirements document covering vision, stories, and context. | Product documentation |
| [RFD](standards/GLOSSARY.md#rfd-request-for-design) | Technical design document covering structure, data, interfaces, logic, and operations. | Architecture documentation |
| [Milestone](standards/GLOSSARY.md#milestone) | Group of related issues implementing one RFD module. Has collective Definition of Done. | Work grouping |

---

## Chaining

**Upstream**:
- `workflow/define-product` — PRDs define module scope; roadmap sequences them
- `standards/apply-principles` — JIT and Continuous Momentum principles ground the gate rule

**Downstream**:
- `workflow/specify-solution` — Only modules in "NOW" receive RFD-level technical design
- `workflow/design-views` — Only modules in "NOW" receive visual design specifications
- `workflow/plan-issues` — Only modules in "NOW" get broken into implementation tasks
- `workflow/define-milestone` — Milestones map to roadmap items; can only be created for "NOW" modules

**Cross-cutting**: The roadmap is the single source of truth for what the team is working on and why. When the PRD or business context changes, update the roadmap first, then propagate changes downstream. Status flips apply to the roadmap document just like PRDs and RFDs.

**Handoff**: After Phase 1-2, share the roadmap with all stakeholders. Roadmap changes go through PR review with the same Proposed → Accepted ceremony as any other planning artifact.
