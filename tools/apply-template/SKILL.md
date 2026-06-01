---
name: apply-template
description: "Reference and apply SSOT templates — PRD, RFD, ADR, Views, Issues, Milestones. Guide agent through each template section with purpose and guidance. Use when creating a new document, reviewing a filled template, or deciding which template to use."
license: MIT
trigger: "/apply-template, apply template, template, PRD template, RFD template, ADR template, views template, issue template, milestone template, document template"
metadata:
  author: Joel Abreu Rojas
  version: "1.0.0"
  ssot_chapter: "03: Templates & Tooling"
  porter_note: "Templates are not forms to fill — they are checklists of questions to answer. Understand each section's purpose before writing."
---

# Apply Template

## Philosophy

### A template is a checklist, not a form

Every section in a template exists to prompt a specific decision. The "Problem Statement" section is not a text field to fill — it is a question: "Can you state the problem in one sentence?" If you cannot, the document is not ready.

Approach each template section as a question that must be answered. If the answer is "we do not know yet," that is honest — mark it as deferred and address it in the next iteration. A blank section with a known gap is better than a filled section that says nothing.

Templates are thinking tools, not paperwork. The value is not in the completed document — it is in the thinking that filling it out forced you to do. If you filled every section without thinking harder about any of them, you used the template wrong.

### The right template at the right time

Each template corresponds to a specific phase in the SSOT lifecycle:

| Template | Phase | Question It Answers |
|----------|-------|-------------------|
| PRD | Define | What are we building and why? |
| RFD | Design | How will it work technically? |
| ADR | Decide | Why did we choose X over Y? |
| Views | Specify | What does it look like in every state? |
| Issues | Execute | Who does what and in what order? |
| Milestones | Group | What set of work delivers value? |

Using the wrong template creates friction for the wrong question. A PRD template cannot produce a good RFD, and an Issue template cannot capture a roadmap decision. Starting with the wrong template wastes time and produces a document that satisfies no one.

### Templates are teaching tools

A well-designed template teaches the author what matters. The ADR template asks for "Context" because the most important part of an architecture decision is understanding why it was made. The PRD template asks for "Out of Scope" because the most common PRD failure is unbounded scope.

New team members learn the team's documentation standards by filling templates. Experienced team members are reminded of what not to forget. Templates encode collective knowledge into a repeatable format.

### Templates enable automation

Templates are not just for humans. Structured documents with consistent section headings enable automated processing: CI checks for required sections, AI agents that can parse and summarize, changelog generators that extract key decisions.

Consistency is what makes this possible. Every ADR follows the same format. Every PRD has the same sections. Automation is the payoff for template discipline. Without consistent templates, every automated tool needs custom parsing for every document.

---

## Anti-Patterns

### 1. Filling Without Understanding

Writing text in every section without understanding why the section exists. The template becomes a chore to complete rather than a tool for thinking.

**Fix**: Before writing, read each section heading and ask: "What question is this asking?" If you cannot articulate the question, research the template's purpose first.

### 2. Skipping Sections

Leaving sections blank or writing "N/A" without consideration. If a section truly does not apply, explain why it does not apply. The reader needs to know it was considered, not forgotten.

**Fix**: Every section gets either substantive content or a deliberate deferral: "Deferred until [trigger condition] because [reason]."

### 3. Rigid Adherence Without Adaptation

Filling a template exactly as-is even when the project's needs differ. Templates are starting points, not straightjackets. If a template section consistently produces unusable content for your project, adapt it.

**Fix**: After two uses of a template, review: which sections provided value and which did not? Adapt the template to your domain. The SSOT templates are reference implementations, not immutable standards.

### 4. Using the Wrong Template for the Job

Writing an RFD when a PRD is needed, or using an Issue template to capture a strategic decision. The document will frustrate readers because it asks the wrong questions.

**Fix**: Before starting a document, confirm the phase of work and select the corresponding template. If unsure, ask: "What question are we trying to answer?"

### 5. Over-Writing the Template

Filling every section with excessive detail, turning a concise decision record into a novel. Templates have a purpose — capturing essential information. Padding them with unnecessary detail buries the signal.

**Fix**: Be concise. Each section should answer its question and stop. If a section needs more than a paragraph, consider whether it should be its own document.

