---
name: spell-out
description: "Grill a plan before building — iterative questioning about intent, scope, trade-offs, edge cases, and dependencies. Use before starting implementation, during design review, or when a proposal feels incomplete."
license: MIT
trigger: "/spell-out, spell out, grill, plan review, design review, questioning, challenge, pre-build review, plan validation, intent check"
metadata:
  author: Matt Pocock
  version: "1.0.0"
  derived_from: "mattpocock/skills — grill-me"
  porter_note: "Design-First means question before build. This is the gate between design and implementation — do not skip it."
---

# Spell Out

## Philosophy

### Design-First means question before build

The most expensive mistake in software is not a bad implementation — it is a bad specification that was implemented perfectly. A flawless implementation of the wrong feature delivers negative value: it costs time, consumes budget, and must be undone.

Spelling out is the gate that catches specification mistakes before they become implementation failures. It is the cheapest quality control mechanism available: a conversation that costs minutes prevents days of wasted work. Every hour spent clarifying a plan before building saves three to ten hours of rework after building.

### Questioning is a skill, not a personality trait

Effective questioning is learned and practiced. The first time you challenge a plan, it feels uncomfortable. The tenth time, it feels natural. The hundredth time, it is invisible — just part of how the team works.

The skills of spelling out are: asking "why" without sounding accusatory, probing for edge cases without seeming pedantic, and pushing for specificity without being dismissive. These are teachable, learnable, and essential for any team that ships reliable software.

Every plan has assumptions. Every requirement has ambiguity. Every timeline has hidden dependencies. The goal of spelling out is not to slow down — it is to surface these invisible risks before they become expensive failures.

The most expensive bug is the one that requires a design change. Catching a flawed assumption before a line of code is written costs minutes. Catching it after implementation costs days. Catching it after shipping costs customers.

### Good questions reveal good answers

You do not need to know the answer to ask a good question. The process of questioning forces clarity. If a requirement cannot survive five minutes of honest questioning, it will not survive implementation.

Ask iteratively. Each answer reveals the next question. The goal is not to reach "no more questions" — it is to reach shared understanding of what is known, what is assumed, and what is deferred.

### The plan is the property of the team

Spelling out is not adversarial. It is collaborative defense against shared failure. A challenged assumption is not an attack on the author — it is insurance for everyone who depends on the plan working. Blameless culture (SSOT pillar) applies here: the plan can be wrong without the planner being wrong.

---

## Anti-Patterns

### 1. Blind Coding into Ambiguity

Starting implementation when core questions are unanswered. "We'll figure it out during development" is not agility — it is deferred risk that compounds with every line of code.

**Fix**: Before writing code, confirm: is the intent clear? Is the scope bounded? Are the trade-offs understood? If the answer to any is "not yet," spell out first.

### 2. Ignoring Edge Cases

Focusing only on the happy path during planning. Edge cases are where bugs live, systems fail, and users get frustrated. A plan that only covers the happy path is incomplete.

**Fix**: Ask "what happens when X goes wrong?" for every critical path. Cover empty states, error states, boundary conditions, and concurrent access.

### 3. Assuming Requirements Are Stable

Treating the first articulation of a requirement as the final one. Requirements evolve when you question them — that is the point of spelling out.

**Fix**: Challenge every requirement. "Is this actually needed?" "What problem does this solve?" "What would happen if we did not do this?" Surface the real need behind the stated requirement.

### 4. Ignoring Trade-Offs

Every decision has a cost. A faster implementation might mean less flexibility. A simpler solution might mean more manual work. Pretending there are no trade-offs leads to surprises down the road.

**Fix**: For every significant decision, document the trade-off explicitly: "Choosing X because it gives us Y, at the cost of Z."

### 5. Confusing "Quick Question" With "Spelled Out"

Treating a 30-second skim as a proper review. A quick question catches surface issues but never reveals the deep assumptions, edge cases, or unstated dependencies that cause failures mid-implementation.

**Fix**: Spell out is a structured process with documented phases and explicit outputs. It takes time proportional to the plan's complexity. A 30-second skim does not qualify.

### 6. Defensive Reactions to Challenges

Treating questions about the plan as personal criticism. This poisons the review process — people stop raising concerns, and risks go unexamined until they become failures.

**Fix**: Frame every question as "help me understand" rather than "you are wrong." The planner's job is to welcome scrutiny. The reviewer's job is to challenge with respect. Both serve the same goal: a plan that works.

---

## Workflow

### Phase 1 — Clarify Intent

Before discussing scope or implementation, confirm what the plan is meant to achieve:

- [ ] What problem does this solve? State it in one sentence
- [ ] Who is the user or beneficiary?
- [ ] What does success look like? Measurable outcome preferred
- [ ] Why now? What changed to make this a priority?
- [ ] What happens if we do nothing? Is the status quo acceptable?

**Output**: A one-paragraph problem statement that the team agrees on. If the team does not agree on the problem, every subsequent decision will be misaligned.

**Probing questions for deeper clarity**:
- "Who exactly experiences this problem? Can we name the user or persona?"
- "How do we know this is a real problem? What data or evidence supports it?"
- "What have we tried before to solve this, and why did it not work?"
- "Is this the real problem or a symptom of a deeper problem?"
- "What would be the smallest experiment that validates this is the right problem to solve?"

