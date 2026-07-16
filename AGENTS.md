# Skills Repository — Agent Instructions

This repository contains original, composable LLM-first agent skills by Joel Abreu Rojas, based on the **SSOT (Single Source of Truth)** engineering framework. Designed for OpenCode, Claude Code, Cursor, or any coding agent.

Third-party skills (Matt Pocock, Emil Kowalski, Gentle AI) and plugins are documented in [`EXTERNAL.md`](EXTERNAL.md) — this repo contains only original skills.

## Repository Structure

Skills are organized into three buckets:

| Bucket | Skills | Focus |
|--------|--------|-------|
| `standards/` | 4 | Quality gates, principles, security, testing |
| `workflow/` | 11 | Engineering lifecycle: from PRD to release |
| `tools/` | 2 | Transversal: init, templates |

Supporting directories:
- `templates/` — Document templates (PRD, RFD, ADR, VIEWS, milestones)
- `EXTERNAL.md` — Registry of third-party skills and plugins

## How Skills Work

Each skill lives in its own directory named with a verb-noun convention:

```
tools/init-repo/SKILL.md
workflow/define-product/SKILL.md
standards/apply-principles/SKILL.md
```

### SKILL.md Anatomy

Every `SKILL.md` follows this structure:

1. **YAML frontmatter** — `name`, `description`, `trigger`, `metadata`
2. **Philosophy** — the "why" behind the skill (≥2 paragraphs)
3. **Anti-Patterns** — mistakes to avoid (≥3)
4. **Workflow** — numbered phases with checklists (≥3)
5. **Glossary** — terms with cross-references to `standards/GLOSSARY.md`
6. **Chaining** — upstream, downstream, and cross-cutting relationships

### Frontmatter Rules

```yaml
---
name: verb-noun
description: "One sentence. Covers what the skill does AND when to trigger it. Front-load trigger keywords."
license: MIT
trigger: "/skill-name, trigger words"
metadata:
  author: Your Name
  version: "1.0.0"
---
```

- `name`: lowercase hyphen-separated, matches directory name, max 64 chars
- `description`: write in third person. Cover WHAT and WHEN. Front-load trigger keywords
- `trigger`: comma-separated list of keywords/phrases that should activate this skill
- `metadata`: optional — `author`, `version`

### Naming Convention

All skill names follow **verb-noun**:
- `define-product`, `specify-solution`, `write-commits`
- `init-repo`, `apply-template`

Do NOT use prefixes (`ssot-`), underscores, or passive names (`templates` → `apply-template`).

### Cross-Reference Convention

Skills reference each other by **bucket path** in the Chaining section:

```
- `workflow/define-product` — PRD requirements are the primary input
- `standards/apply-principles` — Simplicity principle grounds design
- `tools/init-repo` — Creates the project scaffolding
```

External skills are referenced with a link to `EXTERNAL.md`:

```
- [`spell-out`](EXTERNAL.md#matt-pocock-skills) (externa) — Grill the plan before building
```

Always verify the referenced skill exists before adding a cross-reference. The directory is the source of truth — `standards/apply-principles/` exists, `standards/principles/` does not.

### Glossary Terms

Shared glossary terms live in `standards/GLOSSARY.md`. Skills reference them:

```markdown
| [Term](standards/GLOSSARY.md#term) | Definition | Source |
```

Cross-reference `<a>` anchors in GLOSSARY.md use the term slug (lowercase, hyphens).

## Lifecycle Flow

The 15 standards + workflow skills form a complete delivery pipeline:

```
/define-product → /plan-roadmap → /specify-solution
→ /design-views → /define-milestone → /plan-issues
→ /enforce-branch-flow → /write-commits → /log-adr
→ /create-pr → /apply-principles → /shift-left-security
→ /write-tests → /ship-release
```

## Template Files

The `templates/` directory contains document templates installed by `init-repo`:

| Directory | Files | Purpose |
|-----------|-------|---------|
| `templates/PRD/` | 5 | Product Requirements Documents |
| `templates/RFD/` | 6 | Request for Design (technical specs) |
| `templates/ADR/` | 1 | Architecture Decision Records |
| `templates/VIEWS/` | 2 | Visual design specifications |
| `templates/milestones/` | 2 | Milestone blueprints and issue tasks |

These are master copies. Projects copy them into their own `docs/` directories.

## Editing Guidelines

1. **One concern per skill**. If a skill covers two unrelated workflows, split it
2. **≥3 anti-patterns**. Every skill must document what NOT to do
3. **≥3 workflow phases**. Every skill must have actionable phases with checklists
4. **Chaining references must exist**. Verify directories before adding cross-references
5. **Description must include trigger context**. A user/agent should know WHEN to use it from the description alone
6. **External skills go in EXTERNAL.md**. No third-party skills in repo; document them there
7. **AGENTS.md + CONTEXT.md keep the global picture**. Update both when adding/removing/renaming skills, or when the bucket structure changes
8. **Templates are cargo, not skills**. Template files go in `templates/`; the skill that teaches their use is `apply-template`
