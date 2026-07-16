---
name: design-api
description: "Design predictable, resilient APIs and architectural boundaries. REST nouns/plural, HTTP methods, versioning, idempotency, error envelopes, resilience patterns (timeouts, retries, circuit breakers, graceful degradation), and internal automation contracts. Use when designing endpoints, defining contracts, handling errors, setting up system boundaries, or reviewing API architecture."
license: MIT
trigger: "/design-api, design endpoints, API contract, error handling, versioning, idempotency, REST, GraphQL, system boundaries, circuit breaker, timeout, fallback, error envelope"
metadata:
  author: Joel Abreu Rojas
  version: "1.0.0"
  ssot_chapter: "04: Architecture & API Standards"
---

# Design API

## Philosophy

APIs are formal contracts. A broken contract breaks every consumer. Predictable, visually documented, and resilient APIs are not optional — they are the minimum bar for production systems.

Architecture must actively anticipate failure. Every system boundary needs timeouts, retries, circuit breakers, and fallbacks. If you cannot describe what happens when a dependency fails, you have not designed the system — you have designed the happy path.

REST and GraphQL are tools, not religions. Choose based on the problem. REST for resource-oriented CRUD. GraphQL for complex, nested data requirements with multiple consumers. Neither is universally superior. Choosing the wrong one is not a crime; failing to justify the choice is.

We utilize **Mermaid.js** and the **C4 Model** to ensure the system is comprehensible. Beyond structure, we must define the "Rules of Engagement" for components: which modules are permitted to communicate, which imports are strictly prohibited, and how state is managed.

---

## Anti-Patterns

### 1. Verbs in URLs
Using `/createUser`, `/getUser`, `/deleteUser` instead of `POST /users`, `GET /users/:id`, `DELETE /users/:id`. URLs represent resources (nouns), not actions. HTTP methods are the verbs. Verbs in URLs signal that the API designer does not understand REST fundamentals.

**Fix**: Rewrite every verb-based URL as a noun. The HTTP method already expresses the action.

### 2. Exposing Stack Traces to Clients
Returning internal error details, database errors, or stack traces in API responses. This leaks implementation details to attackers and confuses legitimate consumers. Always use a standardized error envelope (RFC 7807 Problem Details).

**Fix**: Catch all internal errors. Return a structured error with `error_code`, `message`, `details`, and `trace_id`. Log the stack trace server-side.

### 3. Missing Idempotency on Critical Mutations
Processing duplicate `POST` requests for payments, resource provisioning, or any side-effecting operation. Clients will retry. Without idempotency keys, retries cause duplicate charges, duplicate records, and data corruption.

**Fix**: For critical `POST` mutations, require an `Idempotency-Key` header. Store the key and return the cached successful response on replay.

### 4. GraphQL Without Depth Limiting
Allowing arbitrary query depth in GraphQL APIs. Deeply nested queries cause exponential database load. An attacker (or accidental client) can craft a query that brings down the service. Always enforce a maximum query depth (recommended: ≤ 5 levels).

**Fix**: Configure depth limiting in your GraphQL server. Reject queries that exceed the limit with a clear error message.

### 5. No Versioning From Day 1
Exposing unversioned public APIs. You *will* make a breaking change eventually. Without versioning, you must either break all consumers simultaneously or never make breaking changes. Both are unacceptable.

**Fix**: Start with `/v1/` in the URI or version headers from the first deployment. Document the versioning policy.

### 6. Deep Nesting in Resource URLs
Creating URLs like `/users/:id/orders/:id/items/:id/variants/:id`. Deep nesting makes URLs fragile, hard to document, and difficult for clients to construct. It also signals that the resource model is not well-defined.

**Fix**: Prefer flat resource URLs with query parameters for relationships: `/items?order_id=123`. Limit nesting to 2 levels maximum.

### 7. Inconsistent Response Shapes
Different endpoints returning different response structures for the same resource. One endpoint wraps data in `data`, another returns it flat. One uses `id`, another uses `_id`. Clients must write special-case logic for every endpoint.

