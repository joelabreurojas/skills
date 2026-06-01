---
name: design-views
description: "Define visual design specifications as AI-ready master prompts. Component inventory, data contracts, interaction logic, and mandatory state specifications (empty, loading, error, success). Use when specifying UI screens, writing frontend requirements, creating design-to-code prompts, or defining visual interface contracts."
license: MIT
trigger: "/design-views, define UI, visual design, view specification, master prompt, component inventory, UI states, frontend spec, screen design, design-to-code, wireframe spec, UI requirements"
metadata:
  author: Joel Abreu Rojas
  version: "1.0.0"
  ssot_chapter: "10: Views Standards"
  porter_note: "View specs work with any UI generation tool (v0, Stitch, Figma AI) — adapt the master prompt format to your toolchain."
---

# Design Views

## Philosophy

Visual specs serve two audiences: **humans** (developers, designers, stakeholders) and **AI tools** (v0, Stitch, Figma AI). The spec must be structured enough for a machine to generate a first draft, and precise enough for a human to implement the final version.

We do not ask AI to "design a dashboard." We provide it with strict architectural constraints derived from the PRD and RFD. The view specification is a **master prompt** — a structured input that tells the AI exactly what components exist, what data drives them, and what interactions are possible. This is structure-first, not creativity-first.

Every view must account for **four states**: empty, loading, error, and success. AI tools are biased toward the happy path. As engineers, we explicitly demand designs for failure and latency. A view that only shows the success state is an incomplete specification that will produce incomplete UIs.

Traceability is enforced through naming. Every view file is named after the PRD user story it supports, creating a bi-directional link between business requirement and visual interface. If a view cannot be traced to a user story, it has no business existing.

---

## Anti-Patterns

### 1. Only Specifying the Happy Path

Writing a view specification that covers the success state but ignores empty, loading, and error states. This is the most common AI-generation failure: the tool produces a beautiful dashboard that crashes when the API is slow or returns an error.

**Fix**: Every view specification must include all four states: Initial/Empty, Loading, Error, Success. No exceptions. If a state is not applicable, document why.

### 2. AI Prompt Without Constraints

Feeding an AI tool a vague prompt like "design a user dashboard." The AI will produce something generic, unstructured, and disconnected from your data model. Without constraints, the output is decoration, not engineering.

**Fix**: The master prompt must include: component inventory (what zones exist), data contracts (what fields drive each zone), interaction logic (what happens on user actions), and state specifications (what happens when things fail).

### 3. No Traceability to PRD Stories

Creating view files that are not named after or linked to a PRD user story. Without traceability, you cannot justify why a screen exists, what business goal it serves, or whether it is in scope.

**Fix**: Name every view file `[UserStoryID]_[ScreenName]_[Device].md`. Include a hyperlink back to `docs/PRD/02_user_stories.md` in the file header.

### 4. Inventing Data Contracts

Defining API responses or data shapes in the view spec that do not exist in the RFD. The view spec references the RFD for data contracts — it does not invent them. Inventing data creates drift between what the UI expects and what the backend provides.

**Fix**: Every data reference in a view spec must link to the RFD interface section. If the needed data does not exist in the RFD, update the RFD first, then reference it.

### 5. Mixing Design System Details

Embedding brand-specific colors, typography, or spacing rules in the view spec. The view spec defines *structure* and *behavior*; the design system defines *visual identity*. Mixing them makes the spec fragile to brand changes and hard to reuse across products.

**Fix**: Reference the design system by name (e.g., "uses Design System color tokens"). Do not hardcode hex values or font sizes in the view spec.

---

## Workflow

### Phase 1 — Prepare the View Directory

Set up the visual spec structure:

```
docs/VIEWS/
├── README.md                    # Index of all view specifications
├── US01_dashboard_desktop.md    # View spec per user story
├── US02_user_login_mobile.md
└── US03_settings_desktop.md
```

- [ ] Create `docs/VIEWS/` directory
- [ ] Initialize `README.md` with an index table mapping each view to its PRD story
- [ ] Follow naming convention: `[UserStoryID]_[ScreenName]_[Device].md`
- [ ] Verify each user story from `02_user_stories.md` has a corresponding view file (or document why it does not need one)

### Phase 2 — Write the Master Prompt

For each view, write the master prompt section:

- [ ] **Screen Purpose**: One sentence — what this screen does for the user
- [ ] **PRD Traceability**: Link to the specific user story in `docs/PRD/02_user_stories.md`
- [ ] **Component Inventory**: List every functional zone (Global Search, Sidebar, Data Grid, Action Bar, etc.)
- [ ] **Data Contracts**: What backend data drives this view? Link to RFD interface section for payload shapes
- [ ] **Interaction Logic**: What happens when the user clicks the primary action? (e.g., "Triggers POST /api/orders, shows success toast, redirects to order detail")
- [ ] **Responsive Behavior**: How the layout adapts across breakpoints (if applicable)

**Master prompt template**:

```markdown
# [Screen Name]

**Purpose**: [One sentence]
**PRD Story**: [Link to user story]
**Device**: [Desktop / Mobile / Both]

## Component Inventory
| Zone | Description | Data Source |
|------|-------------|-------------|
| [Zone 1] | [What it shows] | [RFD link] |

## Interaction Logic
- [Action] → [System response] → [User feedback]

## States
[See Phase 3]
```

### Phase 3 — Specify All Four States

For every view, explicitly define all four states:

**Initial/Empty State:**
- [ ] What the user sees before any data is available
- [ ] Call-to-action to populate the view (e.g., "Upload your first document")
- [ ] Illustration or icon that communicates the empty state meaning

**Loading State:**
- [ ] How progress is communicated (skeleton loaders, spinners, progress bars)
- [ ] Skeleton layout matches the success state structure (not generic spinners)
- [ ] Timeout behavior — what happens if loading exceeds threshold

**Error State:**
- [ ] How validation failures are displayed (inline red text, field-level messages)
- [ ] How system errors are displayed (global alert banners, error pages)
- [ ] Retry mechanism — can the user recover without a full page reload?

**Success State:**
- [ ] Confirmation of completed actions (toast, inline message, redirect)
- [ ] Updated data displayed after mutation
- [ ] Next-action guidance (what should the user do next?)

**Quality check**: Can you mentally walk through the view with an empty database, a slow network, a failed API call, and a successful flow? If any scenario has no spec, write it.

### Phase 4 — Validate Against RFD

Verify the view spec aligns with the technical design:

- [ ] Every data field referenced exists in the RFD data model
- [ ] Every API call referenced exists in the RFD interface section
- [ ] Every interaction maps to a valid backend operation
- [ ] No invented data contracts — all shapes come from the RFD
- [ ] Error states align with the RFD error envelope format

**Cross-check**: If the view needs data that the RFD does not provide, update the RFD first, then update the view spec.

**Common drift scenarios**:
| View Needs | RFD Missing | Action |
|-----------|-------------|--------|
| User avatar URL | User entity has no `avatar_url` field | Add field to RFD data model |
| Real-time notifications | No WebSocket endpoint in RFD interface | Add notification channel to RFD |
| Bulk select action | No bulk mutation endpoint | Add batch endpoint to RFD interface |

### Phase 5 — Generate AI-Ready Prompt (Optional)

If using AI-driven UI generation:

- [ ] Combine the master prompt with the PRD user story text
- [ ] Add explicit constraints: "Include empty, loading, error, and success states"
- [ ] Add data context: "Data shape is [link to RFD contract]"
- [ ] Feed the combined prompt to the generation tool
- [ ] Review generated output against the state specifications
- [ ] Export to design system for brand alignment (colors, typography, spacing)
- [ ] Commit the permanent link (Figma, screenshot, or static export) back to the view spec file

**AI prompt structure**:
```
1. PRD User Story text (the "why")
2. Component Inventory (the "what zones exist")
3. Data Contracts (the "what fields drive each zone")
4. Interaction Logic (the "what happens on click")
5. State Specifications (the "what happens when things fail")
6. Constraints: "Include all four states. Follow [Design System] tokens."
```

**Quality gate**: After AI generation, walk through each of the four states mentally. Does the generated UI handle empty data? Loading delays? API errors? If any state is missing, refine the prompt and regenerate.

---

## Glossary

| Term | Definition | Full Reference |
|------|-----------|---------------|
| [Master Prompt](standards/GLOSSARY.md#master-prompt) | Structured markdown specification that serves as both human documentation and AI generation input. | View specification |
| [Component Inventory](standards/GLOSSARY.md#component-inventory) | Strict list of functional zones in a view (e.g., Global Search, Sidebar, Data Grid). | UI structure |
| [Data Contract](standards/GLOSSARY.md#data-contract) | Shared type definition for data flowing between backend and frontend. Defined in RFD. | Interface specification |
| [Traceability](standards/GLOSSARY.md#traceability) | Bi-directional link between view files and PRD user stories. Enforced via naming convention. | Requirements linking |
| [State Specification](standards/GLOSSARY.md#state-specification) | Explicit definition of empty, loading, error, and success states for every view. | UI completeness |
| [PRD](standards/GLOSSARY.md#prd-product-requirements-document) | Product requirements document — the source of truth for what the system does. | Product documentation |
| [RFD](standards/GLOSSARY.md#rfd-request-for-design) | Technical design document — the source of truth for how the system works. | Architecture documentation |

---

## Chaining

**Upstream**:
- `workflow/define-product` — User stories drive which views are needed and their purpose
- `workflow/specify-solution` — RFD data contracts and interfaces define what data drives each view
- `standards/design-api` — API contracts inform the data shapes displayed in views

**Downstream**:
- `workflow/plan-issues` — View specifications break into frontend implementation tasks

**Cross-cutting**: View specs are living documents. When the RFD data model changes, update the view specs that reference it. When a PRD story changes, update the linked view spec. The status flip (Proposed → Accepted) applies here too.

**Handoff**: After Phase 5, pass the view specs to `plan-issues` for frontend task breakdown. The issue author reads both the RFD and VIEWS as inputs.