### 6. Ignoring Template Updates

Using an old version of a template after the team has updated it. The team improved the template based on experience — using the old version means missing those improvements.

**Fix**: Check if the template has been updated before starting a new document. Templates evolve as the team learns what matters and what does not.

---

## Workflow

### Phase 1 — Determine the Right Template

Before opening any template, confirm which one the situation requires:

- [ ] What phase is the project in? (Define, Design, Decide, Specify, Execute, Group)
- [ ] What question needs to be answered?
- [ ] Select the matching template:

| Situation | Template | Location | Reference Tool |
|-----------|----------|----------|---------------|
| New feature or product | PRD | `templates/PRD/` | `workflow/define-product` |
| Technical design decision | RFD | `templates/RFD/` | `workflow/specify-solution` |
| Architecture choice between options | ADR | `templates/ADR/` | `workflow/log-adr` |
| Visual design specification | Views | `templates/VIEWS/` | `workflow/design-views` |
| Break work into tasks | Issues | `templates/milestones/` | `workflow/plan-issues` |
| Group work into delivery units | Milestone | `templates/milestones/` | `workflow/define-milestone` |

- [ ] If the situation covers multiple phases, start with the earliest one. A new feature needs a PRD before an RFD.

### Phase 2 — Locate and Open the Template

Templates live in the project's `templates/` directory, installed by `init-repo`. Each template type has its own subdirectory:

| Template | Path | Files |
|----------|------|-------|
| PRD | `templates/PRD/` | `README.md`, `01_vision.md`, `02_user_stories.md`, `03_requirements.md`, `04_context.md` |
| RFD | `templates/RFD/` | `README.md`, `01_structure.md`, `02_data_model.md`, `03_interface.md`, `04_logic.md`, `05_ops_security_quality.md` |
| ADR | `templates/ADR/` | `000_template.md` |
| Views | `templates/VIEWS/` | `README.md`, `00_template.md` |
| Milestones | `templates/milestones/` | `milestone_blueprint.md`, `issue_task_template.md` |

- [ ] Locate the correct template directory — if templates are not installed, run `init-repo` first
- [ ] Read every section heading before writing anything
- [ ] For each section, identify the question it is asking. Write the question in your own words
- [ ] Note which sections are required (must be filled) vs optional (fill if applicable)
- [ ] Identify sections where you lack information — mark these for research or deferral
- [ ] Check if the template includes cross-references to other documents that should exist
- [ ] Assess the document's expected audience — who will read this and what do they need to know?

**Example**: Opening the ADR template, you see sections: Context, Decision, Consequences. The questions are:
- Context: "What forced this decision? What was the situation?"
- Decision: "What did we choose, in one sentence?"
- Consequences: "What becomes easier and what becomes harder because of this choice?"

Understanding the questions before writing prevents answering the wrong question.

### Phase 3 — Fill Each Section with Intent

For each section in order:

- [ ] **Write the purpose**: Answer the question the section is asking
- [ ] **Be specific**: Avoid vague language. Use concrete examples, measurements, and references
- [ ] **Reference other documents**: ADRs should reference the RFD they document. PRDs should reference the roadmap.
- [ ] **Mark unknowns**: If a question cannot be answered yet, mark it as "Deferred: [condition to resolve]" rather than guessing
- [ ] **Use the right format**: Tables for comparisons, diagrams for architecture, lists for requirements

**Section guidance for each template**:

**PRD sections**:
- Problem Statement: One sentence, one paragraph max. If you cannot state the problem in one sentence, you do not understand it well enough to build a solution.
- User Stories: Who, what, why. Format: "As a [user], I want to [goal] so that [reason]." Each story is a distinct user need.
- Requirements: Measurable, testable. Not implementation details. "The page must load in under 2 seconds" not "Use React.lazy for code splitting."
- Out of Scope: Explicit "we are NOT doing this" list. This is as important as what IS in scope — it prevents scope creep and sets expectations.
- Success Metrics: How will we know this worked? Quantitative preferred (conversion rate, load time, error rate).

