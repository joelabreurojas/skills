# Workflow Skills

Process-oriented skills for product definition, technical design, implementation planning, and delivery. These skills cover the "how to do it" — from roadmap strategy to release publishing.

## Skills

### Design Phase

| Skill | Description |
|-------|-------------|
| [plan-roadmap](./plan-roadmap/SKILL.md) | Strategic Now/Next/Later sequencing with JIT design gates. Bridge between product vision and execution. |
| [define-product](./define-product/SKILL.md) | Create or update a Product Requirements Document (PRD) with modular folder structure for vision, stories, and requirements. |
| [specify-solution](./specify-solution/SKILL.md) | Write Request for Design (RFD) technical specifications with Mermaid diagrams, data models, API contracts, and operations planning. |
| [design-views](./design-views/SKILL.md) | Define visual design specifications as AI-ready master prompts with component inventory and four mandatory states. |
| [define-milestone](./define-milestone/SKILL.md) | Define domain-based epics with Definition of Done and vertical slice progression across all architectural layers. |
| [plan-issues](./plan-issues/SKILL.md) | Break designs into executable issues with execution order, dependency mapping, and testable acceptance criteria. |

### Execution Phase

| Skill | Description |
|-------|-------------|
| [enforce-branch-flow](./enforce-branch-flow/SKILL.md) | Branch naming, protection rules, and one-issue-per-branch discipline. |
| [write-commits](./write-commits/SKILL.md) | Conventional Commits format, atomic commit discipline, and message quality. |
| [log-adr](./log-adr/SKILL.md) | Architecture Decision Records capturing significant technical decisions with context and consequences. |
| [create-pr](./create-pr/SKILL.md) | Mandatory PR structure, self-review checklist, test instructions, and merge ceremony. |
| [ship-release](./ship-release/SKILL.md) | Semantic versioning, CHANGELOG management, GitHub Releases, and CI gating. |

## Relationships

The workflow bucket follows the SSOT phased delivery model:

1. **Roadmap** (`plan-roadmap`) sequences what to build and when
2. **Product Definition** (`define-product`) defines what and why
3. **Technical Design** (`specify-solution`) defines how
4. **Visual Design** (`design-views`) defines the interface
5. **Milestones** (`define-milestone`) groups work into delivery units
6. **Issues** (`plan-issues`) breaks work into executable tasks
7. **Execution** (`enforce-branch-flow` → `write-commits` → `create-pr` → `ship-release`) delivers the work

## Conventions

- All skills follow the same structure: YAML frontmatter, philosophy, anti-patterns (≥3), workflow phases (≥3 with checklists), glossary, and chaining.
- Cross-references use bucket-relative paths: `standards/`, `workflow/`, `tools/`.
- Glossary terms link to `standards/GLOSSARY.md` for full definitions.
- Skills may be loaded independently or chained together via their `## Chaining` sections.
