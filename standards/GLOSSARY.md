# Standards Glossary

Shared terms used across skills. Each skill's `## Glossary` section links here for full definitions.

---

## Core Concepts

### Atomic
**1 Issue = 1 Branch = 1 PR.** A single, discrete, testable unit of work. No unrelated changes mixed together. Prevents review fatigue, reduces merge conflicts, and makes rollbacks safe and targeted.

### Blameless
A culture where mistakes are treated as system failures, not personal failures. If a developer makes an error, it is a failure of documentation, tooling, or automated checks. Focus on mitigation and prevention — every mistake improves the "insurance" layers.

### Just-In-Time (JIT)
Design technical architecture only for the module being built *today*. Avoid "Analysis Paralysis" by solving for today's known requirements, not tomorrow's hypothetical ones. Defined in SSOT's Continuous Momentum principle.

### Idempotent
An operation that produces the same result whether called once or multiple times. `GET`, `PUT`, `PATCH`, and `DELETE` are idempotent by definition. For critical `POST` mutations, use `Idempotency-Key` headers to handle client retries safely.

### Vertical Slice
A single path through the system that delivers user-visible value. Unlike horizontal slices (all tests then all code), vertical slices deliver one complete behavior at a time: test → implement → repeat.

### Squash Merge
A merge strategy that compresses all commits on a feature branch into a single, clean commit on `main`. The final commit includes the PR ID for traceability. The branch is deleted automatically after merge. Keeps history linear, clean, and bisectable.

### Stacked PR
A PR that targets another open PR's branch instead of `main`. Used when a task depends on unfinished work in another PR. Allows reviewers to see only the relevant diff for their specific task.

### Stacked Branch
A branch created off an active feature branch instead of `main` when a task depends on unfinished work in the parent branch. Each stacked branch produces its own PR but targets the immediate parent branch, not `main`.

### Breaking Change
A change that requires consumers to update their code. In SemVer, signaled by a MAJOR version bump. In commits, marked with `!` suffix (e.g., `feat!:` or `fix!:`). Must be documented in the CHANGELOG with migration instructions.

---

## Architecture & Design

### C4 Model
A hierarchical system visualization framework:
- **Level 1 (Context):** System interactions with users and external entities
- **Level 2 (Containers):** High-level technical blocks (Web, API, DB)
- **Level 3 (Components):** Internal logic flow (Services, Repositories)

Never combine all three levels in a single diagram.

### Stateless
A system design where no session state is stored between requests. Any persistence is explicitly delegated to a defined storage layer. Default design principle for APIs.

### Circuit Breaker
A resilience pattern that stops traffic to a failing service, allowing recovery time while providing immediate error responses. Used for high-volume dependencies.

### Graceful Degradation
A "Plan B" for critical path failures. If a non-essential service fails, the system degrades to a functional fallback (e.g., cached result, simplified default) rather than crashing.

### Dependency Boundary
Explicit rules in an RFD defining which modules may communicate and which imports are prohibited. Prevents circular dependencies and "spaghetti" coupling.

### Mermaid
A markdown-compatible diagramming language used in RFDs and ADRs to visualize architecture, flows, and state machines. Diagrams are rendered inline by GitHub and other markdown renderers.

### Data Contract
A shared type definition for data flowing between backend and frontend. Defined in the RFD interface section. View specs reference RFD data contracts rather than inventing their own shapes. Prevents drift between what the UI expects and what the API provides.

### Traceability
A bi-directional link between artifacts across the documentation hierarchy. Every user story links to a business goal. Every RFD section traces back to a PRD story. Every view file references a story ID. An artifact without traceability has no justification.

---

## Security

### Principle of Least Privilege (PoLP)
Every user, service, and infrastructure component must be granted only the minimum permissions necessary. Database roles restricted to specific tables. IAM roles define explicit allowed actions. Production access revoked immediately after debugging.

### Shift-Left
Security checks and reviews happen as early in the development lifecycle as possible. Security is not a feature — it is a fundamental requirement present from day one.

