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

---

## Versioning & Releases

### SemVer (Semantic Versioning)
`MAJOR.MINOR.PATCH` versioning scheme. Breaking changes require a new MAJOR. New features require a MINOR. Bug fixes require a PATCH. Manual version bumps without CI are an anti-pattern.

### CHANGELOG
A human-readable log of all notable changes, organized by version. The "Unreleased" section accumulates changes until a release is cut, at which point it is moved to the versioned section.

### Conventional Commits
Commit message format: `type(scope): subject`. Types include `feat`, `fix`, `chore`, `docs`, `refactor`, `test`, `ci`. Title ≤50 chars. Body explains *why*, not *how*.

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
