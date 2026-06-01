---
name: write-tests
description: "Test pyramid (70/20/10), AAA structure, TDD for bug fixes, coverage strategy (>80% business logic), deterministic and isolated test design, flaky test elimination, and non-deterministic bug investigation. Use when writing tests, fixing bugs, setting up test infrastructure, reviewing test quality, configuring CI coverage, or debugging intermittent failures."
license: MIT
trigger: "/write-tests, write tests, fix bug, test infrastructure, test coverage, TDD, test review, flaky test, CI coverage, AAA, deterministic, isolated, non-deterministic bug, regression test"
metadata:
  author: Joel Abreu Rojas
  version: "1.0.0"
  ssot_chapter: "06: Testing Standards & Quality Assurance"
---

# Write Tests

## Philosophy

Testing is not an afterthought — it is a core component of the development process. We do not write code and *then* figure out how to test it. We write code *to be testable*. Testability is a design constraint, not an implementation detail.

Automated tests are the safety net that allows us to move fast, refactor with confidence, and maintain our Blameless Culture. Without tests, every change is a risk. With tests, every change is verified. A codebase without tests is a codebase that cannot be safely modified.

Coverage is a tool for discovery, not a vanity metric. We aim for >80% on core business logic. We do not mandate 100% — testing every getter/setter or UI pixel creates brittle tests that break on minor refactors without catching real bugs. Focus tests on the complex logic defined in the RFD.

Strict TDD (test before code) is encouraged for features and mandatory for bug fixes. When fixing a bug, reproduce it with a failing test first. The fix is complete when the test turns green. This prevents regressions and proves the fix works.

---

## Anti-Patterns

### 1. Testing Implementation, Not Behavior
Tests that break when you rename an internal function but behavior hasn't changed. Tests coupled to implementation details (mocking internal collaborators, testing private methods) are a maintenance burden, not a safety net. Test through public interfaces — what the system *does*, not how it does it.

**Warning sign**: You rename an internal function and tests fail, but the external behavior is identical. Those tests were testing implementation, not behavior.

### 2. Flaky Tests Left in the Suite
A test that sometimes passes and sometimes fails destroys trust in the entire CI pipeline. Flaky tests (random failures from timing, network, or shared state) must be fixed or deleted immediately. There is no acceptable number of flaky tests. Zero is the only acceptable count.

**Root causes**: Shared state between tests, timing dependencies, network calls, non-deterministic input, resource contention. Fix the root cause, not the symptom.

### 3. E2E Tests for Edge Cases
Using end-to-end tests to verify logic that can be covered by unit tests. E2E tests are slow, brittle, and expensive. Reserve them for critical paths only (registration, login, checkout). Every edge case tested via E2E is a maintenance liability.

**Rule**: If a unit test can verify the behavior, use a unit test. E2E is for integration verification, not logic verification.

### 4. 100% Coverage as a Mandate
Forcing coverage of every line creates tests that add no value. Testing trivial getters, setters, or layout pixels produces brittle tests that break on refactors without catching real bugs. Target >80% on business logic; accept lower coverage on plumbing.

**Metric**: Coverage should *discover* untested code, not *mandate* test creation for every line.

### 5. Skipping the Failing Test in Bug Fixes
Writing the fix without first reproducing the bug with a failing test. Without a regression test, you cannot verify the fix works, and the same bug will eventually return. The failing test *is* the proof that the bug existed and the fix works.

**Enforcement**: Bug fix PRs must include a failing test that reproduces the bug. No test, no merge.

### 6. Tests That Depend on Execution Order
Tests that pass when run alone but fail when run with other tests. This indicates shared state, global mutations, or resource contention. Tests must be independent and order-agnostic.

**Fix**: Each test sets up its own state and cleans up after itself. Use transaction rollbacks, database truncation fixtures, or in-memory databases.

### 7. Asserting on Internal Mocks
Mocking internal collaborators and then asserting on mock call counts or argument values. This tests that you called the mock correctly, not that the system behaves correctly. Mocks should simulate external boundaries, not internal implementation details.

