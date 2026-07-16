---
name: apply-principles
description: "Core engineering principles for decision-making and code review. Ground every architectural choice in 8 pillars: simplicity, atomicity, automation, JIT, collective ownership, documentation, and blameless culture. Use when starting a new project, making architecture decisions, reviewing code, resolving design disagreements, or onboarding new team members."
license: MIT
trigger: "/apply-principles, new project, architecture decision, code review, design disagreement, principle, onboarding, retrospective"
metadata:
  author: Joel Abreu Rojas
  version: "1.0.0"
  ssot_chapter: "01: Core Philosophy"
  porter_note: "Principles are universal — adapt terminology to your domain."
---

# Apply Principles

## Philosophy

Eight principles govern every engineering decision. They are not aspirations — they are constraints. When two principles conflict, the one closer to the user wins.

These principles exist because complex systems fail in predictable ways: over-engineering creates friction, skipping automation creates human error, and blaming people creates silence. Each principle targets a specific failure mode that has been observed反复 in real engineering teams.

This skill is the **root** of the standards chain. Every other skill references these principles. When you load a downstream skill (testing, commits, api-design), its rules are derived from here. If a downstream rule feels wrong, return to this skill and check which principle it serves.

**Adaptability**: These rules scale with the project. A solo prototype applies them lightly. A production system with a team applies them rigorously. The principle is the same; the enforcement intensity varies.

---

## Anti-Patterns

### 1. "Rule for Rule's Sake"
Applying principles without understanding *why* the rule exists. Every principle targets a specific failure mode. If you cannot name the failure mode, you are cargo-culting the rule. Stop, identify the failure, then apply the principle that addresses it.

**Example**: Enforcing ADR documentation for a one-line config change. The failure mode ADRs prevent is "nobody knows why we chose X over Y." A config change does not have that risk.

### 2. Skipping the Blameless Postmortem
When something breaks, jumping to "who did this?" instead of "how did the system allow this?" Blame creates silence. Silence creates more failures. Every mistake is an opportunity to improve documentation, tooling, or automation — not to assign fault.

**Check**: After an incident, ask "what would have prevented this?" not "who caused this?"

### 3. Over-Engineering Ahead of Requirements
Building for hypothetical future needs violates JIT (Just-In-Time) engineering. Design the architecture for the module you are building *today*. Tomorrow's requirements will inform tomorrow's design. Premature abstraction is the root of complexity.

**Warning sign**: "We might need this later." If you cannot point to a concrete, near-term requirement, you are over-engineering.

### 4. Human Gates Instead of Automated Gates
Trusting manual review to enforce formatting, type safety, or security. Humans are inconsistent and have limited bandwidth for low-level checks. The pipeline must be the gatekeeper — linting, type checking, security scanning, and testing must be automated and mandatory.

**Rule**: If a machine can check it, a human should not have to.

### 5. Mixing Unrelated Changes in One PR
Combining a bug fix with a refactor, or a feature with a config change, in a single PR. This makes reviews harder, rollbacks dangerous, and blame tracking impossible. Each PR must do exactly one thing.

**Test**: Can you write a one-sentence PR description that covers everything? If not, split it.

### 6. Documentation as an Afterthought
Writing code first, then updating docs "when we have time." We never have time. "What's not on paper didn't exist." If a change isn't reflected in the documentation, the task is not done.

**Enforcement**: Documentation updates are part of the Definition of Done, not a separate ticket.

---

## Workflow

### Phase 1 — Identify the Decision

Before writing code or proposing architecture, classify the decision:

- [ ] Is this a **code-level** decision? (function design, data structure, internal pattern)
- [ ] Is this an **architecture-level** decision? (module boundary, technology choice, system interaction)
- [ ] Is this a **process-level** decision? (workflow change, tool adoption, team convention)

Different decision types require different levels of rigor. A function choice needs simplicity. A technology choice may need an ADR. A process change may need team alignment.

### Phase 2 — Ground in Principles

For each decision, check against the 8 pillars:

- [ ] **Keep It Simple** — Is the straightforward solution available? Does this require massive cognitive load to understand? If a junior developer cannot understand it in 5 minutes, it is too complex.
- [ ] **Build Atomically** — Does this change represent one logical unit? Can it be one issue, one branch, one PR? If you need "and" to describe it, split it.
- [ ] **Pipeline as Gatekeeper** — Are automated checks in place for this area? Will CI enforce the standard? If the pipeline does not check it, it will eventually be violated.
- [ ] **Leverage Automation** — Can a machine do this instead of a human? Is there toil to eliminate? If you are doing it more than twice, automate it.
- [ ] **Continuous Momentum (JIT)** — Am I designing for today's requirements, not tomorrow's? Is this the module being built *now*? If not, defer the design.
- [ ] **Collective Ownership** — Does this change need peer review? Can another developer understand and maintain it? If only you understand it, it is a bus-factor risk.
- [ ] **Documentation as First-Class** — If this changes architecture or behavior, is the documentation updated? "What's not on paper didn't exist."
- [ ] **Blameless Culture** — If this involves a past failure, am I focusing on system improvements, not individual fault?

### Phase 3 — Validate Atomicity

Every change must pass the atomicity test:

- [ ] One issue describes this change
- [ ] One branch contains this change
- [ ] One PR reviews this change
- [ ] The PR does exactly one thing and does it perfectly
- [ ] No unrelated changes are mixed in
- [ ] The PR description is one sentence that covers everything

