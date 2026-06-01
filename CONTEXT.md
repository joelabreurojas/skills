# Skills Repository — Project Context

## Purpose

A curated collection of composable, LLM-first agent skills for professional software engineering. Built primarily from the **SSOT (Single Source of Truth)** engineering framework (19 chapters), with additions adapted from Matt Pocock's engineering skills and Emil Kowalski's design engineering philosophy. Designed for any coding agent — OpenCode, Claude Code, Cursor, GitHub Copilot.

This repo is the central hub for all my agent skills. It started with SSOT as the backbone and will grow with original skills over time.

## Current State

**Stable.** All 24 skills are authored, cross-referenced, and verified. Open to new skills as needs arise.

- 24 skills across 4 buckets
- 16 template files across 5 document types (SSOT-originated)
- Shared glossary in `standards/GLOSSARY.md` (20+ terms)
- Skill registry at `.atl/skill-registry.md` for OpenCode auto-loading

## Repository Structure

```
skills/
├── .atl/                    # OpenCode skill registry (auto-loaded)
├── standards/               # 5 skills — quality gates, principles
│   ├── GLOSSARY.md          # Shared terminology index
│   ├── apply-principles/
│   ├── design-api/
│   ├── shift-left-security/
│   ├── write-tests/
│   └── polish-ui/
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
├── ui/                      # 2 skills — design engineering
│   ├── design-animations/
│   └── build-components/
├── tools/                   # 6 skills — transversals
│   ├── spell-out/
│   ├── debug-code/
│   ├── explain-code/
│   ├── pack-context/
│   ├── init-repo/
│   └── apply-template/
├── templates/               # 16 master template files
│   ├── PRD/                 # 5 — Product Requirements Documents
│   ├── RFD/                 # 6 — Request for Design (technical specs)
│   ├── ADR/                 # 1 — Architecture Decision Records
│   ├── VIEWS/               # 2 — Visual design specifications
│   └── milestones/          # 2 — Milestone blueprints and issue tasks
├── AGENTS.md                # Agent instructions for this repo
├── CONTEXT.md               # This file
└── README.md                # Human-facing index
```

## Origins

The skills come from three sources, plus original work:

| Source | Author | Skills | Attribution |
|--------|--------|--------|-------------|
| **SSOT framework** | Joel Abreu Rojas | 17 across standards/, workflow/, tools/ | Primary reference; full lifecycle engineering |
| **Matt Pocock** | Matt Pocock | 4 in tools/ | Adapted: debug-code, explain-code, pack-context, spell-out |
| **Emil Kowalski** | Emil Kowalski | 3 in ui/ + standards/ | Adapted: design-animations, build-components, polish-ui |

Each adapted skill has a `derived_from` field in its frontmatter linking back to the original work. Skills I author from scratch carry only my name.

## Lifecycle Flow

The 16 standards + workflow skills implement a complete delivery pipeline:

```
/spell-out → /define-product → /plan-roadmap → /specify-solution
→ /design-views → /define-milestone → /plan-issues
→ /enforce-branch-flow → /write-commits → /log-adr
→ /create-pr → /apply-principles → /shift-left-security
→ /polish-ui → /write-tests → /ship-release
```

## Key Conventions

### Naming

- **Verb-noun**: `define-product`, `write-commits`, `debug-code`
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

1. Choose the right bucket (standards/, workflow/, ui/, tools/)
2. Create a directory with the verb-noun name
3. Write SKILL.md following the anatomy above
4. Set `author` to yourself if it's original work, or use `derived_from` if adapted
5. Update cross-references in related skills
6. Update this CONTEXT.md and AGENTS.md if the bucket structure or conventions change
7. The `.atl/skill-registry.md` auto-loads for OpenCode — no manual registry update needed

## Evolution History

This repository was restructured from a flat `engineering/` directory to the 4-bucket structure. The original SSOT chapters (19) were mapped to command-like skills:

- 14 SSOT chapters → workflow + standards skills
- 5 new skills authored: `plan-roadmap`, `define-milestone`, `polish-ui`, `spell-out`, `pack-context`
- 3 Emil Kowalski design engineering skills: `design-animations`, `build-components` (plus `polish-ui`)
- 2 merged from gentle-ai: `write-commits` (SDD integration), `create-pr` (CI checks)
- 4 adapted from Matt Pocock: `debug-code`, `explain-code`, `pack-context`, `spell-out`
- 6 tools skills for transversal concerns

## Versioning

This repo does not use SemVer. Skills evolve independently. Breaking changes to a skill's interface (required inputs, workflow phases) are documented in the skill's frontmatter `version` field and noted in commit messages.

## Dependencies

- **OpenCode** (recommended): reads `.atl/skill-registry.md` for auto-loading
- **Any agent**: load individual `SKILL.md` files by path
- **No runtime dependencies**: skills are pure markdown, no packages or build step
