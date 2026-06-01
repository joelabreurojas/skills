---
name: enforce-branch-flow
description: "Branch naming, protection rules, and one-issue-per-branch discipline. Use when creating a branch, starting work on an issue, setting up repository rulesets, or validating branch hygiene."
license: MIT
trigger: "create branch, start issue, branch naming, repository settings, rulesets, branch protection, squash merge, enforce-branch-flow"
metadata:
  author: Joel Abreu Rojas
  version: "1.0.0"
  ssot_chapter: "15: Branch Flow & Repository Settings"
  porter_note: "Rulesets are GitHub-native; adapt to GitLab Protected Branches, Bitbucket branch permissions, or equivalent."
---

# Enforce Branch Flow

## Philosophy

The branch is the workspace for a single unit of work. Its name encodes what kind of work it is, which issue it addresses, and what it does in plain language. A well-named branch tells a story: a developer seeing `feat/102-user-auth-service` knows instantly that this is a feature, it closes issue #102, and it implements user authentication.

The branch exists to protect `main`. Every change must pass through peer review and automated CI before integration. This is not bureaucracy — it is the insurance layer that prevents regressions, security vulnerabilities, and broken builds from reaching production. The pipeline is the gatekeeper; the branch is the gate.

**One Issue = One Branch** is the atomicity constraint. Bundling unrelated changes into a single branch creates "hostage PRs" — PRs that cannot be merged because one part is blocked, even though the other parts are ready. It also makes rollbacks dangerous: you cannot revert one change without reverting the other. If you discover a separate bug while working on a feature, create a new issue and a new branch for that fix.

**Repository settings are not optional.** Squash-only merging, required status checks, and branch protection rulesets are not "nice to have." They are the automated enforcement that makes the human process work. Without them, the branching convention is aspirational. With them, it is mandatory.

The branch naming convention encodes metadata that the git log alone cannot carry. The type tells you the *nature* of the change. The issue ID links to the *why*. The description gives you the *what* in plain language. Together, they create a searchable, sortable history where every branch tells a complete story without requiring a detour to the issue tracker.

**Adaptability**: These rules apply to any version control workflow that uses feature branches. The naming convention, atomicity constraint, and protection rules are universal. The specific tool (GitHub Rulesets, GitLab Protected Branches, Bitbucket permissions) changes; the discipline does not.

---

## Anti-Patterns

### 1. Direct Commits to `main`

Pushing code directly to `main` without a pull request. This bypasses peer review, skips CI validation, and introduces untested code into the production-ready branch. Even "trivial" changes have caused outages.

**Fix**: Enforce branch protection rulesets on `main`: require PR, require status checks, require conversation resolution. No bypasses, no exceptions for maintainers.

### 2. Mixing Unrelated Changes in One Branch

Combining a bug fix with a feature, or a refactor with a config change, in a single branch. The resulting PR is impossible to review thoroughly, dangerous to rollback, and creates ambiguous blame when something breaks.

**Fix**: Apply the atomicity test — can you write a one-sentence PR description that covers everything? If you need "and," split it. One issue = one branch = one PR.

### 3. Skipping the Issue ID in Branch Names

Creating branches like `fix-sidebar-bug` without referencing an issue number. This breaks traceability: there is no link between the code change and the planned work. When reviewing the git log months later, nobody knows *why* this change was made.

**Fix**: Every branch name starts with `[type]/[issue-id]-[description]`. If there is no issue, create one first. The issue captures the *why*; the branch captures the *what*.

### 4. Leaving Stale Branches Unmerged

Keeping branches open long after the associated PR is closed or the issue is resolved. Stale branches confuse contributors, clutter the repository, and may contain outdated code that looks current.

**Fix**: Enable automatic head branch deletion after merge. For abandoned branches, close the PR and delete the branch explicitly. Repository hygiene is part of the Definition of Done.

### 5. Using Merge Commits Instead of Squash Merge

