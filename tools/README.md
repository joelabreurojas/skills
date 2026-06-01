# Tools Skills

Tool-specific skills for project initialization, debugging, code explanation, context management, plan validation, and template application. These skills support the entire SSOT workflow — from bootstrapping a new project to handing off context between sessions.

## Skills

| Skill | Description |
|-------|-------------|
| [init-repo](./init-repo/SKILL.md) | Bootstrap a new SSOT project — detect agent, create CONTEXT.md, scaffold directory structure, configure branch protection, install templates. Run this first on any new project. |
| [spell-out](./spell-out/SKILL.md) | Grill a plan before building — iterative questioning about intent, scope, trade-offs, edge cases, and dependencies. Design-First means question before build. |
| [apply-template](./apply-template/SKILL.md) | Reference and apply SSOT templates — PRD, RFD, ADR, Views, Issues, Milestones. Guide agent through each template section with purpose and guidance. |
| [debug-code](./debug-code/SKILL.md) | Disciplined bug-fixing loop: reproduce, minimize, hypothesize, instrument, fix, regression test. Every bug is a system failure, not a personal failure. |
| [explain-code](./explain-code/SKILL.md) | Explain code in system context — zoom out to architecture, show boundaries and data flow, reveal design constraints. Zoom out before zooming in. |
| [pack-context](./pack-context/SKILL.md) | Compact session context for handoff — capture what was done, what remains, decisions made, and gotchas found. Context is the most expensive thing to rebuild. |

## Relationships

The tools bucket follows a natural progression:

1. **Initialize** (`init-repo`) — Scaffolds the project and makes SSOT possible
2. **Validate** (`spell-out`) — Questions the plan before building, catches risk early
3. **Template** (`apply-template`) — Guides creation of structured documents
4. **Debug** (`debug-code`) — Fixes bugs when things go wrong
5. **Explain** (`explain-code`) — Helps others understand the codebase
6. **Handoff** (`pack-context`) — Preserves context between sessions and people

## Conventions

- All skills follow the same structure: YAML frontmatter, philosophy, anti-patterns (≥3), workflow phases (≥3 with checklists), glossary, and chaining.
- Cross-references use bucket-relative paths: `standards/`, `workflow/`, `ui/`, `tools/`.
- Glossary terms link to `standards/GLOSSARY.md` for full definitions.
