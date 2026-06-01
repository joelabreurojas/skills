# UI Skills

User interface patterns, animation decisions, component implementation, and design system conventions. These skills cover the "how to build the interface" — from animation philosophy to component architecture.

## Skills

| Skill | Description |
|-------|-------------|
| [design-animations](./design-animations/SKILL.md) | Animation decision framework, spring physics, clip-path techniques, stagger effects, and asymmetric timing for purposeful UI animation. |
| [build-components](./build-components/SKILL.md) | Component building principles, gesture interactions, the Sonner approach to developer experience, and the Review Format for component reviews. |

## Relationships

The UI bucket works closely with `standards/polish-ui` (performance, accessibility, CSS transforms, debugging checklist) to provide complete coverage of Emil Kowalski's design engineering philosophy:

1. **Polish** (`standards/polish-ui`) — Performance rules, CSS transform mastery, accessibility, debugging
2. **Design** (`design-animations`) — When and how to animate, easing choices, spring physics, clip-path
3. **Build** (`build-components`) — Component implementation patterns, gesture interactions, developer experience

The three skills should be used together: polish-ui for the rules, design-animations for the decisions, build-components for the implementation.

## Conventions

- All skills follow the same structure: YAML frontmatter, philosophy, anti-patterns (≥3), workflow phases (≥3 with checklists), glossary, and chaining.
- Cross-references use bucket-relative paths: `standards/`, `workflow/`, `ui/`, `tools/`.
- Skills derived from external sources include `derived_from` in the YAML metadata.