**Better**: Mock external services (databases, APIs, file systems). Assert on the observable outcome of the behavior.

---

## Workflow

### Phase 1 — Apply the Test Pyramid

Determine the right test type for what you are verifying:

- [ ] **Unit Tests (70%)**: Fast, isolated, single-function verification. Mock all external dependencies (databases, APIs, file systems). Use for business logic, data transformations, and edge cases.
- [ ] **Integration Tests (20%)**: Verify wiring between components. Use real or test-instance databases, caches, and services. Use for API endpoints, database queries, and service interactions.
- [ ] **E2E Tests (10%)**: Simulate real user interactions on the fully deployed system. Focus on critical paths only. Do not use for edge-case logic.

For each new test, ask: "What is the cheapest test type that verifies this behavior with confidence?"

**Test type selection matrix**:
| What You're Testing | Recommended Type | Why |
|--------------------|-----------------|-----|
| Business logic function | Unit | Fast, isolated, cheap |
| API endpoint | Integration | Verifies request → response flow |
| Database query | Integration | Verifies ORM/SQL correctness |
| User registration flow | E2E | Verifies full stack |
| Edge case in data transform | Unit | Logic verification |
| Authentication flow | Integration + E2E | Both boundary and full flow |

### Phase 2 — Structure Every Test with AAA

Every test must follow the Arrange-Act-Assert pattern:

```python
def test_login_fails_with_invalid_password():
    # Arrange — set up state
    user = create_user(email="test@example.com", password="correct-password")
    
    # Act — execute the behavior
    result = login(email="test@example.com", password="wrong-password")
    
    # Assert — verify the outcome
    assert result.success is False
    assert result.error_code == "INVALID_CREDENTIALS"
```

- [ ] **Arrange**: Set up test data, mock dependencies, configure state
- [ ] **Act**: Execute the single behavior being tested
- [ ] **Assert**: Verify the expected outcome
- [ ] Each section is visually distinct (blank lines or comments)
- [ ] One behavior per test — if you need multiple asserts, you likely need multiple tests
- [ ] Test names describe the behavior: `test_login_fails_with_invalid_password_and_returns_401()`

### Phase 3 — Ensure Deterministic and Isolated Tests

Every test must pass consistently and independently:

- [ ] **Deterministic**: Same input → same output, every time. No timing dependencies, no random data, no network calls.
- [ ] **Isolated**: No dependency on execution order. No shared state between tests. Each test sets up its own data and cleans up after itself.
- [ ] **No Flaky Tests**: If a test fails randomly, fix it or delete it. Flaky tests destroy CI trust. Investigate the root cause (timing, shared state, network) and address it.
- [ ] **Clear Naming**: Test names describe the behavior being verified.
  - Bad: `test_login()`
  - Good: `test_login_fails_with_invalid_password_and_returns_401()`

**Flaky test triage**:
1. Run the test 100 times in isolation. If it never fails, it is not flaky — something in the test suite causes interference.
2. Run it 100 times with the full suite. If it fails intermittently, identify shared state or resource contention.
3. Fix the root cause. Do not add retries, sleeps, or "eventually consistent" assertions.

### Phase 4 — Write Bug Fixes with TDD

When fixing a `type:bug` issue, follow this sequence strictly:

- [ ] **Step 1**: Write a failing test that reproduces the bug
- [ ] **Step 2**: Verify the test fails (RED)
- [ ] **Step 3**: Write the minimal fix to make the test pass (GREEN)
- [ ] **Step 4**: Verify the test passes (GREEN)
- [ ] **Step 5**: Refactor if needed, keeping tests green
- [ ] **Step 6**: Verify no other tests broke (regression check)

The fix is not complete until the failing test turns green. If you cannot write a failing test for the bug, document why (e.g., the bug is in a system without test seams).