Allowing merge commits, rebase merges, or default merge strategies. This creates a noisy git history where individual commits from a feature branch are interleaved with merge commits, making `git log` and `git bisect` harder to use.

**Fix**: Configure the repository to allow squash merging only. The squash operation compresses an entire feature branch into a single, clean commit on `main` with full context in the message.

### 6. Branching Without an Issue

Creating a branch to "just fix something quickly" without creating an issue first. This breaks the traceability chain: the branch exists, the code changes, but nobody knows *why*. Six months later, `git blame` points to a commit that says "fix bug" with no context about what bug, what caused it, or what the fix actually does.

**Fix**: Always create an issue before creating the branch. The issue captures the *why* (context, root cause, acceptance criteria). The branch captures the *what* (the code changes). If the issue is trivial, a one-line issue is still better than no issue.

### 7. Long-Lived Feature Branches

Keeping a branch open for weeks, accumulating dozens of commits and drifting far from `main`. The longer a branch lives, the harder it is to merge, the more conflicts accumulate, and the riskier the final integration becomes.

**Fix**: Keep branches short-lived — ideally 1-3 days of work. If a feature is large, decompose it into smaller issues that each produce a short-lived branch. Use stacked branches (branching off a feature branch) for hard dependencies, but keep each branch focused and mergeable.

**Rule of thumb**: If your branch has more than 20 commits, it is probably too large. Split the work into smaller, independently mergeable units.

---

## Workflow

### Phase 1 — Configure Repository Settings

Before any branch is created, ensure the repository enforces the branching policy automatically:

- [ ] **Allow squash merging ONLY** — disable merge commits and rebase merging
- [ ] **Default squash commit message** — set to "Pull request title and description" for automatic context
- [ ] **Automatically delete head branches** — keep the repository clean after merge
- [ ] **Require a pull request before merging** — bypass is prohibited
- [ ] **Require approvals** — at least 1 approval from a Maintainer (solo projects may relax this, but status checks remain mandatory)
- [ ] **Require status checks to pass** — CI pipeline (linters, tests, security scans) must succeed
- [ ] **Require conversation resolution** — all review comments must be resolved

**Solo project note**: The "Require approvals" setting may be disabled for solo projects, but "Require status checks to pass" remains mandatory. The Pull Request is still the only entry point to `main`.

### Phase 2 — Create the Branch

When starting work on an issue, create the branch using the naming convention:

- [ ] **Format**: `[type]/[issue-id]-[short-description]`
- [ ] **Type**: Use the types from the labeling system (`feat`, `fix`, `chore`, `research`, `refactor`, `hotfix`)
- [ ] **Issue ID**: The numeric ID from the issue tracker (e.g., `102`)
- [ ] **Description**: 2-3 words summarizing the task, separated by hyphens