**Fix**: Define a standard response envelope and apply it uniformly. Document the envelope in the API's README.

---

## Workflow

### Phase 1 — Define the Contract

Before writing any endpoint code, define the complete API contract:

- [ ] Identify the **resource** (noun): what entity does this endpoint manage?
- [ ] Choose **REST or GraphQL** based on the data access pattern
- [ ] Define the **URL structure** using plural nouns: `/users`, `/orders/:id/items`
- [ ] Map **HTTP methods** to operations: GET (read), POST (create), PUT (replace), PATCH (update partial), DELETE (remove)
- [ ] Define **standard status codes**: 200, 201, 204, 400, 401, 403, 404, 500
- [ ] Document the contract in the RFD (see `workflow/specify-solution` skill)

#### REST Checklist
- [ ] URLs are plural nouns, not verbs
- [ ] HTTP methods match the operation semantics
- [ ] Status codes are standard and meaningful
- [ ] Response shapes are consistent across endpoints
- [ ] Nesting is limited to 2 levels maximum

#### GraphQL Checklist
- [ ] Mutations are named as verbs: `UpdateUserEmail`, `PublishPost`
- [ ] Query depth is limited (recommended: ≤ 5 levels)
- [ ] No N+1 query patterns (use DataLoader or equivalent)
- [ ] Schema is documented with descriptions on types and fields
- [ ] Pagination is enforced on list queries

### Phase 2 — Visualize Architecture

Use the C4 Model to document system boundaries:

- [ ] **Level 1 (Context)**: Map system interactions with users and external entities
- [ ] **Level 2 (Containers)**: Map high-level technical blocks (Web, API, DB)
- [ ] **Level 3 (Components)**: Map internal logic flow (Services, Repositories)
- [ ] Never combine all three levels in a single diagram
- [ ] If a Mermaid diagram requires scrolling, it is too complex — break it down
- [ ] Define **dependency boundaries**: which modules may communicate, which imports are prohibited

### Phase 3 — Version the API

All public or client-facing APIs must be versioned from Day 1:

- [ ] Choose versioning strategy: **URI** (`/v1/users`) or **Header** (`Accept: application/vnd.domain.v1+json`)
- [ ] Define what constitutes a **breaking change**: removing a field, changing a data type, adding a mandatory parameter
- [ ] Breaking changes require a new version (e.g., `v2`)
- [ ] Non-breaking changes (adding optional parameters, new response fields) can go in-place
- [ ] Document the versioning policy in the API's README or RFD

### Phase 4 — Design for Resilience

Every external boundary must implement resilience patterns:

- [ ] **Strict Timeouts**: Every external call (database, third-party API, LLM) has a defined timeout. No hanging requests. Period.
- [ ] **Retries with Backoff**: Transient failures trigger exponential backoff with jitter. Prevents overwhelming a recovering service.
- [ ] **Circuit Breakers**: High-volume dependencies have circuit breakers. Traffic stops to failing services; immediate error responses returned.
- [ ] **Graceful Degradation**: Critical path failures have a "Plan B." Non-essential service failure → cached result, simplified default, or meaningful error (not crash).
- [ ] **Fallback Logic**: Documented in the RFD. Every critical dependency has an explicit fallback strategy.

**Resilience pattern selection**:
| Failure Type | Pattern | Example |
|-------------|---------|---------|
| Temporary network issue | Retry with backoff | Database connection timeout |
| Sustained dependency failure | Circuit breaker | Third-party API down for minutes |
| Non-critical service failure | Graceful degradation | AI recommendation engine offline → show chronological list |
| Resource exhaustion | Timeout + fallback | LLM rate limit hit → return cached response |

### Phase 5 — Handle Errors Consistently

Enforce a system-wide error envelope (RFC 7807 Problem Details):

