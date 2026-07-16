# Skills Repository — Project Context

## Purpose

Original, composable LLM-first agent skills for professional software engineering by Joel Abreu Rojas. Based on the **SSOT (Single Source of Truth)** engineering framework. Designed for any coding agent — OpenCode, Claude Code, Cursor, GitHub Copilot.

This repo contains **only original skills**. Third-party skills (Matt Pocock, Emil Kowalski, Gentle AI) and plugins are documented in [`EXTERNAL.md`](EXTERNAL.md).

## Current State

**Stable.** 18 skills across 3 buckets, all authored and cross-referenced.

- 18 skills across standards/, workflow/, tools/
- 16 template files across 5 document types
- Shared glossary in `standards/GLOSSARY.md` (30+ terms)

## Repository Structure

```
skills/
├── standards/               # 4 skills — quality gates, principles
│   ├── GLOSSARY.md          # Shared terminology index
│   ├── apply-principles/
│   ├── design-api/
│   ├── shift-left-security/
│   └── write-tests/
├── workflow/                # 11 skills — engineering lifecycle
│   ├── define-product/
│   ├── plan-roadmap/
│   ├── specify-solution/
│   ├── design-views/
│   ├── define-milestone/
│   ├── plan-issues/
│   ├── enforce-branch-flow/
│   ├── write-commits/
│   ├── log-adr/
│   ├── create-pr/
│   └── ship-release/
├── tools/                   # 3 skills — transversals
│   ├── init-repo/
│   ├── apply-template/
│   └── sdd-model-assigner/
├── templates/               # 16 master template files
│   ├── PRD/                 # 5 — Product Requirements Documents
│   ├── RFD/                 # 6 — Request for Design (technical specs)
│   ├── ADR/                 # 1 — Architecture Decision Records
│   ├── VIEWS/               # 2 — Visual design specifications
│   └── milestones/          # 2 — Milestone blueprints and issue tasks
├── EXTERNAL.md              # Registry of third-party skills and plugins
├── AGENTS.md                # Agent instructions for this repo
├── CONTEXT.md               # This file
└── README.md                # Human-facing index
```

## Lifecycle Flow

The 15 standards + workflow skills implement a complete delivery pipeline:

```
/define-product → /plan-roadmap → /specify-solution
→ /design-views → /define-milestone → /plan-issues
→ /enforce-branch-flow → /write-commits → /log-adr
→ /create-pr → /apply-principles → /shift-left-security
→ /write-tests → /ship-release
```

## Key Conventions

### Naming

- **Verb-noun**: `define-product`, `write-commits`
- **No prefixes**: never `ssot-` or `gentle-`
- **No underscores**: use hyphens
- **Command-like**: the name reads as an action (`init-repo`, not `repo-init`)

### Skill Anatomy (every SKILL.md)

1. YAML frontmatter (`name`, `description`, `trigger`, `metadata`)
2. Philosophy (≥2 paragraphs explaining the "why")
3. Anti-Patterns (≥3 mistakes to avoid)
4. Workflow (≥3 phases with actionable checklists)
5. Glossary (terms cross-referenced to `standards/GLOSSARY.md`)
6. Chaining (upstream, downstream, cross-cutting)

### Cross-References

- Skills reference each other by **bucket path**: `workflow/define-product`, `standards/apply-principles`
- External skills are referenced via [`EXTERNAL.md`](EXTERNAL.md): `[skill](EXTERNAL.md#section) (externa)`
- Glossary links use relative paths: `[Term](standards/GLOSSARY.md#term)`
- Anchor slugs in GLOSSARY.md use lowercase-hyphenated term names
- Every cross-reference must point to an existing directory — verify before adding

### Template vs Skill

- **Skills** teach process (how to do something)
- **Templates** provide structure (what to fill in)
- `apply-template` is the skill that teaches template usage
- `init-repo` installs templates into target projects

### Adding New Skills

When adding a new skill to this repo:

1. Choose the right bucket (standards/, workflow/, tools/)
2. Create a directory with the verb-noun name
3. Write SKILL.md following the anatomy above
4. Set `author` to yourself
5. Update cross-references in related skills
6. Update this CONTEXT.md and AGENTS.md if the bucket structure or conventions change

## External Skills Policy

Third-party skills are not stored in this repo. They are documented in [`EXTERNAL.md`](EXTERNAL.md) with:
- Source and author
- Installation instructions
- Purpose of each skill

For a fresh install, follow the instructions in `EXTERNAL.md`.

## Evolution History

- **2026-05**: SSOT-to-Skills restructuring. 24 skills created from SSOT + Matt Pocock + Emil Kowalski.
- **2026-06**: Full audit: 59 inconsistencies fixed, GLOSSARY.md expanded.
- **2026-07**: Separated external skills. Repo now contains only original skills. Third-party skills moved to EXTERNAL.md.

## Versioning

This repo does not use SemVer. Skills evolve independently. Breaking changes to a skill's interface are documented in the `version` field in frontmatter and in commit messages.

## Dependencies

- **OpenCode** (recommended): reads `.atl/skill-registry.md` for auto-loading
- **Any agent**: can load individual `SKILL.md` files by path
- **No runtime dependencies**: skills are pure markdown, no packages or build step
