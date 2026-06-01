---
name: write-commits
description: "Conventional Commits format, atomic commit discipline, and message quality. Use when committing code, writing commit messages, reviewing commit history, or preparing a branch for PR."
license: MIT
trigger: "commit, commit message, conventional commits, atomic commits, git commit, commit history, commit format, write-commits"
metadata:
  author: Joel Abreu Rojas
  version: "1.0.0"
  ssot_chapter: "16: Commit Standards"
  porter_note: "Conventional Commits is a universal standard — works with any language, framework, or toolchain."
---

# Write Commits

## Philosophy

A commit is the smallest atomic unit of work in a project. It is the DNA of the codebase: every `git blame`, every `git bisect`, every code review traces back to individual commits. A clean commit history makes debugging possible, feature evolution understandable, and rollbacks safe.

The fundamental rule: **each commit must represent one logical change**. If a commit contains changes that require two different descriptions, it is too big and must be split. This is not pedantry — it is the difference between a codebase you can navigate and a codebase you can only rewrite.

Atomic commits serve three masters:
1. **Debugging**: `git bisect` finds the exact commit that introduced a bug. If a commit bundles three unrelated changes, bisect cannot isolate which change caused the regression.
2. **Code review**: Reviewers can focus on one logical change at a time. A commit that mixes a refactor with a bug fix forces the reviewer to mentally separate the two, increasing cognitive load and reducing review quality.
3. **Rollback**: If a commit introduces a regression, reverting it is safe when it does one thing. Reverting a commit that bundles a fix with a refactor re-introduces the original bug.

The commit message format — Conventional Commits — is a machine-readable standard. It enables automated changelogs, semantic version bumps, and tooling that understands the *nature* of each change. The format is simple: `type(scope): subject`. The type tells you *what kind* of change this is. The scope tells you *where* it happened. The subject tells you *what* it does.

**The body explains why, not how.** The code shows how. The commit message explains why this change was necessary, what problem it solves, and what alternatives were considered. A body that describes the diff is redundant — the reviewer can see the diff.

**Commit messages are documentation.** They are the first thing a developer reads when investigating a bug with `git blame`, understanding a feature's evolution with `git log`, or bisecting a regression with `git bisect`. A well-written commit message saves future developers minutes of investigation. A poor one forces them to read the diff, the PR, and the issue to reconstruct the context.

---

## Anti-Patterns

### 1. Commits Requiring Two Descriptions

A commit whose message cannot be captured in a single sentence. "Fix null pointer and add input validation" is two changes — two logical units, two potential rollback points, two independent review concerns.

**Fix**: Split into two commits. Each commit gets its own message, its own review, and its own rollback boundary. The goal: if you revert the first commit, the second commit still makes sense on its own.

### 2. Title Exceeding 50 Characters

Commit titles that run long force reviewers to scroll horizontally in `git log`, break the visual alignment of the history, and get truncated in GitHub UIs. The 50-character limit forces concise, focused messages.

**Fix**: Use imperative mood, drop unnecessary words, abbreviate where clear. "add user authentication service" (31 chars) beats "implement the user authentication service module for the backend API" (66 chars). If you cannot describe the change in 50 characters, the change is probably too big.

### 3. Body Explaining "How" Instead of "Why"

Writing a commit body that describes the code changes: "This commit adds a new function to validate email addresses using regex." The reviewer can see that from the diff. The body adds zero value.

**Fix**: Explain the *reason*: "Email validation was scattered across three modules with inconsistent rules. Consolidating into a single function ensures consistent behavior and makes future rule changes atomic." The body answers: why was this change needed?

### 4. Mixing Unrelated Changes in One Commit

Combining a dependency update with a feature, or a config change with a bug fix, in a single commit. This makes `git bisect` useless for the affected range, creates rollback risk, and confuses reviewers.

**Fix**: Each commit does exactly one thing. If you updated a dependency AND fixed a bug, those are two commits. The dependency update is `chore(deps): update express to v4.18`. The bug fix is `fix(auth): prevent token expiry race condition`.

### 5. Forgetting the Scope

Writing `fix: prevent token expiry race condition` without a scope. The subject tells you *what* was fixed, but not *where*. In a large codebase, this forces the reviewer to search the diff to find which module was affected.

**Fix**: Always include a scope that identifies the logical area: `fix(auth): prevent token expiry race condition`. The scope is a free-form topic (e.g., `sidebar`, `api-auth`, `db-init`), not a file path.