```json
{
  "error_code": "RESOURCE_NOT_FOUND",
  "message": "A human-readable explanation.",
  "details": {
    "field": "Specific validation failure reasons, if applicable."
  },
  "trace_id": "uuid-for-logging-correlation"
}
```

- [ ] All endpoints use the same error structure
- [ ] `error_code` is a machine-readable constant (e.g., `VALIDATION_ERROR`, `UNAUTHORIZED`)
- [ ] `message` is human-readable and actionable
- [ ] `trace_id` enables log correlation for debugging
- [ ] Internal errors (500) trigger immediate alerts
- [ ] **Never** expose stack traces, database errors, or internal paths to clients
- [ ] Validation errors include specific field-level details

### Phase 6 — Ensure Idempotency

Design operations to handle retries safely:

- [ ] `GET`, `PUT`, `PATCH`, `DELETE` are idempotent by definition (verify this in implementation)
- [ ] Critical `POST` mutations require an `Idempotency-Key` header
- [ ] Backend stores the key and returns cached successful response on replay within a defined timeframe
- [ ] Idempotency window is documented (e.g., 24 hours)
- [ ] Idempotent operations are documented in the API contract

### Phase 7 — Set Internal Automation Contracts

Internal tools and CI/CD follow the same rigor as public APIs:

- [ ] Scripts define strict input/output schemas (arguments, env vars, exit codes)
- [ ] Invalid inputs are handled predictably (not silently ignored)
- [ ] Structured logs are produced for failure analysis
- [ ] Boundary control is documented
- [ ] Internal schemas are versioned or at minimum documented

### Phase 8 — Design for Statelessness

Default to stateless system design:

- [ ] No session state stored between requests
- [ ] Any persistence is explicitly delegated to a defined storage layer
- [ ] State management is documented in the Data Model section of the RFD
- [ ] If stateful components are required (sessions, caches), their lifecycle is documented

---

## Glossary

| Term | Definition | Full Reference |
|------|-----------|---------------|
| [Idempotent](standards/GLOSSARY.md#idempotent) | Operation that produces the same result whether called once or multiple times. | Reliability pattern |
| [Circuit Breaker](standards/GLOSSARY.md#circuit-breaker) | Pattern that stops traffic to a failing service to allow recovery. | Resilience pattern |
| [Graceful Degradation](standards/GLOSSARY.md#graceful-degradation) | Fallback to functional reduced behavior when a dependency fails. | Resilience pattern |
| [Stateless](standards/GLOSSARY.md#stateless) | No session state stored between requests. Default API design principle. | Architecture default |
| [Dependency Boundary](standards/GLOSSARY.md#dependency-boundary) | Rules defining which modules may communicate and which imports are prohibited. | Architecture constraint |
| [SemVer](standards/GLOSSARY.md#semver-semantic-versioning) | MAJOR.MINOR.PATCH versioning scheme for breaking, feature, and fix changes. | Versioning standard |
| [RFD](standards/GLOSSARY.md#rfd-request-for-design) | Technical design document covering structure, data, interfaces, logic, and operations. | Design documentation |
| [C4 Model](standards/GLOSSARY.md#c4-model) | Hierarchical system visualization: Context → Containers → Components. | Architecture visualization |

---

## Chaining

**Upstream**:
- `standards/apply-principles` — Simplicity, automation, and documentation principles ground API design decisions

**Downstream**:
- `workflow/specify-solution` — API contracts are documented in RFD structure and interface sections
- `standards/shift-left-security` — API security (authentication, CSRF, injection prevention) follows security standards
- `standards/write-tests` — API contracts must be tested with integration and E2E tests
- [`polish-ui`](EXTERNAL.md#emil-kowalski-skills) (externa) — API response design influences UI state transitions and loading patterns
- `workflow/create-pr` — API changes require peer review and CI validation

**Cross-cutting**: API design decisions should be captured as ADRs when they involve technology choices (REST vs GraphQL, versioning strategy). See the `workflow/log-adr` skill.
