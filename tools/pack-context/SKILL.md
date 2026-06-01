---
name: pack-context
description: "Compact session context for handoff — capture what was done, what remains, decisions made, and gotchas found. Use at session end, before context switch, when handing off to another developer, or when persistent memory is unavailable."
license: MIT
trigger: "/pack-context, pack context, handoff, session end, context switch, handover, session summary, session close, context handoff"
metadata:
  author: Matt Pocock
  version: "1.0.0"
  derived_from: "mattpocock/skills — handoff"
  ssot_chapter: "14: Context Communication"
  porter_note: "Packed context is the single most important artifact for maintaining momentum across sessions and team members."
---

# Pack Context

## Philosophy

### Context is the most expensive thing to rebuild

Every time a developer or agent picks up work-in-progress, the first cost is reconstructing context: what was being done, why, what was decided, what was discovered, what was left unfinished. This cost is invisible because it is mental, but it dominates the start of every session.

Packed context is insurance against this cost. A 5-minute investment in packing context saves 30 minutes of reconstruction later. For AI agents with episodic memory, it is the difference between continuity and starting from zero every session.

Think of it as a "save game" for development work. Without a save file, you start each session from the title screen. With a good save file, you resume exactly where you left off, with full memory of the map, the quest, and the inventory.

### What matters is what a future reader needs to act

Not everything from a session needs to be preserved. The filter: "If I read this in a week, can I continue working without asking anyone?" If the answer is no, more context is needed.

This means capturing:
- **Decisions**: What was chosen and why (the reasoning, not just the outcome)
- **Discoveries**: Gotchas, edge cases, unexpected behaviors (the most valuable section)
- **State**: What is done, what is not, what is blocked (the factual status)
- **Next steps**: What the next session should do first (actionable, not vague)
- **Relevant files**: Where the work lives (save the next person from searching)

### Structured context is findable context

Free-form notes are better than nothing, but structured context is searchable, parsable, and actionable. The same structure every time means every consumer — human or agent — knows where to find each type of information.

The structure serves both retrieval and comprehension. When you know that "Discoveries" is always the third section, you can jump directly to it. When an automated tool needs to extract "Next Steps," it knows exactly where to look because the structure never changes.

### Handoffs happen more often than you think

Every context switch is a handoff: end of day, before a meeting, switching to a higher-priority task, handing over to a teammate, closing a session. These transitions are invisible overhead — you do not notice them, but they accumulate.

Packing context is not just for "big handoffs" between team members. It is for every moment you walk away from a task. The cost of reconstructing context after a weekend is the same whether you hand off to another person or to your future self. Pack context for both.

---

## Anti-Patterns

### 1. Omitting the "Why"

Recording what was done without explaining why. "Added rate limiting to the API" without saying "because we hit 429 errors in production from a misconfigured client." The next reader knows WHAT changed but not whether it was important.

**Fix**: Every accomplished item includes its motivation. "Fixed N+1 query in user list (production query was timing out at 5-second threshold)."

### 2. Skipping the Gotchas

Recording only successes and decisions, omitting the struggles. "The approach was straightforward" — when actually it took three attempts to find a working solution. The next person who hits the same problem will repeat the same failures.

**Fix**: Gotchas are the most valuable part of context. If something surprised you, write it down. The next person will be surprised too.

### 3. Vague Next Steps

"Continue working on auth" as a next step. From where? With what approach? Blocked on what? A vague next step forces the next session to start with investigation, not action.

**Fix**: Next steps must be specific and actionable. "Complete the token refresh endpoint in `internal/auth/refresh.go`. The approach was decided: rotation-based refresh with a 7-day expiry. Blocked on PR review for the parent `feat(auth/jwt)` branch."

### 4. Failing to Update Persisted Artifacts

Packing context into a file but not saving it to the team's persistent memory (Engram, wiki, shared doc). The context is only useful if the next session can find it.

**Fix**: Save to both local (handoff file) and persistent (Engram, issue tracker, shared documentation). Use stable topic keys so updates evolve the same record.

---

## Workflow

### Phase 1 — Capture the Session Goal

Before summarizing, establish what this session was about:

- [ ] What was the objective for this session?
- [ ] Does the objective come from an issue, PRD, RFD, or task list? Link it.
- [ ] Was the objective completed? If not, how far did we get and why?

**Output**: One-sentence goal statement. "Implement JWT refresh token rotation from issue #42."

**Examples**:
- "Implement token refresh endpoint from RFD-004"
- "Debug production performance regression on /search endpoint"
- "Refactor auth middleware to support multiple providers"