If the change is too large, decompose it into smaller atomic units. If decomposition is impossible, document why and get explicit approval for a multi-issue PR.

**Decomposition strategy**: Split by concern (data layer, API layer, UI layer) or by behavior (create, update, delete). Never split by file.

### Phase 4 — Verify Automation Coverage

Confirm the pipeline enforces standards for the affected area:

- [ ] Linting is active for the language/framework
- [ ] Type checking is active (if applicable)
- [ ] Security scanning covers the changed code
- [ ] Tests exist and pass for the affected module
- [ ] No manual formatting or review steps replace automated checks
- [ ] CI runs on every PR, not just on merge

### Phase 5 — Document the Decision

If the decision is significant (architecture, technology, process change):

- [ ] Create an ADR if it is a technical pivot (see `workflow/log-adr` skill)
- [ ] Update the relevant RFD if it changes system design
- [ ] Update PRD if it affects product requirements
- [ ] Ensure the commit message reflects *why*, not just *what*
- [ ] Update the glossary if new terms were introduced

### Phase 6 — Review for Blamelessness

Before finalizing any change that addresses a past failure:

- [ ] Focus on what the *system* allowed, not what the *person* did
- [ ] Identify which insurance layer failed (documentation, tooling, automation)
- [ ] Propose a concrete improvement to prevent recurrence
- [ ] If the improvement requires an ADR, create one (see `workflow/log-adr` skill)

---

## Glossary

| Term | Definition | Full Reference |
|------|-----------|---------------|
| [Atomic](standards/GLOSSARY.md#atomic) | 1 Issue = 1 Branch = 1 PR. Single, discrete, testable unit of work. | Core building principle |
| [Blameless](standards/GLOSSARY.md#blameless) | Treat mistakes as system failures, not personal failures. Focus on prevention. | Incident response culture |
| [JIT](standards/GLOSSARY.md#just-in-time-jit) | Design architecture only for the module being built today. | Anti-paralysis principle |
| [Pipeline](standards/GLOSSARY.md#pipeline) | Automated CI/CD system that validates code. The gatekeeper for `main`. | Automation standard |
| [ADR](standards/GLOSSARY.md#adr-architecture-decision-record) | Document capturing a significant technical decision with context, options, and consequences. | Decision documentation |
| [RFD](standards/GLOSSARY.md#rfd-request-for-design) | Technical design document covering structure, data, interfaces, logic, and operations. | Architecture documentation |
| [PRD](standards/GLOSSARY.md#prd-product-requirements-document) | Product requirements document covering vision, stories, and context. | Product documentation |
| [Vertical Slice](standards/GLOSSARY.md#vertical-slice) | One test → one implementation → repeat. Deliver complete behaviors incrementally. | Delivery strategy |

---

## Chaining

This skill is the **root** of the standards chain. It has no upstream parents.

**Downstream**:
- `standards/design-api` — API design decisions are grounded in simplicity and documentation
- `standards/write-tests` — Blameless culture depends on test safety nets
- `standards/shift-left-security` — Automation principle enforces security checks in CI
- [`polish-ui`](EXTERNAL.md#emil-kowalski-skills) (externa) — UI polish and animation principles inherit from simplicity and automation
- `workflow/log-adr` — Significant technical decisions require ADR documentation
- `workflow/write-commits` — Atomic building requires conventional commits
- `workflow/create-pr` — Collective ownership requires peer review

**Cross-cutting**: Every skill inherits these 8 principles. When a downstream skill's rules conflict, return here to resolve using the principle hierarchy (user-facing > automation > documentation > simplicity).

**Conflict resolution**: When two principles seem to conflict (e.g., "Keep It Simple" vs "Documentation as First-Class"), ask: "Which failure mode is more likely to cause harm in this context?" Apply the principle that prevents the more costly failure.

---

## Decision Framework

When facing a significant decision, use this framework to apply the principles systematically:

### Step 1: Classify the Decision

| Decision Type | Rigor Level | Documentation Required | Example |
|--------------|------------|----------------------|---------|
| Code-level | Light | Commit message | Choosing a data structure |
| Architecture-level | Medium | ADR | Choosing REST vs GraphQL |
| Process-level | High | ADR + Team alignment | Adopting a new CI tool |
| Technology-level | High | ADR + RFD update | Switching database |

### Step 2: Check for Existing Precedent

Before making a decision, check:
- [ ] Has this decision been made before in this project? (Check ADRs)
- [ ] Has this decision been made before in the industry? (Check established practices)
- [ ] Is there an existing pattern in the codebase that applies?

### Step 3: Apply the Relevant Principles

Not all 8 principles apply to every decision. Focus on the ones most relevant to the failure mode you are preventing:

| Failure Mode | Primary Principle | Secondary Principle |
|-------------|------------------|-------------------|
| Over-complexity | Keep It Simple | JIT |
| Review fatigue | Build Atomically | Collective Ownership |
| Inconsistent quality | Pipeline as Gatekeeper | Leverage Automation |
| Knowledge silos | Documentation as First-Class | Collective Ownership |
| Incident blame | Blameless Culture | Documentation as First-Class |
| Scope creep | JIT | Build Atomically |

### Step 4: Document and Communicate

- [ ] Decision is documented (ADR, RFD, or commit message)
- [ ] Team is notified if the decision affects workflows
- [ ] Downstream skills are updated if the decision changes standards
