# Skills Repository — Agent Instructions

This repository contains a curated collection of composable, LLM-first agent skills for professional software engineering. Built primarily from the **SSOT (Single Source of Truth)** engineering framework, with additions adapted from Matt Pocock's engineering skills and Emil Kowalski's design engineering philosophy. Designed for OpenCode, Claude Code, Cursor, or any coding agent.

## Repository Structure

Skills are organized into four buckets, each with a distinct focus:

| Bucket | Skills | Focus |
|--------|--------|-------|
| `standards/` | 5 | Quality gates, principles, security, testing, UI polish |
| `workflow/` | 11 | Engineering lifecycle: from PRD to release |
| `ui/` | 2 | Component implementation and animation design |
| `tools/` | 6 | Transversal: debug, explain, context pack, init, templates |

Supporting directories:
- `templates/` — Document templates (PRD, RFD, ADR, VIEWS, milestones)
- `.atl/skill-registry.md` — Skill registry for OpenCode loading

## How Skills Work

Each skill lives in its own directory named with a verb-noun convention:

```
tools/init-repo/SKILL.md
workflow/define-product/SKILL.md
standards/apply-principles/SKILL.md
ui/design-animations/SKILL.md
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
- `metadata`: optional — `author`, `version`, `derived_from`, `porter_note`

### Derived Skills

Skills adapted from existing work use two frontmatter fields for attribution:

- `author`: the original author's name
- `derived_from`: a reference back to the source (e.g., `"mattpocock/skills — diagnose"`)

Skills authored from scratch carry only the `author` field.

### Naming Convention

All skill names follow **verb-noun**:
- `define-product`, `specify-solution`, `write-commits`, `enforce-branch-flow`
- `design-animations`, `build-components`, `debug-code`, `explain-code`
- `init-repo`, `apply-template`, `spell-out`, `pack-context`

Do NOT use prefixes (`ssot-`), underscores, or passive names (`templates` → `apply-template`).

### Cross-Reference Convention

Skills reference each other by **bucket path** in the Chaining section:

```
- `workflow/define-product` — PRD requirements are the primary input
- `standards/apply-principles` — Simplicity principle grounds design
- `tools/init-repo` — Creates the project scaffolding
- `ui/design-animations` — Animation decisions feed into components
```

Always verify the referenced skill exists before adding a cross-reference. The directory is the source of truth — `standards/apply-principles/` exists, `standards/principles/` does not.

### Glossary Terms

Shared glossary terms live in `standards/GLOSSARY.md`. Skills reference them:

```markdown
| [Term](standards/GLOSSARY.md#term) | Definition | Source |
```

Cross-reference `<a>` anchors in GLOSSARY.md use the term slug (lowercase, hyphens).

## Lifecycle Flow

The standards + workflow skills form a complete delivery pipeline:

```
/spell-out → /define-product → /plan-roadmap → /specify-solution
→ /design-views → /define-milestone → /plan-issues
→ /enforce-branch-flow → /write-commits → /log-adr
→ /create-pr → /apply-principles → /shift-left-security
→ /polish-ui → /write-tests → /ship-release
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
6. **Attribute adapted work**. If a skill is derived from someone else's work, add `derived_from` in the frontmatter
7. **AGENTS.md + CONTEXT.md keep the global picture**. Update both when adding/removing/renaming skills, or when the bucket structure changes
8. **Templates are cargo, not skills**. Template files go in `templates/`; the skill that teaches their use is `apply-template`