### Phase 2 — Bound the Scope

With the problem confirmed, define what is in scope and what is explicitly out:

- [ ] What is the minimum viable change that solves this problem?
- [ ] What is explicitly out of scope for this iteration? (Be specific — "future work" is too vague)
- [ ] What are the dependencies this work requires? (Other teams, libraries, infrastructure, data)
- [ ] What is the estimated effort? (Lines changed, files touched, days of work)
- [ ] Is there a deadline or external constraint driving scope?

**Heuristic**: If the scope cannot be described in 3-5 bullet points, it is not scoped yet. Keep drilling until you can.

### Phase 3 — Surface Trade-Offs and Edge Cases

Challenge the plan from multiple angles:

- [ ] **Edge cases**: What happens with empty input, network failure, concurrent access, max data size, rapid repeated triggers?
- [ ] **Trade-offs**: For each significant decision, name what we are giving up. Speed vs flexibility? Simplicity vs capability? Consistency vs velocity?
- [ ] **Failure modes**: What is the most likely thing to go wrong? What is the worst thing that could go wrong?
- [ ] **Rollback**: If this needs to be undone, what is the rollback plan? Can we revert cleanly?
- [ ] **Monitoring**: How will we know if this works in production? What metrics change?

**Output**: A list of surfaced risks with mitigation plans. Each risk gets: description, likelihood (low/med/high), impact (low/med/high), and mitigation.

**Risk table format**:
| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| [Description of what could go wrong] | Low/Med/High | Low/Med/High | [What we will do if it happens] |
| [Next risk] | ... | ... | ... |

**Distributed questions for each angle**:
- *Security*: Does this introduce new attack surface? How are credentials handled?
- *Performance*: What happens at 10x the expected load? At 100x?
- *Data integrity*: What happens if a write succeeds but the read fails? What about partial writes?
- *Concurrency*: What happens when two users perform this action simultaneously?
- *Observability*: Can we trace a failure through the system? Are the right metrics in place?
- *Compliance*: Does this touch regulated data (PII, financial, healthcare)?
- *Third-party risk*: What if the external API changes, goes down, or rate-limits us?
- *Browser/device*: Does this work on all target browsers? What about offline?

### Phase 4 — Update Documentation

- [ ] Update `CONTEXT.md` with any resolved terms or clarified decisions
- [ ] Offer ADRs for significant decisions that impact architecture or long-term strategy
- [ ] Document deferred questions — things intentionally left for later
- [ ] Update the plan artifact with the findings from the spell-out session

**Output**: Updated context documentation + ADR records for decisions that change the project's trajectory.

### Phase 5 — Get Approval to Proceed

- [ ] Present the clarified plan to stakeholders
- [ ] Confirm the scope and trade-offs are accepted
- [ ] Confirm the risk list is acceptable
- [ ] Get explicit go-ahead before implementation begins

**Gate rule**: If Phase 1-3 revealed a fundamental flaw in the plan (wrong problem, impossible scope, unacceptable risk), do NOT proceed. Return to `workflow/specify-solution` or `workflow/define-product` to redesign.

### Phase 6 — Follow Up and Validate

Spelling out does not end when approval is given. Track whether the surfaced risks materialize and whether the mitigations worked:

- [ ] Schedule a follow-up after implementation begins — do the assumptions still hold?
- [ ] When a surfaced risk materializes, assess: was the mitigation plan sufficient?
- [ ] Log the outcomes: what risks were overestimated? What risks were missed entirely?
- [ ] Use the results to calibrate future spell-out sessions — what types of questions are most valuable for this team and domain?

**Feedback loop**: Each spell-out session improves the next. Keep a running list of questions that consistently surface valuable information. Drop questions that never find anything. The practice evolves as the team and product mature.

---

## Glossary

| Term | Definition | Source |
|------|-----------|--------|
| Design-First | Principle: question before build. Know what you are building and why before writing code. | Philosophy |
| Deferred Risk | Risk that is postponed rather than eliminated. Compounds with every line of code written. | Planning |
| Trade-Off | The cost of choosing one option over another. Must be explicit and documented. | Decision-making |
| ADR | Architecture Decision Record — captures context, decision, and consequences for significant choices. | Standards |
| Risk Mitigation | A planned response to a surfaced risk — what we will do if the risk materializes. | Planning |
| Happy Path | The primary, expected execution path where everything works as intended. Not sufficient for planning. | Testing |

---

## Chaining

**Upstream**:
- `workflow/specify-solution` — The RFD is the artifact being spelled out; the plan under review
- `workflow/define-product` — Product requirements set the intent that Phase 1 validates

**Downstream**:
- `workflow/plan-issues` — After spelling out, break clarified scope into executable issues
- `tools/init-repo` — Decisions surfaced during spelling out may affect project structure
- `workflow/log-adr` — Significant trade-offs discovered during spelling out must be captured as ADRs

**Cross-cutting**: Spell out is optional for trivial changes but mandatory for any change that touches architecture, external dependencies, or user-facing behavior. The orchestrator decides when to invoke this skill based on risk assessment.
