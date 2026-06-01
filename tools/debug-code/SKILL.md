---
name: debug-code
description: "Disciplined bug-fixing loop: reproduce, minimize, hypothesize, instrument, fix, regression test. Use when investigating a bug, fixing a regression, or diagnosing a test failure."
license: MIT
trigger: "/debug-code, debug, debugging, bug fix, reproduce, regression, diagnose, root cause, instrument, flaky test, bug investigation"
metadata:
  author: Matt Pocock
  version: "1.0.0"
  derived_from: "mattpocock/skills — diagnose"
  ssot_chapter: "13: Diagnose & Debug"
  porter_note: "Blameless culture is the foundation — every bug is a system failure, not a personal failure."
---

# Debug Code

## Philosophy

### Every bug is a system failure

Blameless debugging means: a developer made an error because documentation was unclear, tooling was insufficient, or automated checks did not catch it. The bug is evidence of a gap in the system, not a gap in the person.

This is not semantic generosity. It is a practical position: blame creates silence. Silence hides information. Information is what you need to fix the root cause. If developers hide bugs out of fear, the system never improves.

Fix the bug. Then fix what allowed the bug to exist. Every bug is an opportunity to improve documentation, tooling, or automation. A bug that does not result in a systemic improvement is a wasted bug.

### The reproduction is the diagnosis

If you cannot reproduce the bug, you cannot fix it. Reproduction is not optional — it is the first and most important step. A bug that cannot be reproduced cannot be confirmed as fixed.

A failed reproduction means you do not understand the conditions yet. More data. More specific conditions. Narrower scope. The reproduction IS the investigation. When you can reproduce the bug with a three-line input and two configuration flags, you have already done 80% of the work — the remaining 20% is finding the code path that fails.

### One thing at a time

Changing multiple variables simultaneously makes it impossible to know what fixed the bug. Change one thing. Test. Observe. If it did not fix it, revert and try something else. This feels slow but is faster than the alternative — making three changes, seeing it pass, and never knowing which change actually fixed it (or what new bug was introduced).

Scientific method applies here: hypothesis → prediction → experiment → observation → conclusion. The hypothesis predicts what will happen when you make a specific change. If the prediction is wrong, the hypothesis is wrong. Form a new one and try again.

### Every fix needs a regression test

A fix without a regression test is not a fix — it is a temporary workaround. The next refactor, dependency update, or well-intentioned cleanup will reintroduce the same bug, and nobody will know until it hits production.

The regression test is the permanent record of the bug. It proves the fix works, documents the expected behavior, and prevents reintroduction. Writing the test is not optional — it is part of the fix.

---

## Anti-Patterns

### 1. Guessing Before Reproducing

Jumping to a fix based on reading the code without reproducing the issue. Code reading tells you what the code *should* do. Reproduction tells you what it *actually* does.

**Fix**: Always reproduce the bug first. If you cannot reproduce, instrument to gather more data until you can. Never fix a bug you have not seen with your own eyes.

### 2. Changing Multiple Things at Once

Applying three potential fixes and hoping one works. If the bug goes away, you do not know which change fixed it — and worse, you do not know which of the other two changes introduced a new bug.

**Fix**: Change one variable at a time. Test after each change. Revert failed attempts before trying the next approach.

### 3. Skipping Regression Tests

Fixing the bug but not adding a test that proves it is fixed and stays fixed. The next refactor or dependency update will reintroduce the same bug, and nobody will know until it hits production.

**Fix**: Write a failing test that reproduces the bug. Fix the code. Confirm the test passes. Now the bug is permanently prevented.

### 4. Ignoring the Root Cause

Fixing the symptom but not the underlying issue. A null check prevents the crash, but does not explain why the value was null in the first place.

**Fix**: After the immediate fix, ask "why did this happen?" and "what allowed this to happen?" Address the systemic gap — better validation, clearer documentation, stricter types, or more comprehensive tests.

---

## Workflow

### Phase 1 — Reproduce the Bug

Before touching any code, confirm you can trigger the bug on demand:

- [ ] Get a reliable reproduction case — the exact steps, inputs, and environment
- [ ] If the bug is intermittent, identify the conditions that increase frequency
- [ ] If you cannot reproduce, add logging or instrumentation to capture state when the bug occurs
- [ ] Capture the actual output vs expected output
- [ ] Check if the bug is environment-specific (OS, browser, timezone, data volume)

**Heuristic**: A bug that cannot be reproduced is not a bug — it is a symptom of insufficient instrumentation.

**Reproduction checklist**:
- Exact input that triggers the bug (save it as a test case)
- Exact environment: OS version, browser version, Node/Python/Go version, dependency versions
- Exact steps: what the user did, in what order, with what timing
- Frequency: does it happen every time? 50%? Only under load?
- Does the bug exist on a specific branch, commit, or only in production?
- Is there a difference between development and production environments? (minification, environment variables, data volume, database schema)

**If the bug is intermittent**:
- Add detailed logging around the suspected area and deploy to production
- Use feature flags to toggle the suspected code path for a subset of users
- Record request traces (OpenTelemetry, Datadog APM, Sentry) to capture state when the bug occurs
- Check if the bug correlates with specific conditions: time of day, data size, user locale, device type

### Phase 2 — Minimize the Scope

Narrow the search space using binary search or isolation:

- [ ] **Binary search**: Find the commit that introduced the bug by bisecting the history. `git bisect start` + `git bisect bad` + `git bisect good` automates this.
- [ ] **Isolate variables**: If the bug involves multiple components, isolate each one. Test the data layer in isolation. Test the UI in isolation. Eliminate variables one by one.
- [ ] **Simplify the input**: Reduce the reproduction case to the minimum input that triggers the bug. Strip away everything that is not necessary.
- [ ] **Check assumptions**: Is the database schema what you expect? Is the API returning the format you assume? Is the config file loaded correctly? Verify every assumption the code makes about its inputs.

### Phase 3 — Hypothesize and Instrument

Form a specific hypothesis and test it with targeted instrumentation:

- [ ] State the hypothesis: "The bug is caused by X because of Y"
- [ ] What would confirm or disprove this? Be specific
- [ ] Add logging, breakpoints, or assertions that target the hypothesis
- [ ] Run with the instrumentation and observe the result
- [ ] If the hypothesis is wrong, revert the instrumentation and form a new one

**Checklist for hypotheses**:
- The hypothesis is specific (not "something is wrong" but "the value is null because the API returned 500")
- The hypothesis is testable — you can confirm or disprove it with data
- The hypothesis accounts for all observed symptoms
- The hypothesis is falsifiable — if a specific experiment disproves it, you accept the result and move on

**Debugging techniques**:
- **Print debugging**: Add `console.log` / `fmt.Println` / `print()` at key points. Quick and effective for most bugs.
- **Debugger stepping**: Set breakpoints and step through the code. Best for understanding control flow and state changes.
- **Log file analysis**: Search logs for error patterns, timestamps, and correlation IDs.
- **Binary search**: Comment out half the code. If the bug persists, the problem is in the other half. Repeat.
- **Rubber ducking**: Explain the problem aloud to a colleague (or a rubber duck). The act of explaining often reveals the answer.
- **Diff review**: Compare the working version (last known good commit) with the broken version. Every changed line is suspect.
- **Stack trace analysis**: Read the full stack trace top to bottom. The top frame is where the error was thrown, but the bottom frame is where the mistake was made.

### Phase 4 — Fix the Bug

With the root cause confirmed, fix it:

- [ ] Write a failing test that reproduces the exact bug — this is your regression guard
- [ ] Apply the minimal fix that addresses the root cause
- [ ] Confirm the test passes
- [ ] Run the full test suite to ensure the fix does not break anything
- [ ] Clean up any temporary instrumentation added during debugging

**Blameless note**: The fix is not complete until you understand what system gap allowed this bug. Even if the fix is a one-line change, ask what documentation, type system, or validation should have prevented it.

### Phase 5 — Regression Test and Document

- [ ] The regression test is committed with the fix (tests and code in the same commit)
- [ ] Check for similar patterns in the codebase — if this bug existed here, it may exist elsewhere
- [ ] Document the root cause and fix in the commit message (explain *why*, not *how*)
- [ ] If the bug was caused by unclear documentation or missing validation, file a follow-up to fix the systemic gap

**Commit message format**:
```
fix(scope): short description of what was fixed

Root cause: One-sentence explanation of WHY the bug occurred.
Fix: What was changed to address it.
Systemic gap: What allowed this to happen — documentation, validation, or tooling gap.
Resolves: #issue-number
```

---

## Workflow Summary

The debugging loop at a glance:

```
Reproduce → Minimize → Hypothesize → Instrument → Fix → Regression Test
    ↑                                                         |
    └─────────────────── Did it work? ────────────────────────┘
                              ↓ No
                    Form new hypothesis
```

Each cycle narrows the search space. If the fix did not work, you eliminated one hypothesis and know more than before. The goal is not to be right — it is to eliminate wrong answers until only the right one remains.

---

## Glossary

| Term | Definition | Source |
|------|-----------|--------|
| Blameless | Mistakes are system failures, not personal failures. Focus on prevention, not blame. | Philosophy |
| `git bisect` | Git tool that finds the commit introducing a bug using binary search across commit history. | Tool |
| Regression test | A test written to prevent a previously fixed bug from reappearing. | Testing |
| Root cause | The underlying reason a bug occurred — not the symptom, but the condition that made it possible. | Debugging |
| Instrumentation | Code added temporarily to gather data (logging, assertions, metrics) during debugging. | Debugging |
| Rubber ducking | Explaining a problem aloud to an inanimate object; the act of verbalizing often reveals the solution. | Technique |
| Falsifiability | The capacity of a hypothesis to be proven wrong by experiment. Essential for scientific debugging. | Methodology |
| Systemic gap | The missing documentation, validation, or automation that allowed a bug to exist. The real fix target. | Debugging |
| Bug taxonomy | Categories of bugs: logic errors, state errors, concurrency errors, integration errors, environment errors. Each requires a different approach. | Debugging |

---

## Chaining

**Upstream**:
- `standards/write-tests` — Tests provide the safety net for confident debugging; TDD cycle helps reproduce bugs
- `standards/apply-principles` — Blameless culture and automation principles ground the debugging approach

**Downstream**:
- `workflow/write-commits` — Bug fixes follow the `fix` commit type with root cause documentation
- `workflow/create-pr` — Bug fix PRs need special attention to reproduction steps in the description
- `workflow/log-adr` — If the bug reveals a flawed architecture decision, capture it in an ADR

**Cross-cutting**: Debugging is universal — it applies to every skill in every bucket. Every fix must include a regression test (the TDD rule for bugs: reproduce with a failing test first). The debugging approach is the same whether the bug is in a Go backend, a React component, or a CI pipeline.
