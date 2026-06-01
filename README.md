<h1 align='center'>
    🛠️ Skills
</h1>

<p align='center'>
    <em>A curated collection of composable, LLM-first agent skills for professional software engineering.</em>
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

24 composable, LLM-first agent skills for OpenCode, Claude Code, Cursor, or any coding agent. Curated from three sources:

| Source | Author | Skills |
|--------|--------|--------|
| **SSOT** engineering framework | Joel Abreu Rojas | 17 — standards, workflow, tools |
| Matt Pocock's engineering skills | Matt Pocock | 4 — debug, explain, pack, spell |
| Emil Kowalski's design engineering | Emil Kowalski | 3 — animations, components, UI polish |

**These skills help agents to:**
- Bootstrap projects with `init-repo` — AGENTS.md, CONTEXT.md, templates, CI/CD
- Define products with `define-product` — PRDs from vision to success metrics
- Design solutions with `specify-solution` — RFDs for structure, data, interfaces, ops
- Maintain quality with `apply-principles`, `write-tests`, `shift-left-security`
- Debug systematically with `debug-code` — reproduce, instrument, fix, regress
- Ship releases with `create-pr`, `ship-release` — PR lifecycle and SemVer automation
- ...and 18 more!

&nbsp;

### 🚀 Quickstart

To use these skills in your project:

1. **Install** the skills with the `skills` CLI:
   ```bash
   npx skills@latest add <tu-usuario>/skills
   ```
2. **Initialize** your project:
   ```bash
   /init-repo
   ```
3. The agent reads `CONTEXT.md` and knows which commands to use in each phase.

> [!NOTE]
> If you use OpenCode, skills are auto-loaded from `.atl/skill-registry.md`. No manual install needed.

&nbsp;

### 📦 Skills

The repository is organized into four buckets:

<div align="center">

| Bucket | Skills | Focus |
|--------|-------|-------|
| **`standards/`** | 5 | Quality gates, principles, security, testing, UI polish |
| **`workflow/`** | 11 | Engineering lifecycle: from PRD to release |
| **`ui/`** | 2 | Component implementation and animation design |
| **`tools/`** | 6 | Transversal: debug, explain, context pack, init, templates |

</div>

#### 📐 Standards

| Command | Description |
|---------|------------|
| `/apply-principles` | Apply the 8 SSOT pillars to engineering decisions |
| `/design-api` | Design REST APIs with versioning, idempotency, error envelopes |
| `/shift-left-security` | End-to-end security: PoLP, OWASP, secrets, supply chain |
| `/write-tests` | Test pyramid, AAA, TDD, coverage, flaky triage |
| `/polish-ui` | UI audit: performance, accessibility, blur, transforms |

#### 🔄 Workflow

| Command | Description |
|---------|------------|
| `/define-product` | Full PRD lifecycle: vision, stories, requirements |
| `/plan-roadmap` | Now/Next/Later, JIT design gate, sequencing |
| `/specify-solution` | Full RFD lifecycle: data model, interface, ops |
| `/design-views` | UI specs with 4 states (empty/loading/error/success) |
| `/define-milestone` | Epics, Definition of Done, vertical slice progression |
| `/plan-issues` | Atomic decomposition with execution order |
| `/enforce-branch-flow` | Repo settings, naming, one-issue-per-branch, hygiene |
| `/write-commits` | Conventional Commits, atomic discipline, history review |
| `/log-adr` | Architecture Decision Records with status flip |
| `/create-pr` | PR lifecycle: self-review, peer review, squash merge |
| `/ship-release` | SemVer, CHANGELOG, CI gates, tagging |

#### 🎨 UI

| Command | Description |
|---------|------------|
| `/design-animations` | Decision framework: easing, timing, springs, clip-path |
| `/build-components` | Component principles: buttons, popovers, tooltips |

#### 🧰 Tools

| Command | Description |
|---------|------------|
| `/spell-out` | Review and challenge the plan before designing |
| `/debug-code` | Debug loop: reproduce → hypothesize → fix |
| `/explain-code` | Explain code in system context |
| `/pack-context` | Compact session context for handoff |
| `/init-repo` | Bootstrap project: AGENTS.md, CONTEXT.md, structure, templates |
| `/apply-template` | Template reference for PRD/RFD/ADR/Views/Milestones |

&nbsp;

### 🔗 Lifecycle

The 16 standards + workflow skills form a complete delivery pipeline:

```
/spell-out → /define-product → /plan-roadmap → /specify-solution
→ /design-views → /define-milestone → /plan-issues
→ /enforce-branch-flow → /write-commits → /log-adr
→ /create-pr → /apply-principles → /shift-left-security
→ /polish-ui → /write-tests → /ship-release
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

### 🏷️ Origins

These skills are adapted from three bodies of work:

- **SSOT** — Joel Abreu Rojas's opinionated engineering framework: 19 chapters covering the full development lifecycle, from product definition through design, execution, and delivery.
- **Matt Pocock's skills** — Real-world engineering skills from a decade of professional TypeScript and full-stack development. Adapted: `debug-code`, `explain-code`, `pack-context`, `spell-out`.
- **Emil Kowalski's design engineering** — Purposeful animation and component design philosophy. Adapted: `design-animations`, `build-components`, `polish-ui`.

Each adapted skill carries a `derived_from` field in its frontmatter linking back to the original work.

&nbsp;

### 📌 What This Is NOT

- **NOT a project framework.** These skills teach workflows — they don't replace your project's `opencode.json` or CI/CD. That's what `init-repo` bootstraps.
- **NOT language-specific.** The skills are technology-agnostic. They work for Go backends, React frontends, or embedded systems.
- **NOT a passive reference.** Each skill has actionable phases with checklists. Skills are meant to be executed, not just read.
- **NOT a replacement for judgment.** Skills encode best practices but don't replace human decision-making. Use them as a force multiplier, not a crutch.

&nbsp;

### 👐 Contribute

- Found a bug in a skill? Open an issue or PR.
- Want to improve a skill? Read [`AGENTS.md`](/AGENTS.md) for conventions and [`CONTEXT.md`](/CONTEXT.md) for project context.
- Questions? Start a discussion.

Licensed under MIT.
