---
name: init-repo
description: "Bootstrap a new SSOT project — detect agent, create CONTEXT.md, scaffold directory structure, configure branch protection, install templates. Use when starting a new project, converting an existing project to SSOT, or setting up CI/CD for a new repository."
license: MIT
trigger: "/init-repo, init repo, new project, bootstrap, scaffold, repository setup, project initialization, SSOT setup, new repository"
metadata:
  author: Joel Abreu Rojas
  version: "1.0.0"
  ssot_chapter: "02: Project Structure"
  porter_note: "This is the MOST important tool skill — it creates the project scaffolding that makes the entire SSOT workflow possible. Run this first on any new project."
---

# Init Repo

## Philosophy

### Structure precedes workflow

A project's directory structure is the physical manifestation of its architecture philosophy. SSOT's phased delivery model requires a specific layout: PRDs in `docs/PRD/`, RFDs in `docs/RFD/`, ADRs in `docs/ADR/`, views in `docs/VIEWS/`. This structure is not decorative — it tells every contributor where to find each type of decision and how to contribute.

Without this structure, documentation scatters. ADRs appear in PR descriptions. PRDs live in Notion. RFDs get lost in Google Docs. The single source of truth fractures before the project begins. Every contributor invents their own organization system, and finding any document becomes a tribal knowledge game of "who was working on that" instead of "it is in docs/RFD/."

### Agent awareness is non-negotiable

Developers use different AI agents: OpenCode, Claude Code, Cursor, Copilot. Each expects its instructions in a different file (`AGENTS.md`, `CLAUDE.md`, `.cursorrules`). If the project does not speak the agent's language, the agent operates with zero context.

Initialization must detect the developer's agent and create the correct instruction file. Not one-size-fits-all. Not "documentation in the README." The agent's instruction file is the first thing it reads — make it count. An agent without project context is a junior developer who has never seen the codebase and has no one to ask for help.

### Automation from day one

Branch protection, CI checks, and community files are not "things to set up later." Setting them up on day one means every commit from commit #1 follows the rules. Retroactively applying branch protection is harder because the rules may break existing workflows.

Automation gates enforce standards consistently. A human reviewer might miss a missing test or a formatting issue. An automated CI check catches it every time. The principle: if a machine can check it, a human should not have to.

### Templates lower the barrier to contribution

When every document type has a template, contributors do not need to ask "what should this document include?" They open the template and start filling. The template encodes the collective knowledge of what matters for each document type.

New contributors benefit most. A PRD template tells them what a good PRD looks like. An ADR template teaches them how to think about architecture decisions. Templates are teaching tools disguised as forms.

---

## Anti-Patterns

### 1. Skipping Agent Detection

Creating `AGENTS.md` for every project regardless of whether the user uses OpenCode, or creating `.cursorrules` for a project that uses Claude Code. The agent instruction file is useless if the agent does not read it.

**Fix**: Detect which agent the developer uses and create only the corresponding file. Support OpenCode (`AGENTS.md`), Claude Code (`CLAUDE.md`), Cursor (`.cursorrules`), and Copilot (`.github/copilot-instructions.md`).

### 2. No CONTEXT.md

Starting a project without a CONTEXT.md that maps the project lifecycle. Without it, new contributors (and AI agents) have no starting point to understand the project structure, conventions, and current state.

**Fix**: Create `CONTEXT.md` as the first document in the project. It should contain: project purpose, directory structure (lifecycle map), key conventions, and cross-references to standards.

### 3. No Branch Protection Rules

Relying on developer discipline to enforce branch naming, PR requirements, and status checks. Without automation, rules are optional, and "optional rules" are not rules at all.

**Fix**: Configure branch protection on day one for `main` and release branches. Require PRs, status checks, linear history, and no direct pushes.

### 4. Skipping Community Files