**RFD sections**:
- Architecture: Module boundaries, data flow diagram (Mermaid), dependency direction. Show the boxes and arrows before describing the content.
- Data Model: Entities, relationships (ERD), migration plan. New fields, new tables, data migration strategy.
- API: Contract (request/response shapes), error codes, rate limits, authentication. Include examples for every endpoint.
- Operations: Monitoring (what to watch), deployment (migration order, feature flags), rollback (revert steps, data recovery).
- Testing Strategy: How will each layer be tested? Unit, integration, E2E coverage boundaries.

**ADR sections**:
- Context: What forced the decision? What was the situation, constraint, or problem that made a decision necessary?
- Decision: What was chosen, one sentence. "We chose Postgres over MySQL."
- Rationale: Why this choice over alternatives. List the options considered and why each was rejected.
- Consequences: What becomes easier and harder as a result. "Easier: JSONB support for flexible schemas. Harder: Read replicas require more careful setup than MySQL."
- Status: Proposed, Accepted, Deprecated, Superseded. ADRs can change — track the status.

**Views sections**:
- States: Loading, empty, error, edge case — all four, every component. A button has a loading state, a list has an empty state, a form has an error state.
- Component Inventory: Every UI element that needs specification. List every component, its states, and its behavior.
- Behavior: Interaction details, transitions, edge cases. What happens on hover, on click, on error, on success?
- Visual Design: Color, typography, spacing, responsive breakpoints. Reference the design system tokens.

### Phase 4 — Review for Completeness

- [ ] Every required section has content
- [ ] Every optional section is either filled or explicitly deferred with a reason
- [ ] Cross-references to other documents are accurate and link to existing files
- [ ] The document answers the question it was created to answer
- [ ] The document is self-contained enough for a new reader to understand without asking questions
- [ ] Consistency check: Does this document agree with related documents? If not, flag the inconsistency.

### Phase 5 — Save and Chain to Next Phase

- [ ] Save the filled template to the correct directory (`docs/PRD/`, `docs/RFD/`, `docs/ADR/`, etc.)
- [ ] Link the new document from `CONTEXT.md` or the project index so it is discoverable
- [ ] If this document triggers the next phase (PRD → RFD, RFD → Views, etc.), notify the relevant skill
- [ ] If the document reveals decisions that need ADRs, create those ADRs now
- [ ] Commit the document following Conventional Commits: `docs(scope): add [document type] for [feature name]`

**Examples of good commit messages for template-filed documents**:
- `docs(auth): add PRD for JWT refresh token rotation`
- `docs(api): add RFD-007 for rate limiting middleware`
- `docs(architecture): add ADR-023 for Postgres over MySQL`
- `docs(search): add views specification for search results page`

**Examples of cross-linking**:
- PRD references: "See RFD-004 for technical design" and "See ADR-012 for the auth provider decision"
- RFD references: "As specified in PRD-003 Section 4.2 (Performance Requirements)"
- ADR references: "This ADR supersedes ADR-015" and "See ADR-018 for the database migration strategy"

---

## Glossary

| Term | Definition | Source |
|------|-----------|--------|
| PRD | Product Requirements Document — defines what to build and why. | Template |
| RFD | Request for Design — technical specification describing how something will be built. | Template |
| ADR | Architecture Decision Record — captures a significant decision with context and consequences. | Template |
| Deferred Decision | A question intentionally left unanswered until a specific trigger condition is met. | Decision-making |
| Cross-Reference | A link from one document to another that provides context or dependency information. | Documentation |

---

## Chaining

**Upstream**:
- `tools/init-repo` — Installs templates into the project's `templates/` directory during scaffolding
- `workflow/define-product` — Creates PRDs using the PRD template
- `workflow/specify-solution` — Creates RFDs using the RFD template
- `workflow/log-adr` — Creates ADRs using the ADR template
- `workflow/design-views` — Creates view specs using the Views template
- `workflow/plan-issues` — Creates issues using the Issue template
- `workflow/define-milestone` — Creates milestone definitions using the Milestone template

**Downstream**:
- None — Apply-template is a reference utility. It is called BY the workflow skills listed above. It does not chain to other skills.

**Cross-cutting**: Template quality affects every document in the project. If a template consistently produces unusable documents, improve the template, not the document. This skill is a meta-guide — it teaches how to use templates, not what specific templates to use. Actual template files live in `templates/` and are installed by `tools/init-repo`.
