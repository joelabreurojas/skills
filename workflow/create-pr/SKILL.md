---
name: create-pr
description: "Pull request structure, self-review discipline, test instructions, and squash-merge workflow. Use when opening a PR, reviewing a PR, preparing code for merge, or validating PR completeness."
license: MIT
trigger: "pull request, PR, code review, merge request, open PR, review PR, squash merge, self-review, PR template, create-pr"
metadata:
  author: Joel Abreu Rojas
  version: "1.0.0"
  ssot_chapter: "18: Pull Requests"
  porter_note: "PR structure is GitHub-native; adapt to GitLab Merge Requests, Bitbucket Pull Requests, or equivalent."
---

# Create PR

## Philosophy

A Pull Request is more than a request to merge code. It is a **story about a solution** told to your teammates. Its purpose is to provide reviewers with all the context they need to understand the change, verify its quality, and maintain the project's long-term health.

The PR is the final quality gate before code reaches `main`. It is where automated checks (CI), human review (peer approval), and documentation (test instructions, screenshots) converge. A PR that skips any of these is not ready for review — it is a draft pretending to be final.

**Every PR MUST link an approved issue.** The issue captures the *why*. The PR captures the *what* and *how*. Without an issue link, there is no traceability between the code change and the planned work. Reviewers cannot verify the PR solves an approved problem, and future developers cannot understand why the change was made.

**Self-review is mandatory before peer review.** The author must read through their own "Files Changed" before requesting review. This catches typos, debug logs, commented-out code, and logic errors that the author can fix in seconds but the reviewer would spend minutes documenting. Self-review is not optional politeness — it is the first line of quality defense.

**A PR without test instructions is not ready for review.** "How to Test" is not a courtesy section. It is the mechanism by which reviewers verify the change works as described. Without it, the reviewer must guess how to test, which means they will either skip testing (approving untested code) or waste time figuring it out (slow reviews). Both outcomes are failures.

**Squash and Merge** is the only merge strategy. This compresses all incremental commits on a feature branch into a single, clean commit on `main`. The final commit includes the PR ID for traceability. The branch is then deleted automatically. This keeps `main`'s history linear, clean, and bisectable.

---

## Anti-Patterns

### 1. PR Without Test Instructions

Opening a PR without a "How to Test / Reproduce" section. The reviewer has no way to verify the change works. They either approve blindly (dangerous) or ask "how do I test this?" (wasteful). Both outcomes slow the team down.

**Fix**: Every PR includes step-by-step instructions to verify the change. Include the exact commands, the expected output, and the scenarios to test. If the change is visual, include screenshots or a GIF.

### 2. Skipping Self-Review

Requesting peer review without reading through your own "Files Changed" tab first. The author catches their own typos, debug logs, and commented-out code in seconds. The reviewer spends minutes documenting them. Multiply by every PR, every week — self-review is the highest-leverage quality activity.

**Fix**: Before clicking "Request Review," read every file in "Files Changed." Check for: debug statements, commented-out code, unused imports, inconsistent formatting, logic errors, and missing documentation updates.

### 3. No Screenshots for UI Changes

Opening a PR that changes the UI without including a screenshot or GIF. Reviewers cannot assess visual changes from code alone. They must check out the branch, run the app, and navigate to the changed screen — a process that takes minutes instead of seconds.

**Fix**: For any PR that affects the UI, include a before/after screenshot or a short GIF showing the change. This is not optional for UI-facing changes.

### 4. PR Description Missing Context

Writing a PR description that says "Fixed bug" or "Added feature" without explaining *why*. Reviewers need context: what problem does this solve? What was the root cause? What alternatives were considered? Without context, the reviewer evaluates code without understanding its purpose.

**Fix**: Follow the mandatory PR structure: Summary, Associated Issue, Problem Addressed, Technical Details, How to Test, Visual Aids, and Checklist. Every section exists for a reason.

### 5. Approving Without Reading the Diff

Rubber-stamping a PR without reviewing the actual code changes. "Looks good to me" without reading the diff is not approval — it is negligence. It lets bugs, security vulnerabilities, and architectural violations reach `main`.