### OIDC (OpenID Connect)
For cloud deployments, use OIDC trust relationships instead of long-lived, static access keys. Temporary, scope-limited credentials are generated only when the pipeline runs.

### Supply Chain Security
Defending against attacks that compromise open-source dependencies rather than your code directly. Mitigated via Dependabot/Renovate for automated updates, security advisory monitoring, and SAST (CodeQL) in CI.

### OWASP Top 10
The Open Web Application Security Project's list of the ten most critical web application security risks. Used as a baseline for security reviews and threat modeling. Includes categories like Injection, Broken Authentication, and XSS.

---

## Testing

### Test Pyramid
The recommended ratio of test types:
- **Unit Tests (70%):** Fast, isolated, single-function verification. Mock all external dependencies.
- **Integration Tests (20%):** Verify wiring between components with real or test-instance databases and services.
- **E2E Tests (10%):** Simulate real user interactions on the fully deployed system. Focus on critical paths only.

### AAA (Arrange, Act, Assert)
Every test must visually separate setup (Arrange), execution (Act), and verification (Assert). A structural convention that keeps tests readable and maintainable.

### Deterministic
Tests that pass or fail consistently. Flaky tests (random failures from network latency, timing, or shared state) destroy trust in CI and must be fixed or deleted immediately.

### Isolated State
Tests must never depend on execution order or data left by previous tests. Each test sets up its own state and cleans up after itself.

### TDD (Test-Driven Development)
Write a failing test before writing the implementation. Mandatory for bug fixes: reproduce the bug with a failing test, then fix until green. Encouraged but not strictly required for features.

### Acceptance Criteria
Objective, testable conditions that define when a user story or issue is complete. Each criterion must be pass/fail — if you cannot write a test for it, rewrite until you can.

---

## Versioning & Releases

### SemVer (Semantic Versioning)
`MAJOR.MINOR.PATCH` versioning scheme. Breaking changes require a new MAJOR. New features require a MINOR. Bug fixes require a PATCH. Manual version bumps without CI are an anti-pattern.

### CHANGELOG
A human-readable log of all notable changes, organized by version. The "Unreleased" section accumulates changes until a release is cut, at which point it is moved to the versioned section.

### Conventional Commits
Commit message format: `type(scope): subject`. Types include `feat`, `fix`, `chore`, `docs`, `refactor`, `test`, `ci`. Title ≤50 chars. Body explains *why*, not *how*.

### Scope
In the context of Conventional Commits, the optional `(scope)` portion of a commit message that identifies the module or area affected by the change. Written in lowercase kebab-case (e.g., `feat(auth):`). Provides additional context for changelog generation.

### Git Tag
A named reference to a specific commit, typically used to mark release points. Tags follow the version number format (e.g., `v1.2.3`). Signed tags are preferred for verified releases.

### GitHub Release
A release object created from a Git tag that includes release notes, compiled assets, and changelog entries. Published from the Releases tab in the GitHub UI or via CLI with `gh release create`.

---

## Process

### ADR (Architecture Decision Record)
A document capturing a significant technical decision: context, options considered, the decision itself, and consequences. Status flows from `Proposed` → `Accepted` (before merge) → `Deprecated` or `Superseded`.

### RFD (Request for Design)
A technical design document covering structure, data model, interfaces, logic, and operations. Uses Mermaid diagrams for visualization. Must include a fallback/contingency plan.

### PRD (Product Requirements Document)
A product-facing document covering vision, user stories, requirements, and context. Focus on *what* and *why*, not *how*. Has a live status field tracking its lifecycle.

### Execution Order
A numeric ordering assigned to issues/tasks that defines the sequence of implementation. Each issue has an `Order` field and a `Depends On` field for dependency tracking.

### Pipeline
The automated CI/CD system that validates code via linting, type checking, security scanning, and testing. The pipeline is the gatekeeper — no code reaches `main` unless all checks are green.