**Examples**:
| Branch Name | Meaning |
|------------|---------|
| `feat/102-user-auth-service` | Feature: implement user authentication (#102) |
| `fix/105-null-pointer-on-login` | Bug fix: null pointer exception on login (#105) |
| `chore/110-update-dependencies` | Maintenance: update dependencies (#110) |
| `research/115-evaluate-redis` | Spike: evaluate Redis as caching layer (#115) |
| `hotfix/120-payment-timeout` | Critical fix: payment gateway timeout (#120) |

### Phase 3 — Follow One-Issue-Per-Branch

Maintain strict atomicity throughout the work:

- [ ] The branch solves exactly **one** planned issue
- [ ] No unrelated changes are bundled into the branch
- [ ] If a separate bug is discovered, create a new issue and a new branch for it
- [ ] All commits on the branch follow the atomic commit standard (see `workflow/write-commits` skill)
- [ ] The branch has a clear, testable scope defined by the issue's acceptance criteria

### Phase 4 — Handle Special Branch Types

For non-standard work, follow the special branch conventions:

**Spike/Research (`research/`)**:
- [ ] Used for investigations or testing new technologies
- [ ] To mark as Done, produce a **Knowledge Asset**: an ADR, a prototype PR, or a final report in the issue comments
- [ ] Research branches follow the same PR and CI requirements

**Hotfix (`hotfix/`)**:
- [ ] Used for critical production bugs that must be fixed immediately
- [ ] May jump to the front of the queue, ignoring the current Roadmap
- [ ] Still requires a PR, CI checks, and peer review — speed does not bypass quality

**Refactor (`refactor/`)**:
- [ ] Used for cleaning up code without adding features or changing behavior
- [ ] Must have test coverage before merging
- [ ] Refactor branches must not change external behavior — if behavior changes, it is a feature or bug fix

### Phase 5 — Keep the Branch Clean

Throughout the lifecycle of the branch, maintain discipline:

- [ ] Do not commit directly to `main` from this branch — all commits stay on the feature branch
- [ ] Rebase on `main` regularly to avoid large merge conflicts
- [ ] If a merge conflict arises, resolve it on the feature branch — never on `main`
- [ ] Do not merge other branches into this branch unless they are part of the planned work
- [ ] Keep the branch focused — if scope creeps, create a new issue and a new branch

### Phase 6 — Prepare for Merge

Before requesting review:

- [ ] All commits follow the atomic commit standard (see `workflow/write-commits` skill)
- [ ] CI pipeline is green (all status checks pass)
- [ ] Branch is up to date with `main` (rebase if behind)
- [ ] No merge conflicts exist
- [ ] The PR is linked to the issue (e.g., `Resolves #102`)
- [ ] The PR follows the pull request standard (see `workflow/create-pr` skill)
- [ ] The branch is deleted automatically after merge (enabled in Phase 1)

### Phase 7 — Post-Merge Cleanup

After the PR is merged:

- [ ] Verify the branch was automatically deleted
- [ ] Confirm the issue is closed (if linked via `Resolves #`)
- [ ] Update the issue with any notes about the implementation
- [ ] If the branch was not deleted automatically, delete it manually

---

## Glossary

| Term | Definition | Full Reference |
|------|-----------|---------------|
| [Atomic](standards/GLOSSARY.md#atomic) | 1 Issue = 1 Branch = 1 PR. Single, discrete, testable unit of work. | Core building principle |
| [Squash Merge](standards/GLOSSARY.md#squash-merge) | Merging strategy that compresses all branch commits into a single commit on `main`. | Clean history strategy |
| [Pipeline](standards/GLOSSARY.md#pipeline) | Automated CI/CD system that validates code. The gatekeeper for `main`. | Automation standard |
| [Rulesets](standards/GLOSSARY.md#rulesets) | GitHub branch protection rules that enforce required reviews, status checks, and merge strategies. | Repository governance |
| [Execution Order](standards/GLOSSARY.md#execution-order) | Numeric priority assigned to issues. Lowest number = highest priority. | Task prioritization |
| [Stacked Branch](standards/GLOSSARY.md#stacked-branch) | Branching off an active feature branch instead of main when starting a blocked task. | Parallel execution technique |
| [Hotfix](standards/GLOSSARY.md#hotfix) | Critical production fix that may jump the queue but still requires PR and CI. | Emergency workflow |

---

## Chaining

**Upstream**:
- `workflow/plan-issues` — Issues define the work that branches implement; each issue maps to one branch

**Downstream**:
- `workflow/write-commits` — All commits on a branch follow the conventional commit standard
- `workflow/create-pr` — The branch produces one PR following the PR standard

**Cross-cutting**: Branch flow is the bridge between planning (issues) and implementation (commits + PRs). Every branch must trace back to an issue, and every PR must trace forward to a merge.

**Handoff**: After Phase 5, create the pull request and hand off to `workflow/create-pr` for self-review, CI validation, and merge preparation.
