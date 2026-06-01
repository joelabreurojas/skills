---
name: ship-release
description: "CI/CD gates, Semantic Versioning, changelog maintenance, and GitHub Releases. Use when cutting a release, updating the changelog, bumping versions, creating git tags, or publishing a GitHub Release."
license: MIT
trigger: "release, version bump, changelog, semver, git tag, github release, publish, deploy, ci cd, versioning, ship-release"
metadata:
  author: Joel Abreu Rojas
  version: "1.0.0"
  ssot_chapter: "19: CI/CD, Security Scanning & Releases"
  porter_note: "SemVer and changelog are universal; GitHub Release workflow adapts to GitLab Releases, npm publish, or equivalent."
---

# Ship Release

## Philosophy

A release is a formal, stable snapshot of the project that is ready for end-users. It is the culmination of every PR merged, every test passed, and every decision documented. The release process must be **automated, auditable, and reproducible** — manual version bumps without CI are an anti-pattern.

**Semantic Versioning (SemVer)** is the communication contract with users. `MAJOR.MINOR.PATCH` is not arbitrary — it encodes the *impact* of changes:
- **MAJOR**: Incompatible API or architectural changes. Users must update their code.
- **MINOR**: New functionality added in a backwards-compatible manner. Users can adopt at their leisure.
- **PATCH**: Backwards-compatible bug fixes. Users should upgrade immediately.

Getting SemVer wrong destroys trust. A PATCH that introduces a breaking change forces users to scramble. A MAJOR bump for a minor feature scares users away. The version number is a promise — break it, and users stop trusting your releases.

**The CHANGELOG is the human-readable interface** of the release. Git tags and GitHub Releases are for machines and CI. The CHANGELOG is for developers, product managers, and stakeholders who need to understand *what changed and why* — not just that something changed. Every release must have a CHANGELOG entry that is clear, accurate, and user-focused.

**CI is the gatekeeper.** No release is cut unless all automated checks pass. This includes formatting, linting, static analysis, type checking, automated testing, build verification, and security scanning. The pipeline enforces the standards that humans might forget.

---

## Anti-Patterns

### 1. Manual Version Bumps Without CI

Manually editing `package.json` or a version file to bump the version number without running the CI pipeline. This bypasses the quality gate and can ship untested code to production.

**Fix**: Version bumps are part of the CI/CD pipeline. The pipeline runs all checks, then bumps the version, creates the tag, and publishes the release. Manual intervention is for triggering the pipeline, not for performing the release.

### 2. Major Bump for Minor Changes

Bumping the MAJOR version for a backwards-compatible feature addition. This scares users into thinking they must update immediately, when in fact their code will continue to work. It also inflates the major version number, making future real breaking changes harder to communicate.

**Fix**: Follow SemVer strictly. MAJOR is for breaking changes only. MINOR is for new features. PATCH is for bug fixes. When in doubt, ask: "Will existing users' code break if they upgrade?" If no, it is not a MAJOR bump.

### 3. Missing CHANGELOG Entry

Cutting a release without updating `docs/CHANGELOG.md`. Users and stakeholders have no way to know what changed. They must read the git log or compare releases manually — both are time-consuming and error-prone.

**Fix**: Every release has a CHANGELOG entry. The entry goes in the `[Unreleased]` section during development. When the release is cut, `[Unreleased]` becomes the versioned section (e.g., `[1.2.0] - 2026-04-10`). A new empty `[Unreleased]` block is added at the top.

### 4. Cutting a Release with Failing CI

Merging a PR or cutting a release when the CI pipeline is red. This ships untested, unlinted, potentially broken code to production. The pipeline exists to catch problems before they reach users.

**Fix**: No PR merges with failing CI. No release is cut with failing CI. This is non-negotiable. If CI fails, fix the failure first. The pipeline is the gatekeeper — it does not negotiate.

### 5. Skipping the Git Tag

Releasing without a git tag. Without a tag, there is no way to identify which commit corresponds to which version. Rollbacks become guesswork. Users cannot pin to a specific version. CI/CD cannot identify the release point.

**Fix**: Every release is marked with a git tag (e.g., `v1.2.0`) pointing to the specific commit on `main`. The tag is created automatically by the CI/CD pipeline during the release process.

---

## Workflow

### Phase 1 — Verify CI/CD Pipeline

Before preparing a release, confirm the quality gate:

- [ ] **Formatting & Linting** — code style is enforced
- [ ] **Static Analysis & Type Checking** — logical errors caught before execution
- [ ] **Automated Testing** — Unit and Integration test suites pass
- [ ] **Build Verification** — application compiles successfully
- [ ] **Security Scanning** — Secret Scanning, Dependabot, CodeQL/SAST all clear
- [ ] **All PRs merged** — no open PRs targeted at the release

**Rule**: No release is cut with failing CI. Fix the failure first.

### Phase 2 — Update the CHANGELOG

Prepare the changelog for the release:

- [ ] Review all merged PRs since the last release
- [ ] Write a one-line summary for each notable change in the `[Unreleased]` section
- [ ] Categorize entries: Added, Changed, Deprecated, Removed, Fixed, Security
- [ ] Ensure entries are user-focused (what changed for *them*, not what you did internally)
- [ ] Include PR/commit references for traceability