**Fix**: Reviewers must read the full diff. Focus on logic, security, architecture, and edge cases. Formatting and style should be enforced by CI, not human comments — but logic and design require human judgment.

### 6. PR Without a Linked Issue

Opening a PR that does not reference an approved issue. Without an issue link, reviewers cannot verify the change was planned, prioritized, and approved. The PR exists in a vacuum — there is no traceability, no context, no accountability.

**Fix**: Every PR must include `Resolves #N`, `Closes #N`, or `Fixes #N` linking to an approved issue. The issue-first discipline ensures that code changes always trace back to planned work.

---

## Workflow

### Phase 1 — Prepare the PR

Before opening the PR, ensure the branch is ready:

- [ ] All commits follow the atomic commit standard (see `workflow/write-commits` skill)
- [ ] CI pipeline is green — all status checks pass
- [ ] Branch is up to date with `main` (rebase if behind)
- [ ] No merge conflicts exist
- [ ] All related documentation is updated (PRD, RFD, ADR, CHANGELOG)
- [ ] The branch solves exactly one issue (see `workflow/enforce-branch-flow` skill)
- [ ] The issue exists and is approved — every PR must link to an approved issue
- [ ] The PR will have exactly one type label matching the change category

### Phase 2 — Write the PR Description

Follow the mandatory structure:

