# Tools Skills

Tool-specific skills for project initialization, template application, and model assignment.

## Skills

| Skill | Description |
|-------|-------------|
| [init-repo](./init-repo/SKILL.md) | Bootstrap a new SSOT project — detect agent, create CONTEXT.md, scaffold directory structure, configure branch protection, install templates. Run this first on any new project. |
| [apply-template](./apply-template/SKILL.md) | Reference and apply SSOT templates — PRD, RFD, ADR, Views, Issues, Milestones. Guide agent through each template section with purpose and guidance. |
| [sdd-model-assigner](./sdd-model-assigner/SKILL.md) | Discover free-tier models from opencode Zen, assign them to SDD phases and Judgment Day agents, handle quota-exhaustion swaps. |

## Conventions

- All skills follow the same structure: YAML frontmatter, philosophy, anti-patterns (≥3), workflow phases (≥3 with checklists), glossary, and chaining.
- Cross-references use bucket-relative paths: `standards/`, `workflow/`, `tools/`.
- Glossary terms link to `standards/GLOSSARY.md` for full definitions.
