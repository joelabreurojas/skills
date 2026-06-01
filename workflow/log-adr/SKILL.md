---
name: log-adr
description: "Architecture Decision Records for documenting significant technical pivots. Use when making a major technology choice, selecting one approach over another, recording a decision with alternatives and consequences, or when the team needs to understand why the system is designed this way."
license: MIT
trigger: "architecture decision, technology choice, ADR, technical pivot, design decision, decision record, adr create, significant decision, log-adr"
metadata:
  author: Joel Abreu Rojas
  version: "1.0.0"
  ssot_chapter: "17: Architecture Decision Record Standards"
  porter_note: "ADR format is universal — works with any project type. File paths (`docs/ADR/`) are SSOT convention; adapt to your project structure."
---

# Log ADR (Architecture Decision Records)

## Philosophy

An ADR captures a **significant technical decision** — the context that forced it, the alternatives considered, the choice made, and the consequences accepted. It is not a design document (that is the RFD). It is not a requirements document (that is the PRD). It is a decision log: a permanent record of *why the system is this way*.

ADRs exist because teams forget. The developer who chose PostgreSQL over SQLite remembered the reasons. Six months later, a new contributor asks "why not SQLite?" Without an ADR, the answer is "I think someone decided that a long time ago." With an ADR, the answer is a document with context, options, data, and rationale.

**Reserve ADRs for significant decisions.** Not every choice needs an ADR. Choosing a linter config does not need one. Choosing a database does. The threshold: would a new team member need to understand *why* this choice was made to work effectively in the codebase? If yes, write an ADR. If no, a commit message suffices.

**ADRs are immutable once accepted.** If a later decision changes a previous one, write a new ADR and mark the old one as "Superseded." Never edit an accepted ADR — the history of decisions is as valuable as the current state. The old ADR explains what was tried, what was learned, and why the new direction was chosen.

**The Status Flip is mandatory.** An ADR starts as `Proposed` (under review in a PR). After approval but **before** merging, the status is flipped to `Accepted`. This ensures `main` always reflects the truth. An ADR that stays `Proposed` after merge is a lie — the decision was accepted, but the document says it is still under review.

---

## Anti-Patterns

### 1. ADR for Trivial Changes

Writing an ADR for decisions that do not need one: choosing a linter, picking a code formatter, selecting a test framework for a greenfield project. These are preferences, not architectural decisions. ADRs have maintenance cost — each one must be read, understood, and referenced.

**Fix**: Apply the significance test: would a new developer need to understand *why* this choice was made to work in the codebase? If the answer is "probably not," use a commit message instead. Save ADRs for database choices, API style decisions, authentication strategies, and similar cross-cutting concerns.

### 2. Skipping Alternatives

Writing an ADR that says "we chose X" without explaining why Y and Z were rejected. Without alternatives, the reader cannot evaluate whether the decision was well-reasoned or merely a default. Future developers cannot challenge the decision with new information if they do not know what was considered.

**Fix**: Every ADR must list at least 2 alternatives (including "do nothing") with pros and cons. The decision section must explain *why* the chosen option wins, not just *what* was chosen.

### 3. Leaving Status as Proposed After Merge

Merging a PR that contains an ADR with `Status: Proposed`. This creates a permanent record that says "this decision is under review" even though it was already accepted and merged. It erodes trust in the ADR system.

**Fix**: The developer **must** flip the status to `Accepted` after approval but **before** merging the PR. This is a mandatory step in the ADR workflow. CI or a pre-merge check can enforce this.

### 4. Writing ADRs Without Context

Starting the ADR with the decision instead of the context. "We chose PostgreSQL" means nothing without knowing *why*: what constraints existed, what workload patterns were expected, what the team already knew.

**Fix**: The Context section must come first and must answer: what problem or constraint forced this decision? What is the current state? What are the requirements that make this decision necessary now?

### 5. Editing Accepted ADRs

Going back and modifying an ADR after it has been accepted and merged. This destroys the historical record. If the decision needs to change, that is a *new* decision with new context.

**Fix**: Write a new ADR that references the old one and marks it as `Superseded`. The new ADR explains what changed, why, and what the new consequences are.

---

## Workflow

### Phase 1 — Determine if an ADR is Needed

Before writing, classify the decision:

- [ ] Is this a **significant technical decision**? (new major dependency, data model change, API contract change, security/performance/scalability impact)
- [ ] Would a new developer need to understand *why* this choice was made?
- [ ] Are there **multiple viable alternatives** with meaningful trade-offs?
- [ ] Does this decision **affect the project's architecture** beyond a single module?

If the answer to most of these is "no," use a commit message instead of an ADR.