### Phase 2 — Document Decisions and Discoveries

- [ ] **Decisions**: What was decided, what was the alternative, why this choice?
  - "Chose refresh-rotation over long-lived tokens because rotation limits exposure window for stolen tokens."
- [ ] **Discoveries**: Gotchas, edge cases, unexpected behaviors.
  - "Found that the auth library does NOT support refresh rotation natively — had to implement custom state tracking."
- [ ] **User preferences**: Constraints or preferences the user expressed about how work should proceed.
- [ ] **Patterns established**: Naming conventions, file structure choices, test patterns.

**Output**: A list of structured items under ## Discoveries and ## Instructions.

**Examples of good discoveries**:
- "Found that the auth library does NOT support refresh rotation natively — had to implement custom state tracking in a wrapper"
- "The `calculateFees` function throws on negative amounts, but the input validation runs AFTER the calculation call — needs reordering"
- "The test database seeding script takes 45 seconds because it rebuilds indexes — split into structure-only + data-only scripts for faster iteration"

**Examples of good decisions**:
- "Chose refresh-rotation over long-lived tokens because rotation limits exposure window for stolen tokens (accepted trade-off: more complex client logic)"
- "Used CSS transitions over keyframes for the toast component because toasts are added rapidly and keyframes restart from zero on interruption"
- "Deferred the caching layer to Phase 2 because we need production traffic data to choose the right eviction strategy"

### Phase 3 — Summarize Accomplishments

For each significant piece of work completed:

- [ ] What was accomplished? One line.
- [ ] Why was it done? The motivation.
- [ ] Where is it? File paths.

**Format**:
```
- ✅ Implemented token refresh with rotation (internal/auth/refresh.go)
- ✅ Added integration test for refresh-then-revoke sequence (internal/auth/refresh_test.go)
- ✅ Documented refresh token lifecycle in RFD-004
```

**Bad accomplishments** (too vague, no evidence):
- "Worked on auth" — What specifically? What was the outcome?
- "Fixed some bugs" — Which bugs? What was the root cause? Where is the fix?
- "Made progress" — Progress toward what? How much remains?

### Phase 4 — Define Next Steps

For work that remains:

- [ ] What is the next action? Specific and actionable.
- [ ] Who owns it? (self, another team member, external dependency)
- [ ] What is it blocked on? If blocked, state the blocker.
- [ ] Is there a decision pending? If so, from whom.

**Format**:
```
- Next: Wire refresh endpoint into router (internal/server/routes.go)
- Blocked on: PR review for internal/auth/jwt.go (branch: feat/auth/jwt)
- Decision needed: Should refresh tokens invalidate all sibling tokens on rotation?
```

### Phase 5 — Save and Persist

- [ ] Save the packed context to a handoff file (`.handoff.md` in project root or session directory)
- [ ] Persist to engram-mem or team knowledge base using the established topic key
- [ ] If this is a handoff between people, notify the recipient with a link to the context
- [ ] Update any related artifacts (status boards, issue comments, PR descriptions)

**Structure for persistent save**:

```
## Goal
[One sentence: what we were building/working on]

## Instructions
[User preferences or constraints — skip if none]

## Discoveries
- [Technical findings, gotchas, non-obvious learnings]

## Accomplished
- ✅ [Completed items with key details]

## Next Steps
- [What remains to be done]

## Relevant Files
- path/to/file — [what it does or what changed]
```

---

## Glossary

| Term | Definition | Source |
|------|-----------|--------|
| Context Switch | Moving focus between tasks or sessions; the mental cost of re-establishing where you were. | Productivity |
| Handoff | Transfer of work-in-progress context between developers or between sessions. | Communication |
| Episodic Memory | Memory that retains specific events and experiences; AI agents lack this without explicit context persistence. | AI Systems |
| Topic Key | A stable identifier for evolving observations (e.g., `architecture/auth-model`) that enables upsert rather than duplication. | Knowledge Management |

---

## Chaining

**Upstream**:
- `tools/spell-out` — Decisions made during plan review are important context for handoff
- `tools/debug-code` — Bug fixes and root cause discoveries must be captured in handoff context
- `tools/explain-code` — Architecture understanding derived during explanation feeds the context

**Downstream**:
- None — Pack-context is a terminal skill. It captures state after any other skill has been used.

**Cross-cutting**: Every session should end with context packing. This is especially critical for AI agent sessions where the next invocation starts with no memory of the previous session. The packed context is the bridge between sessions.