---

## Workflow

### Phase 1 — Plan the Commit Sequence

Before writing code, think about the commit structure:

- [ ] Identify the logical changes your work involves
- [ ] Plan one commit per logical change
- [ ] Ensure each commit can be reverted independently without breaking the build
- [ ] Map commits to issue To-Do items for traceability

**Heuristic**: If your commit message needs "and," split it.

### Phase 2 — Write the Title

The first line of the commit message follows the Conventional Commits format:

```
type(scope): subject
```

- [ ] **Type**: `feat` (new feature), `fix` (bug fix), `chore` (maintenance), `docs` (documentation), `refactor` (code restructuring), `test` (adding tests), `ci` (CI/CD changes), `research` (investigation)
- [ ] **Scope**: Free-form topic identifying the logical area (e.g., `sidebar`, `api-auth`, `db-init`). Not a file path.
- [ ] **Subject**: Lowercase, imperative-mood summary ("add," "fix," "refactor," not "added," "fixed," "refactored")
- [ ] **Breaking change**: Add `!` after the type (e.g., `feat!(ui):`) to signal an incompatible change
- [ ] **Length**: The entire title line **must not exceed 50 characters**

**Examples**:
| Title | Assessment |
|-------|-----------|
| `fix(ui): correct alignment of sidebar icons` | Good — 48 chars, clear type/scope/subject |
| `feat(auth): add JWT refresh token rotation` | Good — 44 chars, specific scope |
| `chore: update dependencies` | Acceptable — no scope needed for global changes |
| `feat(ui): implement the collapsible sidebar component for the dashboard` | Bad — 71 chars, too verbose |

### Phase 3 — Write the Body (If Needed)

For non-trivial changes, add a body separated from the title by a blank line:

- [ ] Explain **why** the change was made, not **how** the code works
- [ ] Describe the problem this commit solves
- [ ] Mention alternatives considered, if relevant
- [ ] Wrap lines at **72 characters**
- [ ] Reference related issues: `Resolves #12` or `Closes #45`

**When to skip the body**: Simple changes where the title is self-explanatory. `fix(auth): prevent token expiry race condition` does not need a body if the fix is obvious from the diff.

### Phase 4 — Write the Footer (If Needed)

For breaking changes or task linking:

- [ ] Breaking changes: `BREAKING CHANGE: <description of what broke and how to migrate>`
- [ ] Task linking: `Resolves #123` or `Closes #456`
- [ ] Multiple footers separated by blank lines

### Phase 5 — Verify Before Committing

Review the commit message before pushing:

- [ ] Title is ≤ 50 characters
- [ ] Type is correct (feat, fix, chore, etc.)
- [ ] Scope is present and meaningful
- [ ] Subject is imperative mood, lowercase
- [ ] Body explains *why*, not *how* (if present)
- [ ] Body lines are ≤ 72 characters (if present)
- [ ] The commit does exactly one thing (atomic)
- [ ] The commit message matches the actual changes (no stale messages)
- [ ] Tests or docs for this unit are included when relevant
- [ ] Rollback is reasonable without reverting unrelated work

### Phase 6 — Final Commit on `main`

Because we use squash merge, the final commit on `main` includes the PR ID:

- [ ] Format: `type(scope): subject (#PR-ID)`
- [ ] Example: `feat(ui): implement collapsible sidebar (#136)`
- [ ] The PR ID provides traceability back to the full context in the pull request

### Phase 7 — Review Commit History

Before pushing, review the full commit history on the branch:

- [ ] Each commit does exactly one thing (atomic)
- [ ] No commit requires "and" in its description
- [ ] Commit messages match the actual changes (no stale messages from refactoring)
- [ ] The sequence of commits tells a logical story (if read in order)
- [ ] No debug statements, TODO comments, or work-in-progress commits remain

**Cleanup rule**: If you need to fix a previous commit on the branch, use `git commit --amend` for the most recent commit, or interactive rebase for older commits. Never leave "fix typo" or "WIP" commits in the history.

### Phase 8 — Validate Work-Unit Integrity

Ensure each commit functions as an independent work unit suitable for chained PRs if the change grows large:

- [ ] The commit has one clear purpose (work unit, not file type)
- [ ] The repo still makes sense after applying only this commit
- [ ] Tests or docs for this unit are included when relevant (keep tests with code)
- [ ] Rollback is reasonable without reverting unrelated work
- [ ] The commit message explains the outcome, not the file list