**Threshold examples**:
| Decision | ADR Needed? |
|----------|------------|
| Choosing PostgreSQL over SQLite | Yes — data model, scalability, operational complexity |
| Adopting REST over GraphQL | Yes — API contract, tooling, team skills |
| Adding a new major dependency (e.g., Redis) | Yes — operational overhead, deployment impact |
| Choosing a linter configuration | No — preference, not architecture |
| Deciding on file naming conventions | No — convention, not architecture |
| Switching from monolith to microservices | Yes — fundamental architectural change |

### Phase 2 — Create the ADR File

Follow the naming and location conventions:

- [ ] **Location**: `docs/ADR/` directory
- [ ] **File name**: `NNN_short_description.md` (sequential three-digit prefix + slug)
- [ ] **Example**: `001_use_postgresql_over_sqlite.md`
- [ ] **First ADR**: `001_...` — the number sequence is chronological

### Phase 3 — Write the ADR Content

Follow the standard anatomy:

```markdown
# ADR {NNN}: {Title}

## Status

Proposed

## Context

{What is the specific problem, requirement, or constraint that forced this decision?
What is the current state? What are the requirements that make this decision necessary now?}

## Decision

{What is the chosen solution? Use clear, affirmative language.
"We will..." or "The system will..."}

## Alternatives Considered

### Option A: {Name}
{Description}
- Pros: {list}
- Cons: {list}

### Option B: {Name}
{Description}
- Pros: {list}
- Cons: {list}

## Consequences

{What are the trade-offs? What gets easier, and what gets harder?
What must other developers be aware of now?}

## References

{Links to RFD sections, related ADRs, external documentation, or research}
```

- [ ] Context section is written first — explains *why* this decision is needed
- [ ] Decision section uses affirmative language ("We will...")
- [ ] At least 2 alternatives are listed with pros and cons
- [ ] Consequences section is honest about trade-offs
- [ ] References link to related artifacts

### Phase 4 — The Status Flip Workflow

Follow the mandatory status progression:

- [ ] **Propose**: Create the ADR with `Status: Proposed` in a feature branch
- [ ] **Discuss**: The team reviews the technical choice within the Pull Request
- [ ] **Approve**: The reviewer approves the PR once the decision is validated
- [ ] **Flip**: After approval, but **BEFORE** merging, update status to `Accepted`
- [ ] **Merge**: The PR is squashed and merged — the ADR lives in `main` as an official record

**Critical**: The status flip happens *before* merge. If you merge with `Status: Proposed`, the record is permanently inaccurate.

### Phase 5 — Superseding an ADR

When a later decision changes a previous one:

- [ ] Create a new ADR with a new sequential number
- [ ] In the new ADR's Context, reference the old ADR
- [ ] Mark the old ADR's status as `Superseded`
- [ ] The new ADR follows the same workflow (Proposed → Accepted)

**Never edit an accepted ADR.** The history of decisions is the value. The old ADR explains what was tried and learned. The new ADR explains what changed and why.

### Phase 6 — Validate the ADR

Before requesting review:

- [ ] Context explains the problem clearly — a new developer can understand it
- [ ] Decision is unambiguous — no "we might" or "we could consider"
- [ ] Alternatives are fairly represented — not strawmen
- [ ] Consequences are honest — includes downsides
- [ ] Status is `Proposed` (the flip happens after approval, before merge)
- [ ] The ADR is referenced in the related PR's summary

---

## Glossary

| Term | Definition | Full Reference |
|------|-----------|---------------|
| [ADR](standards/GLOSSARY.md#adr-architecture-decision-record) | Document capturing a significant technical decision: context, options, decision, consequences. | Decision documentation |
| [RFD](standards/GLOSSARY.md#rfd-request-for-design) | Technical design document covering structure, data, interfaces, logic, and operations. | Architecture documentation |
| [Status Flip](standards/GLOSSARY.md#status-flip) | Mandatory workflow: Proposed → Accepted (before merge). Ensures `main` reflects the truth. | ADR lifecycle |
| [Superseded](standards/GLOSSARY.md#superseded) | Status of an ADR that has been replaced by a later decision. The old ADR remains as history. | ADR lifecycle |
| [Squash Merge](standards/GLOSSARY.md#squash-merge) | Merging strategy that compresses all branch commits into a single commit on `main`. | History strategy |
| [Atomic](standards/GLOSSARY.md#atomic) | One ADR = one significant decision. Do not bundle multiple decisions in one record. | Record integrity |

---

## Chaining

**Upstream**:
- `standards/apply-principles` — Principles guide which decisions are significant enough for an ADR and how consequences are evaluated

**Downstream**:
- `workflow/create-pr` — ADRs are created and reviewed within PRs; the Status Flip happens in the PR workflow
- `workflow/specify-solution` — ADRs that change system design may require RFD updates

**Cross-cutting**: ADRs are permanent records. They outlive the code they document. When a decision changes, a new ADR supersedes the old one — never edit the history.

**Handoff**: After Phase 6, create the PR with the ADR and hand off to `workflow/create-pr` for self-review and merge. Remember the Status Flip before merge.