### Milestone
A high-level epic representing the complete development of a single business module or core feature set (e.g., User Management, Billing, Analytics). Domain-based, not time-based. Groups related issues into a coherent delivery unit with a shared Definition of Done.

### Epic
A large body of work that can be broken down into smaller, independently executable issues. Used interchangeably with Milestone in this context. Must have a clear scope boundary — if it covers multiple business domains, split it.

### Definition of Done (DoD)
The completion contract for a milestone or issue. Defines what "finished" means across functional outcome, vertical slice completeness, technical integrity (tests, ADRs), documentation sync, and release criteria. Must be pass/fail verifiable.

### User Story
A user-centric narrative format: "As a [persona], I want [action], so that [outcome]." Captures a feature from the user's perspective. Each story has Acceptance Criteria that make it testable. Stories trace back to personas and business goals.

### Non-Functional Requirements (NFR)
System constraints and quality attributes: performance (response time targets), security (authentication, compliance), scalability (user count, data volume), reliability (uptime SLAs, RTO/RPO), and accessibility (WCAG level). Defined in the PRD to give the RFD a design budget.

### Status Flip
The ceremony of changing a document's status from `Proposed` to `Accepted`. Tied to the PR approval event — a PR is approved, the status is flipped, then Squash and Merge. Prevents documents from lingering in "Proposed" indefinitely.

### Lock Rule
Requirements for a module are frozen once it enters the "NOW" column of the Roadmap. Prevents scope creep during active development. If a requirement change is needed after lock, it goes through a formal PR process with a new Status Flip.

### KPI (Key Performance Indicator)
A measurable value that tracks the success or impact of a delivered feature. Defined in the PRD context file. Examples: user adoption rate, response time p95, conversion funnel completion. Must be measurable without custom instrumentation.

### Now/Next/Later
A roadmap prioritization framework. **NOW** (🟢) — actively being designed or built, requirements are Locked. **NEXT** (🟡) — queued for the next cycle, high-level scope defined. **LATER** (⚪) — acknowledged but not scheduled, one-line descriptions only.

### Superseded
An ADR status indicating that a previous decision has been replaced by a new one. The superseded ADR is not deleted — it remains in the repository as historical context, with a link to the superseding ADR.

### Depends On
A dependency relationship between issues. Issue A depends on Issue B if A cannot start until B is complete. Documented in the issue's `Depends On` field or in the milestone's Execution Order sequence.

### Self-Review
The practice of reading through one's own "Files Changed" tab before requesting peer review. Catches typos, debug logs, commented-out code, and logic errors that the author can fix in seconds. The first line of quality defense.

### Status Checks
Automated CI validations (linting, tests, security scans, build verification) that must pass before a PR can be merged. Enforced via branch protection rulesets. No bypasses, no exceptions.

### Conversation Resolution
The requirement that all review comments on a PR must be explicitly resolved before merge. Ensures no feedback is ignored, no open question is forgotten, and every concern has been addressed (either fixed or explained).

### Rulesets
GitHub branch protection rules that enforce merge policies automatically: require PRs, require approvals, require status checks, require conversation resolution, and restrict merge strategies. The automated enforcement layer that makes branching discipline mandatory rather than aspirational.

### Hotfix
A critical production bug fix that may jump the queue ahead of the current roadmap. Still requires a PR, CI checks, and peer review — speed does not bypass quality. Branch prefix: `hotfix/`.

---

## UI & Design

### Master Prompt
A structured markdown specification that serves as both human-readable documentation and AI generation input. Includes screen purpose, component inventory, data contracts, interaction logic, and state specifications. Designed to be fed directly to AI UI generation tools.

### Component Inventory
A strict, enumerated list of every functional zone in a view (e.g., Global Search, Sidebar, Data Grid, Action Bar). Defines what each zone displays and its data source. Prevents ambiguity about what a view contains.

### State Specification
The explicit definition of all four UI states for every view: Initial/Empty (what the user sees before data), Loading (how progress is communicated), Error (how failures display), and Success (confirmation and next actions). No view is complete without all four states defined.