**Work unit vs file type split**:
| Weak split (by file type) | Better work-unit split |
|---------------------------|----------------------|
| `add models` | `feat(auth): add token validation domain model and tests` |
| `add services` | `feat(auth): wire token validation into login flow` |
| `add tests` | Tests included with each behavior commit |
| `update docs` | Docs included with the user-facing change they explain |

---

## Commit Type Reference

Quick reference for selecting the correct type:

| Type | When to Use | Example |
|------|------------|---------|
| `feat` | New user-facing functionality | `feat(auth): add JWT refresh token rotation` |
| `fix` | Bug fix that corrects existing behavior | `fix(ui): correct alignment of sidebar icons` |
| `chore` | Maintenance, dependencies, tooling | `chore(deps): update express to v4.18` |
| `docs` | Documentation changes only | `docs(readme): add installation instructions` |
| `refactor` | Code restructuring without behavior change | `refactor(api): decouple logic from repository` |
| `test` | Adding or updating tests | `test(auth): add unit tests for token validation` |
| `ci` | CI/CD configuration changes | `ci(github): add CodeQL scanning workflow` |
| `research` | Investigation or spike work | `research(cache): evaluate Redis vs Memcached` |

**Breaking changes**: Append `!` after the type: `feat!(ui): remove deprecated widget API`

---

## SDD Workload Integration

When working within the SDD (Specification-Driven Development) workflow, commits are the building blocks of deliverable work units that map directly to SDD tasks and PR slices.

### Review Workload Forecast

When `workflow/plan-issues` or `sdd-tasks` produces a Review Workload Forecast:

| Risk Level | Response |
|------------|----------|
| **Low** (≤200 lines) | Keep work-unit commits inside one PR. Follow the standard commit workflow. |
| **Medium** (200-400 lines) | Commit by work unit and monitor changed lines before PR creation. Be prepared to split into chained PRs. |
| **High** (>400 lines or complex) | Follow the SDD `delivery_strategy`: ask on `ask-on-risk`, auto-slice on `auto-chain`, require `size:exception` on over-budget `single-pr`, or record accepted `size:exception` on `exception-ok`. |

### Work-Unit Mapping

Each SDD work unit should map cleanly to a commit or PR with:
- Clear start state (what the codebase looks like before)
- Clear finished state (what the codebase looks like after)
- Verification in the same unit (tests + docs with code)
- Rollback that does not remove unrelated work

### Commands

```bash
# Review the story before committing
git diff --stat
git diff --cached --stat

# Check recent commit style
git log --oneline -5
```

---

## Glossary

| Term | Definition | Full Reference |
|------|-----------|---------------|
| [Conventional Commits](standards/GLOSSARY.md#conventional-commits) | Commit format: `type(scope): subject`. Types: feat, fix, chore, docs, refactor, test, ci. | Standard format |
| [Atomic](standards/GLOSSARY.md#atomic) | One commit = one logical change. No unrelated changes bundled. | Core principle |
| [Squash Merge](standards/GLOSSARY.md#squash-merge) | Compresses branch commits into a single commit on `main` with PR context. | History strategy |
| [Scope](standards/GLOSSARY.md#scope) | Free-form topic in commit message identifying the logical area changed (e.g., `sidebar`, `auth`). | Message anatomy |
| [Breaking Change](standards/GLOSSARY.md#breaking-change) | Incompatible API or architectural change, signaled by `!` after the commit type. | SemVer signal |
| [Semantic Versioning](standards/GLOSSARY.md#semantic-versioning-semver) | `MAJOR.MINOR.PATCH` scheme. Commits drive version bumps automatically. | Version strategy |

---

## Chaining

**Upstream**:
- `workflow/enforce-branch-flow` — Commits live on a branch; the branch naming convention provides the context for commit scope

**Downstream**:
- `workflow/create-pr` — A PR aggregates commits from a branch; commit messages provide context for the PR summary
- `workflow/ship-release` — Commit types drive changelog entries and semantic version bumps

**Cross-cutting**: Every commit on a branch must follow this standard. When squash-merged, the PR title becomes the final commit message on `main` — ensure it follows the same format.

**Handoff**: After Phase 7, push the commits and hand off to `workflow/create-pr` for PR creation, self-review, and merge preparation.
