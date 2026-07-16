# External Skills & Plugins Registry

Third-party skills and plugins installed in OpenCode. This document is the official inventory for backup and reinstallation.

---

## Gentle AI Suite

**Author**: gentleman-programming
**Source**: [Gentle AI](https://github.com/gentleman-programming/gentle-ai) — orchestration system for OpenCode.
**Installation**: Included in Gentle AI setup. Copied to `~/.config/opencode/skills/`.

### Workflow Skills (10)

| Skill | Description |
|-------|-------------|
| `go-testing` | Go testing patterns (Bubbletea, golden files, teatest) |
| `branch-pr` | PR creation with issue-first checks |
| `chained-pr` | Split oversized PRs (>400 lines) into chained PRs |
| `issue-creation` | Issue creation with pre-flight validation |
| `judgment-day` | Blind dual adversarial code review + surgical fix |
| `work-unit-commits` | Commits as reviewable work units for chained PRs |
| `cognitive-doc-design` | Docs designed to reduce cognitive load |
| `comment-writer` | Warm, direct collaboration comments for PRs, issues, reviews |
| `skill-creator` | Create LLM-first skills with valid frontmatter |
| `skill-improver` | Audit and upgrade existing skills |

### SDD Infrastructure (10)

Internal orchestration skills for Gentle AI. Sub-agents of the SDD (Spec-Driven Development) cycle.

| Skill | Role |
|-------|------|
| `sdd-init` | Bootstrap SDD context, detect testing capabilities, set up registry |
| `sdd-explore` | Investigate codebase before proposing changes |
| `sdd-propose` | Create change proposals (intent, scope, approach) |
| `sdd-spec` | Write delta specs with requirements and scenarios |
| `sdd-design` | Create technical design and architecture approach |
| `sdd-tasks` | Break changes into implementation tasks |
| `sdd-apply` | Implement tasks from specs and design |
| `sdd-verify` | Validate implementation against specs |
| `sdd-archive` | Archive completed changes with delta spec sync |
| `sdd-onboard` | Guided walkthrough of the SDD workflow |


---

## Matt Pocock Skills

**Author**: Matt Pocock
**Source**: [mattpocock/skills](https://github.com/mattpocock/skills)
**Installation**:
```bash
git clone https://github.com/mattpocock/skills /tmp/mattpocock-skills
cp -r /tmp/mattpocock-skills/diagnose/SKILL.md ~/.config/opencode/skills/debug-code/
cp -r /tmp/mattpocock-skills/zoom-out/SKILL.md ~/.config/opencode/skills/explain-code/
cp -r /tmp/mattpocock-skills/handoff/SKILL.md ~/.config/opencode/skills/pack-context/
cp -r /tmp/mattpocock-skills/grill-me/SKILL.md ~/.config/opencode/skills/spell-out/
```

Or from this repo's git history:
```bash
git checkout <commit-hash> -- tools/debug-code tools/explain-code tools/pack-context tools/spell-out
```

| Skill | Original name | Purpose |
|-------|---------------|---------|
| `debug-code` | diagnose | Disciplined bug-fixing loop: reproduce, minimize, hypothesize, instrument, fix, regression test |
| `explain-code` | zoom-out | Explain code in system context: architecture, modules, boundaries, dependencies |
| `pack-context` | handoff | Compact session context for handoff between sessions or developers |
| `spell-out` | grill-me | Iterative plan questioning: intent, scope, trade-offs, edge cases, dependencies |

---

## Emil Kowalski Skills

**Author**: Emil Kowalski
**Source**: [emilkowalski/skill](https://github.com/emilkowalski/skill) — emil-design-eng
**Installation**:
```bash
git clone https://github.com/emilkowalski/skill /tmp/emil-skill
cp -r /tmp/emil-skill/emil-design-eng/* ~/.config/opencode/skills/
```

| Skill | Purpose |
|-------|---------|
| `polish-ui` | UI polish principles: performance, transforms, hover states, accessibility |
| `build-components` | Component building principles, gestures, Sonner approach to DX |
| `design-animations` | Animation decision framework: spring physics, clip-path, staggers |

---

## Plugins

### ponytail

**Type**: OpenCode Plugin
**Path**: `~/.local/share/opencode-plugins/ponytail/.opencode/plugins/ponytail.mjs`
**Purpose**: Lazy senior developer mode — extreme YAGNI, stdlib first, minimal diffs.
**Installation**: Local plugin, no external setup required.

### video-use

**Type**: OpenCode Plugin + Skill
**Path**: `~/.local/share/opencode-plugins/video-use/` + `~/.config/opencode/skills/video-use/`
**Purpose**: Video editing by conversation: transcription, cuts, color grading, subtitles.

### manim-video

**Type**: Skill (sub-skill of `video-use/`)
**Path**: `~/.config/opencode/skills/video-use/skills/manim-video/`
**Purpose**: Mathematical and technical animation pipeline with Manim Community Edition.

---

## Other Installed Skills

| Skill | Type | Description |
|-------|------|-------------|
| `graphify` | Skill | Convert codebases into knowledge graphs with god nodes, community detection, and query/path/explain tools |
| `customize-opencode` | Built-in | Configure OpenCode itself (opencode.json, agents, skills, MCP) |

---

## Full Installation (fresh setup)

To reinstall everything from scratch:

```bash
# 1. Clone this repo
git clone git@github.com:joelabreurojas/skills.git

# 2. Copy original skills
cp -r skills/standards/* ~/.config/opencode/skills/
cp -r skills/workflow/* ~/.config/opencode/skills/
cp -r skills/tools/* ~/.config/opencode/skills/
cp -r skills/standards/GLOSSARY.md ~/.config/opencode/skills/standards/

# 3. Install Gentle AI (includes SDD + workflow skills)
#    Follow instructions at https://github.com/gentleman-programming/gentle-ai

# 4. Install Matt Pocock skills
git clone https://github.com/mattpocock/skills /tmp/matt-skills
cp /tmp/matt-skills/diagnose/SKILL.md ~/.config/opencode/skills/debug-code/
cp /tmp/matt-skills/zoom-out/SKILL.md ~/.config/opencode/skills/explain-code/
cp /tmp/matt-skills/handoff/SKILL.md ~/.config/opencode/skills/pack-context/
cp /tmp/matt-skills/grill-me/SKILL.md ~/.config/opencode/skills/spell-out/

# 5. Install Emil Kowalski skills
git clone https://github.com/emilkowalski/skill /tmp/emil-skill
cp /tmp/emil-skill/emil-design-eng/polish-ui/SKILL.md ~/.config/opencode/skills/polish-ui/
cp /tmp/emil-skill/emil-design-eng/build-components/SKILL.md ~/.config/opencode/skills/build-components/
cp /tmp/emil-skill/emil-design-eng/design-animations/SKILL.md ~/.config/opencode/skills/design-animations/

# 6. Refresh skill registry
gentle-ai skill-registry refresh --force
```