**CHANGELOG format**:
```markdown
# Changelog

## [Unreleased]

### Added
- [empty until next development cycle]

## [1.2.0] - 2026-04-10

### Added
- JWT refresh token rotation for improved session security (#136)

### Fixed
- Null pointer exception on login with expired tokens (#105)

### Changed
- Migrated user service from SQLite to PostgreSQL (#120)
```

### Phase 3 — Determine the Version Bump

Apply Semantic Versioning rules:

- [ ] **MAJOR** — Are there incompatible API or architectural changes? (Breaking changes)
- [ ] **MINOR** — Are there new features added in a backwards-compatible manner?
- [ ] **PATCH** — Are there only backwards-compatible bug fixes?

**Decision matrix**:
| Change Type | Version Bump | Example |
|------------|-------------|---------|
| Breaking API change | MAJOR (2.0.0) | Removed endpoint, changed response format |
| New feature | MINOR (1.2.0) | Added refresh token rotation |
| Bug fix | PATCH (1.1.1) | Fixed null pointer on login |
| New feature + bug fix | MINOR (1.2.0) | Feature takes precedence over fix |
| Breaking + new feature | MAJOR (2.0.0) | Breaking takes precedence |

### Phase 4 — Create the Release PR

Prepare the release as a PR:

- [ ] Create a PR that changes `[Unreleased]` to the new version number in `CHANGELOG.md`
- [ ] Add a new, empty `[Unreleased]` block at the top
- [ ] Ensure the version number matches the intended bump (MAJOR/MINOR/PATCH)
- [ ] PR description includes a summary of all changes in the release
- [ ] CI passes on the release PR

### Phase 5 — Merge and Tag

After the release PR is approved and merged:

- [ ] Create a git tag: `v{MAJOR}.{MINOR}.{PATCH}` (e.g., `v1.2.0`)
- [ ] Tag points to the specific commit on `main` (the merge commit of the release PR)
- [ ] Tag is annotated with the version number and release date

### Phase 6 — Publish the GitHub Release

Create the formal release artifact:

- [ ] Create a GitHub Release from the git tag
- [ ] Copy the CHANGELOG entry for this version into the GitHub Release description
- [ ] Attach release artifacts (compiled binaries, Docker images, etc.) if applicable
- [ ] The CD pipeline automatically attaches build artifacts to the release
- [ ] Notify stakeholders of the release (if applicable)

---

## CI/CD Reference

The standard pipeline that gates every PR and release:

| Job | Purpose | Failure Impact |
|-----|---------|---------------|
| Formatting & Linting | Enforce code style | PR blocked — no merge |
| Static Analysis & Type Checking | Catch logical errors | PR blocked — no merge |
| Automated Testing | Unit + Integration suites | PR blocked — no merge |
| Build Verification | Ensure compilation | PR blocked — no merge |
| Secret Scanning | Detect API keys, tokens | PR blocked — no merge |
| Dependabot | CVE patching | PR blocked — no merge |
| CodeQL / SAST | Vulnerability detection | PR blocked — no merge |

**Social Preview Environments**: For UI/Frontend PRs, the CI pipeline spins up a temporary, isolated deployment of the branch for visual testing before merge.

---

## Glossary

| Term | Definition | Full Reference |
|------|-----------|---------------|
| [Semantic Versioning](standards/GLOSSARY.md#semantic-versioning-semver) | `MAJOR.MINOR.PATCH` scheme. Breaking = MAJOR, feature = MINOR, fix = PATCH. | Version communication |
| [CHANGELOG](standards/GLOSSARY.md#changelog) | Human-readable log of notable changes organized by version. `[Unreleased]` accumulates until release. | Release documentation |
| [Pipeline](standards/GLOSSARY.md#pipeline) | Automated CI/CD system that validates code. The gatekeeper — no release with failing CI. | Quality gate |
| [Git Tag](standards/GLOSSARY.md#git-tag) | Pointer to a specific commit marking a release (e.g., `v1.2.0`). Required for every release. | Release marker |
| [GitHub Release](standards/GLOSSARY.md#github-release) | Formal release artifact with CHANGELOG content and attached build artifacts. | Release publishing |
| [Squash Merge](standards/GLOSSARY.md#squash-merge) | Compresses branch commits into a single commit on `main` with PR context. | Clean history |
| [Conventional Commits](standards/GLOSSARY.md#conventional-commits) | Commit format driving automatic changelog entries and version bump decisions. | Commit standard |

---

## Chaining

**Upstream**:
- `workflow/create-pr` — Merged PRs are the source material for changelog entries and version decisions

**Downstream**:
- `standards/apply-principles` — After a release, the cycle restarts: new issues, new branches, new decisions
- `diagnose` — Post-release monitoring may trigger the diagnose skill for production issues

**Cross-cutting**: The release is the end of the implementation lifecycle. Every skill in the chain — from `standards/apply-principles` through `workflow/plan-issues`, `workflow/enforce-branch-flow`, `workflow/write-commits`, `workflow/log-adr`, and `workflow/create-pr` — converges here. The release is the proof that the entire process worked.

**Handoff**: After Phase 6, the release is published. The cycle restarts with new issues, new branches, and new PRs. If production issues arise, hand off to `diagnose` for blameless investigation.