```markdown
## Summary of Changes

[One-sentence, high-level summary of the technical change]

## Associated Issue

Resolves #[issue-id]

## Problem Addressed / Purpose

[Explain the "Why." What real-world problem does this solve? What is the goal?]

## Technical Details & Implementation

### Changes Made
[Key architectural shifts or new components]

### Design Decisions
[Why this specific approach was chosen over alternatives]

### Trade-offs
[Any known limitations or future improvements]

## How to Test / Reproduce

1. [Step-by-step instruction 1]
2. [Step-by-step instruction 2]
3. [Expected result]

## Visual Aids

[Screenshot or GIF for UI-facing changes. Required for any visual change.]

## Checklist

- [ ] Linked to an approved issue (`Resolves #N`)
- [ ] Code follows project conventions
- [ ] Tests added/updated for the change
- [ ] Documentation updated (PRD, RFD, ADR, CHANGELOG)
- [ ] CI pipeline is green
- [ ] Self-review completed
- [ ] Exactly one `type:*` label applied
```

- [ ] Summary is one sentence, not a paragraph
- [ ] Associated Issue links to an approved issue in the tracker
- [ ] Problem section explains *why*, not *what*
- [ ] How to Test has clear, reproducible steps
- [ ] Visual Aids included for UI changes

### Phase 3 — Self-Review

Before requesting peer review, perform a critical self-audit:

- [ ] Read through the **Files Changed** tab — catch typos, debug logs, commented-out code
- [ ] Confirm the **CI pipeline** is completely green
- [ ] Ensure all documentation (PRD, RFD, ADR) affected by this change is updated and included
- [ ] Verify the commit messages follow the conventional commit standard
- [ ] Check that no unrelated changes leaked into the branch
- [ ] Review the diff from the reviewer's perspective — would they understand the change without asking questions?
- [ ] Verify the linked issue has an approved status

**Self-review is the first line of quality defense.** If you find issues, fix them before requesting review. Every issue the reviewer catches is a cycle wasted.

### Phase 4 — Request Peer Review

After self-review passes:

- [ ] Request review from at least 1 Maintainer
- [ ] Reviewers focus on: logic, security, architecture, edge cases
- [ ] Formatting and style should be enforced by CI, not human comments
- [ ] If changes are requested, push fixes to the same branch — do not close and reopen

### Phase 5 — Handle Review Feedback

When the reviewer requests changes:

- [ ] Address each comment — fix the code or explain why the current approach is correct
- [ ] Push fixes to the same branch (the PR updates automatically)
- [ ] Re-request review after addressing all comments
- [ ] Do not merge until all conversations are resolved and approval is granted

### Phase 6 — Squash and Merge

Once approved and CI passes:

- [ ] Use **Squash and Merge** — the only allowed merge strategy
- [ ] The final commit title includes the PR ID: `type(scope): subject (#PR-ID)`
- [ ] The branch is automatically deleted after merge
- [ ] The Issue is automatically closed (if linked via `Resolves #`)

**Stacked PRs**: For tasks with hard dependencies on unfinished work:
- [ ] Branch off the prerequisite task's branch
- [ ] Use the stacked PR template
- [ ] Clearly indicate which PR must be merged first
- [ ] This allows reviewers to see only the relevant diff for your specific task

---

## PR Automation Reference

### Automated CI Checks

Every PR must pass all automated checks before merge:

| Check | What It Verifies |
|-------|-----------------|
| Issue Reference | PR body contains `Closes #N`, `Fixes #N`, or `Resolves #N` |
| Issue Status | Linked issue has an approved status label |
| Type Label | PR has exactly one `type:*` label matching the change category |
| Linting & Formatting | Code style is enforced automatically |
| Automated Tests | Unit and integration test suites pass |
| Build Verification | Application compiles successfully |

### Conventional Commits to PR Label Mapping

The commit type determines the PR label:

| Commit Type | PR Label |
|-------------|----------|
| `feat` | `type:feature` |
| `fix` | `type:bug` |
| `docs` | `type:docs` |
| `refactor` | `type:refactor` |
| `chore` | `type:chore` |
| `style` | `type:chore` |
| `perf` | `type:feature` |
| `test` | `type:chore` |
| `build` | `type:chore` |
| `ci` | `type:chore` |
| `revert` | `type:bug` |
| `feat!` / `fix!` | `type:breaking-change` |

### PR Body Quick Reference

Valid issue linking keywords:
- `Closes #N` — auto-closes the issue when merged
- `Fixes #N` — auto-closes the issue when merged
- `Resolves #N` — auto-closes the issue when merged

---

## Glossary

| Term | Definition | Full Reference |
|------|-----------|---------------|
| [Squash Merge](standards/GLOSSARY.md#squash-merge) | Compresses all branch commits into a single commit on `main` with PR context. | Clean history strategy |
| [Self-Review](standards/GLOSSARY.md#self-review) | Author reads their own Files Changed before requesting peer review. First line of quality defense. | Review discipline |
| [Status Checks](standards/GLOSSARY.md#status-checks) | Automated CI validation (lint, test, build) that must pass before merge. | Quality gate |
| [Conversation Resolution](standards/GLOSSARY.md#conversation-resolution) | All review comments must be resolved before merge. Ensures no feedback is ignored. | Review completion |
| [Stacked PR](standards/GLOSSARY.md#stacked-pr) | PR branched off another unfinished PR for hard-dependent tasks. Shows only relevant diff. | Dependency management |
| [Atomic](standards/GLOSSARY.md#atomic) | One PR = one issue = one logical change. No unrelated changes bundled. | Core principle |
| [Pipeline](standards/GLOSSARY.md#pipeline) | Automated CI/CD system that validates code. Must be green before merge. | Automation standard |

---

## Chaining

**Upstream**:
- `workflow/write-commits` — PRs aggregate commits from a branch; commit messages provide context for the PR summary
- `workflow/log-adr` — ADRs are created and reviewed within PRs; the Status Flip happens in the PR workflow
- `workflow/enforce-branch-flow` — PRs are the merge mechanism for branches; the branch is deleted after merge

**Downstream**:
- `workflow/ship-release` — Merged PRs drive changelog entries and semantic version bumps
- `tools/pack-context` — PR context provides handoff information for session boundaries

**Cross-cutting**: The PR is the convergence point where planning (issues), implementation (commits), decisions (ADRs), and quality (CI) meet. Every PR must be self-contained — a reviewer should understand the change without reading anything outside the PR.

**Handoff**: After Phase 6, the PR is merged. The squash commit on `main` becomes part of the release history. Hand off to `workflow/ship-release` for changelog updates and version bumps.