No `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, `SECURITY.md`, or issue/PR templates. Every contributor reinvents the contribution workflow, leading to inconsistent PRs, missing information, and miscommunication.

**Fix**: Install community files and issue/PR templates during initialization. They are cheap to create and expensive to retrofit.

### 5. One-Size-Fits-All Agent Configuration

Creating the same agent instructions for every project regardless of language, framework, or team conventions. Agent instructions must be project-specific to be useful.

**Fix**: Tailor agent instructions to the project: language-specific linting commands, framework-specific patterns, team-specific conventions (commit format, branch naming, PR requirements).

### 6. Skipping .gitignore

No `.gitignore` or using a generic one that misses platform-specific artifacts (`.env`, build output, IDE config, OS files, dependency directories). Committing build artifacts and secrets becomes an immediate cleanup burden.

**Fix**: Generate a language-appropriate `.gitignore` using gitignore.io or platform templates. Include: build output, dependency directories, environment files, IDE/editor config, OS files, and generated code.

---

## Workflow

### Phase 1 — Detect Agent and Create Agent Instructions

Identify which AI agent the project will use and create the appropriate instruction file:

- [ ] Detect agent: Check environment variables, user preference, or existing config files
- [ ] **OPENCODE**: Create `.config/opencode/AGENTS.md` with persona, rules, and skill loading instructions
- [ ] **Claude Code**: Create `CLAUDE.md` with system instructions, tool access rules, and project conventions
- [ ] **Cursor**: Create `.cursorrules` with project context, code style, and framework-specific rules
- [ ] **GitHub Copilot**: Create `.github/copilot-instructions.md` with project context
- [ ] If unsure, create instruction files for ALL detected agents (a team may use multiple)

**Agent instruction file must include**:
- Project purpose and one-line description
- Directory structure and where to find each artifact type
- Key conventions: commit format, branch naming, PR requirements
- Links to SSOT skills or workflow documentation
- Testing requirements and command shortcuts

### Phase 2 — Create CONTEXT.md with Lifecycle Map

- [ ] Project name and purpose
- [ ] Directory structure — the project lifecycle map showing where every artifact lives:

```
docs/
├── PRD/           Product Requirements Documents
├── RFD/           Request for Design (technical specs)
├── ADR/           Architecture Decision Records
├── VIEWS/         Visual design specifications
└── MILESTONES/    Domain-based epic definitions
```

- [ ] Key conventions: branch naming, commit format, PR discipline
- [ ] Current state: what phase the project is in (explore, design, implement, operate)
- [ ] Cross-references to standards/GLOSSARY.md and applicable skills

### Phase 3 — Scaffold Directory Structure

- [ ] Create `docs/` with subdirectories: `PRD/`, `RFD/`, `ADR/`, `VIEWS/`, `MILESTONES/`
- [ ] Create `templates/` directory and install SSOT document templates from the skill's bundled templates:

  | Template | Source | Destination |
  |----------|--------|-------------|
  | PRD | `templates/PRD/` | `templates/PRD/` (5 files) |
  | RFD | `templates/RFD/` | `templates/RFD/` (6 files) |
  | ADR | `templates/ADR/` | `templates/ADR/` (1 file) |
  | Views | `templates/VIEWS/` | `templates/VIEWS/` (2 files) |
  | Milestones | `templates/milestones/` | `templates/milestones/` (2 files) |

  These template files are the master copies — contributors copy them into `docs/` when starting a new document. Never edit them inside `docs/`; if a template needs improvement, update the source in the skills repository.

- [ ] Create `.github/` or `gitlab/` for CI/CD and community files
- [ ] Create CI/CD configuration (GitHub Actions, GitLab CI, etc.) with:
  - Lint and type checking
  - Test runner
  - Security scanning (SAST, dependency audit)
  - Build and deploy (if applicable)
- [ ] Create `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, `SECURITY.md`
- [ ] Create `.gitignore` appropriate for the project language and framework
- [ ] Create `README.md` with project overview and quickstart

### Phase 4 — Configure Branch Protection

- [ ] **Branch protection for `main`**:
  - Require pull request before merging
  - Require status checks to pass (CI, lint, test)
  - Require linear history (no merge commits)
  - Restrict direct pushes
  - Require up-to-date branches
- [ ] **Release branch protection** if using release branches (e.g., `release/*`)
- [ ] **Labels**: Create standard labels for issues and PRs (`bug`, `enhancement`, `docs`, `question`, `breaking-change`, `good-first-issue`)
- [ ] Configure auto-merge for approved PRs (optional, per team preference)

**Platform-specific commands**:

```bash
# GitHub: Use gh CLI or GitHub API
gh api repos/:owner/:repo/branches/main/protection \
  --method PUT \
  --field required_status_checks='{"strict":true,"contexts":["continuous-integration"]}' \
  --field enforce_admins=true \
  --field required_pull_request_reviews='{"required_approving_review_count":1}'
```

### Phase 5 — Install PR and Issue Templates

- [ ] **PR template** (`.github/PULL_REQUEST_TEMPLATE.md`): Structure for describing changes, including issue reference, testing done, and screenshots for UI changes
- [ ] **Bug report template** (`.github/ISSUE_TEMPLATE/bug_report.md`): Reproduction steps, expected vs actual behavior, environment
- [ ] **Feature request template** (`.github/ISSUE_TEMPLATE/feature_request.md`): Problem statement, proposed solution, alternatives considered
- [ ] **Configuration template**: Link the templates to project labels and milestones

### Phase 6 — Create CI/CD Pipeline

Set up automated quality gates from day one:

- [ ] Choose CI platform: GitHub Actions, GitLab CI, Jenkins, CircleCI
- [ ] **Lint and format check**: Fail on lint errors and formatting violations
- [ ] **Type checking**: For typed languages — fail on type errors
- [ ] **Test runner**: Run unit tests on every push, integration tests on PRs to main
- [ ] **Security scanning**: SAST (CodeQL, SonarCloud), dependency audit (Dependabot, Renovate)
- [ ] **Build check**: Verify the project builds successfully
- [ ] **Deployment**: Optional — configure deploy on merge to main or release branches