**Bug fix TDD checklist**:
- [ ] Failing test reproduces the exact bug scenario
- [ ] Test fails before the fix (RED confirmed)
- [ ] Fix is minimal — does not change unrelated code
- [ ] Test passes after the fix (GREEN confirmed)
- [ ] No other tests broke (full suite regression check)
- [ ] Commit message explains the root cause and the fix

### Phase 5 — Set Coverage Strategy

Define and enforce coverage targets:

- [ ] **Core Business Logic**: >80% coverage target (application/domain layer)
- [ ] **Trivial Code**: Accept lower coverage (getters, setters, UI layout)
- [ ] **CI Enforcement**: Pipeline fails if a PR significantly drops overall coverage
- [ ] **Focus Areas**: Complex logic from the RFD, critical paths, security-sensitive code
- [ ] **Not a Vanity Metric**: Use coverage to discover untested code, not to hit arbitrary numbers
- [ ] **Coverage Reports**: Review coverage diffs on PRs, not just absolute numbers

**Coverage guidance by layer**:
| Layer | Target | Focus |
|-------|--------|-------|
| Domain/Business Logic | >90% | Every decision path |
| API/Controller | >80% | Request → Response flow |
| Infrastructure | >60% | Integration points |
| UI/Views | >50% | Critical user flows |

### Phase 6 — Handle Non-Deterministic Bugs

When facing intermittent or timing-dependent bugs:

- [ ] **Increase Reproduction Rate**: Loop the trigger 100x, parallelize, add stress, narrow timing windows
- [ ] **Instrument**: Add targeted debug logs with unique prefixes (e.g., `[DEBUG-a4f2]`)
- [ ] **Hypothesize**: Generate 3-5 ranked hypotheses before testing any of them
- [ ] **Isolate**: Change one variable at a time. Never "log everything and grep"
- [ ] **Document**: If no correct test seam exists, that itself is the finding — flag for architecture improvement

**Non-deterministic bug investigation**:
1. Establish a reproduction rate (e.g., fails 30% of the time)
2. Narrow the timing window (add sleeps, locks, or barriers to force ordering)
3. Increase the rate until it is debuggable (target >50% reproduction)
4. Once reproducible, follow the standard bug fix TDD process

---

## Glossary

| Term | Definition | Full Reference |
|------|-----------|---------------|
| [Test Pyramid](standards/GLOSSARY.md#test-pyramid) | 70% unit / 20% integration / 10% E2E ratio for fast, right-level feedback. | Test distribution model |
| [AAA](standards/GLOSSARY.md#aaa-arrange-act-assert) | Arrange, Act, Assert — visual separation of setup, execution, and verification. | Test structure |
| [Deterministic](standards/GLOSSARY.md#deterministic) | Tests that pass or fail consistently. Flaky tests are unacceptable. | Test quality |
| [Isolated State](standards/GLOSSARY.md#isolated-state) | Tests independent of execution order and shared data. | Test independence |
| [TDD](standards/GLOSSARY.md#tdd-test-driven-development) | Write failing test before implementation. Mandatory for bug fixes. | Development methodology |
| [Vertical Slice](standards/GLOSSARY.md#vertical-slice) | One test → one implementation → repeat. Deliver complete behaviors incrementally. | Delivery strategy |
| [Blameless](standards/GLOSSARY.md#blameless) | Treat test failures as system improvements, not individual blame. | Incident culture |

---

## Chaining

**Upstream**:
- `standards/apply-principles` — Blameless culture depends on test safety nets; automation principle enforces CI testing
- `standards/design-api` — API contracts must be tested at integration and E2E levels
- `standards/shift-left-security` — Security tests (injection, auth, CSRF) are part of the test suite

**Downstream**:
- `workflow/write-commits` — Test code follows the same atomic commit standards as production code
- `workflow/create-pr` — Tests must pass in CI before merge; test instructions included in PR description
- `workflow/ship-release` — Test suite must be green before any release is cut

**Cross-cutting**: Testing applies to every phase of the lifecycle. Every skill that produces code produces tests. When in doubt, ask: "How will I verify this works?"
