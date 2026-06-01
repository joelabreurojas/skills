---
name: plan-issues
description: "Break an RFD or PRD into independently executable issues with execution order, dependencies, and acceptance criteria. Use when creating implementation tasks, decomposing a design into work units, planning sprint work, or converting specs into actionable tickets."
license: MIT
trigger: "/plan-issues, create issues, break down work, implementation tasks, execution order, sprint planning, task decomposition, issue breakdown, work units, dependency mapping, acceptance criteria"
metadata:
  author: Joel Abreu Rojas
  version: "1.0.0"
  ssot_chapter: "12: Issue Standards"
  porter_note: "Issue structure works with GitHub Issues, Jira, Linear, or any tracker — adapt field names to your tool."
---

# Plan Issues

## Philosophy

An issue is a **single, discrete task** that a developer picks up to implement one specific part of a module. It is not a feature, not an epic, not a vague goal. It is a unit of work with clear boundaries, clear dependencies, and clear acceptance criteria.

Issues are the bridge between design and code. The RFD says *what* to build and *how* it fits together. The issue says *what to do first*, *what blocks what*, and *how to know when it is done*. Without this bridge, developers guess at priority, duplicate effort, and deliver incomplete work.

**Execution Order** is the real roadmap. The Issue ID (e.g., #180) is a random identifier assigned by the tracker. The Execution Order (1, 2, 3...) is the strategic priority. Developers always focus on the lowest available numbers. Confusing ID with Order is a common source of coordination failure.

**Dependencies are hard technical blockers**, not "nice to have" relationships. If Issue 3 cannot be completed until Issue 1 is merged, that is a dependency. If Issue 3 can be worked on in parallel with Issue 1, there is no dependency. Over-specifying dependencies creates artificial bottlenecks. Under-specifying them creates integration nightmares.

---

## Anti-Patterns

### 1. Missing or Duplicate Execution Order Numbers

Creating issues without an Execution Order, or assigning duplicate orders. Without a clear order, developers cannot determine what to work on next. Duplicate orders create confusion about priority.

**Fix**: Every issue must have a unique, sequential Execution Order number. Gaps are allowed (1, 3, 7) but duplicates are not. The order defines strategic priority, not sequence of creation.

### 2. Bundling Unrelated Work in One Issue

Combining multiple unrelated changes in a single issue. "Implement user authentication and update the footer" is two issues. Bundled issues are impossible to review, dangerous to rollback, and create ambiguous blame when something breaks.

**Fix**: Apply the atomicity test: can you write a one-sentence description that covers everything? If you need "and," split it. One issue = one logical change = one PR.

### 3. Incomplete or Missing Acceptance Criteria

Writing issues without objective, testable "done" conditions. Without acceptance criteria, "done" is subjective. One developer thinks it is finished; the reviewer thinks it is half-built. This creates rework, frustration, and scope drift.

**Fix**: Every issue must have 3-7 acceptance criteria. Each criterion must be testable — you can verify it with a pass/fail check. If you cannot write a test for it, the criterion is too vague.

### 4. No Dependency Mapping

Creating issues without specifying which tasks block others. Developers start work on blocked tasks, waste time, and produce code that cannot be merged. Or they wait unnecessarily for tasks that are not actually blocking them.

**Fix**: Every issue has a `Depends On` field. If the issue can start immediately, explicitly write "None." If it depends on another issue, list the Execution Order number. Never leave the field blank.

### 5. Issues Without a Clear Goal

Writing a to-do list without a one-sentence goal. The goal answers "why does this issue exist?" Without it, the developer implements the to-do items without understanding the purpose, leading to technically correct but architecturally wrong solutions.

**Fix**: Every issue starts with a Goal: one sentence explaining the value this task adds. The goal connects the task to the PRD user story or RFD module.

---

## Workflow

### Phase 1 — Gather Input Artifacts

Read the source materials before breaking down work:

- [ ] Read the **PRD** (`docs/PRD/`) — understand user stories and acceptance criteria
- [ ] Read the **RFD** (`docs/RFD/`) — understand technical structure, data model, interfaces
- [ ] Read the **VIEWS** (`docs/VIEWS/`) — understand UI specifications (if applicable)
- [ ] Identify the module scope — which RFD sections are being implemented?
- [ ] Check the **Roadmap** — which module is in the "NOW" column?

**Rule**: Do not break down work for modules not in "NOW". JIT applies to issue planning too.

### Phase 2 — Define the Milestone

Create a milestone to group related issues:

- [ ] **Milestone Name**: Matches the RFD module being implemented
- [ ] **Milestone Goal**: One sentence — what the module delivers to the user
- [ ] **Definition of Done**: The collective acceptance criteria for the entire milestone
- [ ] **Linked PRD Story**: Which user story does this milestone serve?
- [ ] **Linked RFD Section**: Which RFD module does this milestone implement?

### Phase 3 — Decompose into Issues

Break the milestone into individual tasks:

- [ ] Each issue represents **one logical change** (not one file, not one layer)
- [ ] Each issue has a **Goal**: one sentence explaining the value
- [ ] Each issue has a **To-Do List**: atomic technical requirements (one to-do ≈ one commit)
- [ ] Each issue has **Acceptance Criteria**: objective, testable "done" conditions
- [ ] No issue bundles unrelated changes — apply the atomicity test

**Decomposition heuristics**:
| Signal | Split Into Separate Issues |
|--------|---------------------------|
| "and" in the description | Yes — two logical changes |
| Different layers (data + API + UI) | Maybe — vertical slice = one issue; horizontal split = multiple |
| Different rollback risk profiles | Yes — separate for safe rollback |
| Different reviewers needed | Yes — separate for focused review |

### Phase 4 — Assign Execution Order and Dependencies

Map the strategic priority and dependency graph:

- [ ] Assign **Execution Order** to each issue (1, 2, 3...) — lowest = highest priority
- [ ] Identify **Hard Technical Blockers**: tasks that cannot be completed until another is merged
- [ ] Set `Depends On` for each issue: the Execution Order number of the blocker, or "None"
- [ ] Verify parallelism: if two issues have no dependencies, they can be worked simultaneously
- [ ] Identify the **critical path**: the longest chain of dependent issues

**Dependency rules**:
- `Depends On: None` → Can start immediately, parallelizable
- `Depends On: Order #X` → Cannot finish until Task X is stable or merged
- Dependencies are **hard blockers**, not "nice to have" ordering preferences
- If you are unsure whether a dependency is real, it probably is not — err on the side of parallelism

### Phase 5 — Write the Issue Content

For each issue, write the tracker-ready content:

```markdown
## Goal

[One sentence: why this issue exists and the value it adds]

## What to Build

[Concise description of the end-to-end behavior. Avoid file paths and code snippets — they go stale.]

## To-Do

- [ ] [Atomic technical requirement 1]
- [ ] [Atomic technical requirement 2]
- [ ] [Atomic technical requirement 3]

## Acceptance Criteria

- [ ] [Objective, testable condition 1]
- [ ] [Objective, testable condition 2]
- [ ] [Objective, testable condition 3]

## Execution Order

[Number]

## Depends On

[Order #X] or [None - can start immediately]

## PRD Story

[Link to the user story this issue serves]

## RFD Reference

[Link to the RFD section this issue implements]
```

- [ ] Goal is one sentence, not a paragraph
- [ ] To-Do items are atomic — each corresponds to one or more commits
- [ ] Acceptance criteria are testable — you can verify each with a pass/fail check
- [ ] No file paths or code snippets in the description (they go stale)
- [ ] PRD and RFD references are included for traceability

### Phase 6 — Validate the Breakdown

Review the complete issue set before publishing:

- [ ] **Completeness**: Every RFD section in the module has a corresponding issue
- [ ] **Atomicity**: No issue bundles unrelated changes
- [ ] **Dependency graph**: No cycles (Issue A blocks B blocks C blocks A)
- [ ] **Critical path**: The longest dependency chain is reasonable (≤ 5 issues deep)
- [ ] **Parallelism**: At least some issues can be worked simultaneously
- [ ] **Acceptance criteria**: Every issue has testable "done" conditions
- [ ] **Execution orders**: Unique, sequential, no gaps required but no duplicates

**Sanity check**: If a developer picks up Issue 1 and works through the orders, do they deliver the module in a logical sequence? If not, reorder.

---

## Glossary

| Term | Definition | Full Reference |
|------|-----------|---------------|
| [Execution Order](standards/GLOSSARY.md#execution-order) | Numeric priority (1, 2, 3...) assigned to issues. Lowest number = highest priority. The real roadmap. | Task prioritization |
| [Depends On](standards/GLOSSARY.md#depends-on) | Hard technical blocker: issue cannot finish until the referenced task is merged. | Dependency tracking |
| [Atomic](standards/GLOSSARY.md#atomic) | One issue = one logical change = one PR. No unrelated changes bundled together. | Work unit principle |
| [Acceptance Criteria](standards/GLOSSARY.md#acceptance-criteria) | Objective, testable conditions that define "done" for a specific issue. | Quality gate |
| [Vertical Slice](standards/GLOSSARY.md#vertical-slice) | One thin cut through all layers (schema → API → UI → tests) that delivers complete, testable behavior. | Decomposition strategy |
| [Stacked Branch](standards/GLOSSARY.md#stacked-branch) | Branching off an active feature branch instead of main when starting a blocked task. | Parallel execution technique |
| [Milestone](standards/GLOSSARY.md#milestone) | Group of related issues implementing one RFD module. Has collective Definition of Done. | Work grouping |

---

## Chaining

**Upstream**:
- `workflow/define-product` — PRD user stories define what needs to be built
- `workflow/specify-solution` — RFD modules define how it is structured and what the technical tasks are
- `workflow/design-views` — View specifications define frontend implementation tasks
- `workflow/define-milestone` — Milestones group issues into domain-based epics with DoD

**Downstream**:
- `workflow/enforce-branch-flow` — Each issue maps to one branch following the naming convention
- `workflow/write-commits` — To-do items map to atomic commits
- `workflow/create-pr` — Each issue produces one PR with test instructions

**Cross-cutting**: Issues are living artifacts. When the RFD changes, update the affected issues. When a PRD story changes, update the linked issues. Execution Orders may need reordering when dependencies shift.

**Handoff**: After Phase 6, publish the issues to the tracker in dependency order (blockers first). Then hand off to `workflow/enforce-branch-flow` for branch creation and implementation.
