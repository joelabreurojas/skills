<h1 align='center'>
    🛠️ Skills
</h1>

<p align='center'>
    <em>Original, composable LLM-first agent skills for professional software engineering.</em>
</p>

<h6 align='center'>
   <a href="/LICENSE">
      <img alt='MIT License' src='https://img.shields.io/static/v1.svg?label=License&message=MIT&logoColor=d9e0ee&colorA=302d41&colorB=3094FF'/>
   </a>
   <a href="/CONTEXT.md">
      <img alt='Context' src='https://img.shields.io/static/v1.svg?label=Docs&message=CONTEXT&logoColor=d9e0ee&colorA=302d41&colorB=3094FF'/>
   </a>
   <a href="/AGENTS.md">
      <img alt='Agent Guide' src='https://img.shields.io/static/v1.svg?label=Agent&message=Guide&logoColor=d9e0ee&colorA=302d41&colorB=3094FF'/>
    </a>
</h6>

&nbsp;

### ✨ Overview

**18** original skills for OpenCode, Claude Code, Cursor, or any coding agent. Authored by [Joel Abreu Rojas](https://github.com/joelabreurojas) based on the **SSOT** engineering framework.

> [!NOTE]
> Third-party skills (Matt Pocock, Emil Kowalski, Gentle AI) and plugins are documented in [`EXTERNAL.md`](EXTERNAL.md). This repo contains only original skills.

**These skills help agents to:**
- Bootstrap projects with `init-repo` — AGENTS.md, CONTEXT.md, templates, CI/CD
- Define products with `define-product` — PRDs from vision to success metrics
- Design solutions with `specify-solution` — RFDs for structure, data, interfaces, ops
- Maintain quality with `apply-principles`, `write-tests`, `shift-left-security`
- Ship releases with `create-pr`, `ship-release` — PR lifecycle and SemVer automation
- ...and 12 more!

&nbsp;

### 🚀 Quickstart

```bash
# Install skills
cp -r standards/* ~/.config/opencode/skills/
cp -r workflow/* ~/.config/opencode/skills/
cp -r tools/* ~/.config/opencode/skills/
cp standards/GLOSSARY.md ~/.config/opencode/skills/standards/

# Refresh registry
gentle-ai skill-registry refresh --force
```

&nbsp;

### 📦 Skills

<div align="center">

| Bucket | Skills | Focus |
|--------|-------|-------|
| **`standards/`** | 4 | Quality gates, principles, security, testing |
| **`workflow/`** | 11 | Engineering lifecycle: from PRD to release |
| **`tools/`** | 3 | Transversal: init, templates, model assignment |

</div>

#### 📐 Standards

| Skill | Description |
|-------|-------------|
| [`apply-principles`](standards/apply-principles/) | Apply the 8 SSOT pillars to engineering decisions |
| [`design-api`](standards/design-api/) | Design REST APIs with versioning, idempotency, error envelopes |
| [`shift-left-security`](standards/shift-left-security/) | End-to-end security: PoLP, OWASP, secrets, supply chain |
| [`write-tests`](standards/write-tests/) | Test pyramid, AAA, TDD, coverage, flaky triage |

#### 🔄 Workflow

| Skill | Description |
|-------|-------------|
| [`define-product`](workflow/define-product/) | Full PRD lifecycle: vision, stories, requirements |
| [`plan-roadmap`](workflow/plan-roadmap/) | Now/Next/Later, JIT design gate, sequencing |
| [`specify-solution`](workflow/specify-solution/) | Full RFD lifecycle: data model, interface, ops |
| [`design-views`](workflow/design-views/) | UI specs with 4 states (empty/loading/error/success) |
| [`define-milestone`](workflow/define-milestone/) | Epics, Definition of Done, vertical slice progression |
| [`plan-issues`](workflow/plan-issues/) | Atomic decomposition with execution order |
| [`enforce-branch-flow`](workflow/enforce-branch-flow/) | Repo settings, naming, one-issue-per-branch, hygiene |
| [`write-commits`](workflow/write-commits/) | Conventional Commits, atomic discipline, history review |
| [`log-adr`](workflow/log-adr/) | Architecture Decision Records with status flip |
| [`create-pr`](workflow/create-pr/) | PR lifecycle: self-review, peer review, squash merge |
| [`ship-release`](workflow/ship-release/) | SemVer, CHANGELOG, CI gates, tagging |

#### 🧰 Tools

| Skill | Description |
|-------|-------------|
| [`init-repo`](tools/init-repo/) | Bootstrap project: AGENTS.md, CONTEXT.md, structure, templates |
| [`apply-template`](tools/apply-template/) | Template reference for PRD/RFD/ADR/Views/Milestones |
| [`sdd-model-assigner`](tools/sdd-model-assigner/) | Discover free models, assign to SDD phases and JD agents, handle quota exhaustion |

&nbsp;

### 🔗 Lifecycle

The 15 standards + workflow skills form a complete delivery pipeline:

```
/define-product → /plan-roadmap → /specify-solution
→ /design-views → /define-milestone → /plan-issues
→ /enforce-branch-flow → /write-commits → /log-adr
→ /create-pr → /apply-principles → /shift-left-security
→ /write-tests → /ship-release
```

&nbsp;

### 📂 Templates

The `templates/` directory holds 16 master document templates:

| Directory | Files | Purpose |
|-----------|-------|---------|
| `templates/PRD/` | 5 | Product Requirements Documents |
| `templates/RFD/` | 6 | Request for Design (technical specs) |
| `templates/ADR/` | 1 | Architecture Decision Records |
| `templates/VIEWS/` | 2 | Visual design specifications |
| `templates/milestones/` | 2 | Milestone blueprints and issue tasks |

Installed into target projects by `init-repo`.

&nbsp;

### 📌 External Skills

Third-party skills I use but do not store in this repo:

| Source | Skills | Registry |
|--------|--------|----------|
| **Gentle AI** | go-testing, branch-pr, chained-pr, issue-creation, judgment-day, work-unit-commits, cognitive-doc-design, comment-writer, skill-creator, skill-improver, skill-registry + 11 SDD | [`EXTERNAL.md`](EXTERNAL.md#gentle-ai-suite) |
| **Matt Pocock** | debug-code, explain-code, pack-context, spell-out | [`EXTERNAL.md`](EXTERNAL.md#matt-pocock-skills) |
| **Emil Kowalski** | polish-ui, build-components, design-animations | [`EXTERNAL.md`](EXTERNAL.md#emil-kowalski-skills) |
| **Plugins** | ponytail, video-use, graphify | [`EXTERNAL.md`](EXTERNAL.md#plugins) |

&nbsp;

### 📌 What This Is NOT

- **NOT a project framework.** These skills teach workflows — they don't replace your project's `opencode.json` or CI/CD.
- **NOT language-specific.** Skills are technology-agnostic. Works for Go backends, React frontends, or embedded systems.
- **NOT a passive reference.** Each skill has actionable phases with checklists. Meant to be executed, not just read.
- **NOT a replacement for judgment.** Skills encode best practices but don't replace human decision-making.

&nbsp;

### 👐 Contribute

- Found a bug in a skill? Open an issue or PR.
- Want to improve a skill? Read [`AGENTS.md`](/AGENTS.md) for conventions and [`CONTEXT.md`](/CONTEXT.md) for project context.
- Questions? Start a discussion.

Licensed under MIT.