```yaml
# Minimal GitHub Actions workflow (.github/workflows/ci.yml)
name: CI
on: [push, pull_request]
jobs:
  ci:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Lint
        run: make lint
      - name: Test
        run: make test
      - name: Build
        run: make build
```

### Phase 7 — Verify Initialization

Run a complete verification before making the first commit:

- [ ] Agent instruction file(s) exist and contain accurate project context
- [ ] `CONTEXT.md` exists with lifecycle map and conventions
- [ ] `docs/` directory structure exists with all subdirectories: `PRD/`, `RFD/`, `ADR/`, `VIEWS/`, `MILESTONES/`
- [ ] `templates/` directory exists with PRD/, RFD/, ADR/, VIEWS/, milestones/ populated
- [ ] CI/CD pipeline is configured and passing on the initial commit
- [ ] Branch protection is active on `main`
- [ ] Issue and PR templates are installed in `.github/`
- [ ] Community files exist (`CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, `SECURITY.md`)
- [ ] `.gitignore` excludes platform-appropriate artifacts (node_modules, .env, build/, dist/, .idea/, .vscode/, *.log)
- [ ] `README.md` has a meaningful project overview, not a placeholder
- [ ] First commit includes all scaffolding files (do not defer any to "later")

**Verification checklist for multi-developer teams**:
- Can a new developer clone the repo and run the project in under 5 minutes?
- Can a new developer find where to file a bug? Where to propose a feature?
- Does the CI pipeline provide fast feedback (under 5 minutes for lint and unit tests)?
- Are there any manual setup steps not documented in README.md?
- Are secrets and environment variables documented without exposing their values?
- Is there a `DEVELOPMENT.md` or equivalent with setup instructions?

**Handling existing projects**:
If initializing SSOT on an existing project (not greenfield):

- [ ] Phase 1: Detect the agent and create/update instruction files
- [ ] Phase 2: Create CONTEXT.md with the current project structure and conventions
- [ ] Phase 3: Create docs/ directories — do NOT move existing docs, just create the structure for new ones
- [ ] Phase 4: Review and update existing branch protection
- [ ] Phase 5: Add templates if missing
- [ ] Mark existing conventions that differ from SSOT standards in CONTEXT.md
- [ ] Do NOT force-migrate existing docs — apply SSOT structure to new work only, migrate old docs gradually

**Output**: The project is initialized and ready for the SSOT workflow. The first commit should include all scaffolding files. After initialization, proceed to `workflow/define-product` to create the first PRD.

---

## Workflow Summary

The initialization sequence at a glance:

```
Phase 1: Detect Agent ──→ Create AGENTS.md / CLAUDE.md / .cursorrules
Phase 2: CONTEXT.md   ──→ Lifecycle map, conventions, project purpose
Phase 3: Scaffold      ──→ docs/ subdirectories + templates/ (PRD, RFD, ADR, VIEWS, milestones)
Phase 4: Branch Prot.  ──→ main protection, labels, linear history
Phase 5: PR Templates  ──→ GitHub PR/issue templates linked to labels
Phase 6: CI/CD         ──→ Lint, test, build, security scanning
Phase 7: Verify        ──→ Full checklist before first commit
```

Run all seven phases in order. Do not skip Phases 1 and 2 — they are the most important. An initialized repo without CONTEXT.md and agent instructions is not ready for the SSOT workflow.

---

## Glossary

| Term | Definition | Source |
|------|-----------|--------|
| SSOT | Single Source of Truth — all project knowledge lives in the repository as structured documents. | Philosophy |
| Branch Protection | Repository rules that enforce PR requirements, status checks, and push restrictions on branches. | DevOps |
| Lifecycle Map | Directory structure that mirrors the project workflow — every artifact type has a canonical location. | Project Structure |
| Agent Instructions | Configuration file that tells an AI agent how to behave in a project context (`AGENTS.md`, `CLAUDE.md`, `.cursorrules`). | AI Systems |

---

## Chaining

**Upstream**:
- `standards/apply-principles` — SSOT philosophy and automation principles guide every initialization decision
- [`spell-out`](EXTERNAL.md#matt-pocock-skills) (externa) — Before scaffolding, spell out the project intent to ensure the structure matches the needs

**Downstream**:
- `workflow/define-product` — After initialization, create the first PRD in `docs/PRD/`
- `workflow/enforce-branch-flow` — Branch protection rules must align with branch naming conventions
- `workflow/ship-release` — Release infrastructure initialized here supports the release workflow

**Cross-cutting**: Init-repo is the FIRST skill to run on any new project. Every other skill depends on the structure and conventions established here. Re-initialization may be needed when adopting SSOT on an existing project — run Phase 2-6, assess migration needs, and update CONTEXT.md with current state before proceeding.
