---
name: explain-code
description: "Explain code in system context — zoom out to architecture, show how the code fits the bigger picture, identify modules, boundaries, and dependencies. Use when onboarding, code review, architecture discussion, or documenting complex logic."
license: MIT
trigger: "/explain-code, explain code, explain, code explanation, architecture explanation, system context, onboarding, code review explanation, how it works"
metadata:
  author: Matt Pocock
  version: "1.0.0"
  derived_from: "mattpocock/skills — zoom-out"
  ssot_chapter: "02: Architecture Communication"
  porter_note: "The goal is understanding, not description. Line-by-line reading is the lowest-value way to understand code."
---

# Explain Code

## Philosophy

### Zoom out before zooming in

Code is the most detailed representation of a system, but it is also the least useful for understanding the system. A line of code tells you *what* happens. The architecture tells you *why* it happens there, in that way, at that time.

Before explaining any specific code, establish the context: what module does this belong to? What boundary does it sit on? What problem does this module solve for the system as a whole? The code is the answer — the question is the architecture.

Think of it like a city map. If someone asks "what does this traffic light do?" you do not explain the wiring inside the traffic light. You explain the intersection, the traffic patterns, and why the light changes when it does. The traffic light makes no sense without the intersection. Code makes no sense without the architecture.

### Architecture is about boundaries

A well-architected system is a set of modules separated by clear boundaries, each with a defined responsibility. The boundaries matter more than the implementation inside them. If the boundaries are wrong, the implementation quality does not matter — the system will be hard to change, test, and reason about.

When explaining code, identify the boundaries first: what does this module own? What does it not own? What does it depend on? What depends on it? A module with unclear boundaries will be confusing regardless of how well the individual functions are written.

### Understanding enables ownership

The goal of code explanation is not just "now you know what this does." It is "now you could change it safely." An explanation that does not enable the listener to modify the code independently has failed.

This means covering not just the happy path, but the failure modes, the edge cases, and the design constraints that shape the code. The listener should leave knowing what to touch, what not to touch, and who to ask if they are unsure.

### Different audiences need different levels

A new team member needs system context and module boundaries. A reviewer needs data flow and design reasoning. A maintainer needs constraint documentation and failure modes.

Match the explanation depth to the audience. A 30-second system overview serves the new joiner. A 5-minute deep dive on data flow serves the reviewer. A written constraint note serves every future reader.

---

## Anti-Patterns

### 1. Line-by-Line Reading

Walking through code line by line from top to bottom. This is the lowest-bandwidth way to understand code. It buries the listener in implementation details before establishing the conceptual model.

**Fix**: Start with the architecture (what and why). Then zoom into the module boundaries and data flow. Only then approach specific lines. The listener should be able to predict what each section does before reading it.

### 2. Ignoring the Architecture

Explaining a function without showing where it fits in the module, or a module without showing where it fits in the system. Code written without architectural context reads like unrelated statements.

**Fix**: Always start with a system-level view. Even for a single function, explain: this function is part of [module], which is responsible for [purpose], and is called by [caller] to achieve [outcome].

### 3. No Context for Decisions

Explaining what the code does without explaining why it does it that way. Every non-obvious line has a history — a bug that was fixed, a performance constraint, a compatibility requirement. Without that history, the code looks arbitrary.

**Fix**: When you encounter a non-obvious line, explain the constraint that produced it: "This is here because [reason]. If [condition] changes, this could be simplified."

---

## Workflow

### Phase 1 — Establish System Context

Before touching specific code, build a mental model of the system:

- [ ] What is the system's purpose? One sentence.
- [ ] What are the major modules/containers? (3-5 boxes max — use C4 Level 2 if needed)
- [ ] How does data flow through the system? (request → auth → validation → business logic → storage → response)
- [ ] Where does the specific code being explained live in this flow?
- [ ] What external dependencies does the system have? (databases, APIs, services, libraries)

**Output**: A verbal or written architecture sketch — even three sentences establishing context is better than none. If the explanation is written, include a brief architecture diagram (ASCII or Mermaid).

### Phase 2 — Identify Module Boundaries

Within the relevant module, define its scope:

- [ ] What is this module responsible for? (its single reason to exist)
- [ ] What is explicitly NOT in this module's responsibility? (prevents scope creep)
- [ ] What are its public interfaces? (exports, APIs, events)
- [ ] What are its dependencies? (imports, injected services, called modules)
- [ ] What is the dependency direction? Does this module depend on others, or do others depend on it?

**Heuristic**: A well-bounded module can be explained in 2-3 sentences. If it takes more, the module may be doing too much.

### Phase 3 — Trace a Data Flow Path

Walk through ONE specific path through the code:

- [ ] Start at the entry point (route handler, event listener, function call)
- [ ] Trace the data: input → transformation → storage/response
- [ ] At each step, note: what is the input, what is the output, what side effects occur?
- [ ] Identify where decisions are made: conditionals, switches, error handling
- [ ] Note any non-obvious behavior and explain WHY it exists

**Tracing technique**: Draw the data flow as a sequence diagram or flowchart:

```
Input → [Middleware: auth] → [Handler: validate] → [Service: process] → [Store: persist] → Response
                ↓                   ↓                    ↓                   ↓
           Reject if       Check business         Apply business       Write to DB
           no token        rules                  logic                or cache
```

Each box is a module boundary. Each arrow is a dependency direction. Show the listener this map before showing them a single line of code. They will be able to predict what each file does before opening it.

**Rule**: Trace ONE path at a time. Do not branch into every possible execution path. The goal is understanding the primary flow, not enumerating every edge case. After the primary path is understood, THEN trace one alternative path (error case, empty state, boundary condition).

### Phase 4 — Explain the Design Constraints

For every non-obvious section, answer these questions:

- [ ] Why does this exist? What requirement or bug produced it?
- [ ] Why is it written this way and not another way? What trade-off was made?
- [ ] What would break if this code was removed or changed?
- [ ] What assumptions does this code make about its inputs, environment, or dependencies?
- [ ] Are there any known limitations or future improvements planned?

**Document these explanations as comments in the code** when the constraint is subtle and the answer is non-trivial. A future developer should not need to explain the code to understand why a line exists.

### Phase 5 — Document Non-Obvious Constraints

When the explanation reveals knowledge that is not visible in the code:

- [ ] **Write a comment for the next developer**: If a non-obvious constraint exists, the code should explain why. "This function does X instead of Y because [reason]. If [condition] changes, this can be simplified."
- [ ] **Create or update an ADR**: If the constraint is architectural (why we chose Postgres over MySQL, why we use server-side sessions), it belongs in an ADR, not a code comment.
- [ ] **Link to external context**: If the explanation depends on an external document (API spec, compliance requirement, business rule), document the link so it is findable.

**Checklist for what to document**:
- [ ] Why was this approach chosen over alternatives?
- [ ] What bug or incident led to this code existing?
- [ ] What assumption does this code make about its inputs, environment, or dependencies?
- [ ] What would break if this code was removed?
- [ ] Is there a simpler approach that was considered and rejected? Why?

### Phase 6 — Confirm Understanding

- [ ] Ask the listener: "Can you describe what happens when [specific input] enters this function?"
- [ ] Ask: "If you needed to change [behavior X], what file would you edit and what would you change?"
- [ ] Ask: "What do you think would be the hardest thing to change in this module?"
- [ ] Ask: "What surprised you about this code?"
- [ ] Document the explanation if it reveals architectural knowledge that is not written anywhere

**If the listener cannot answer confidently**: The explanation was not sufficient. Try a different approach — draw a diagram, walk through a specific debug session, or show how a test exercises the code. Different people understand code through different representations.

### Phase 7 — Levels of Explanation

Match the depth to the audience and purpose:

**Level 1: System Context (30 seconds)**
- What the system does and who uses it
- Where this code lives in the system
- One sentence about what this module is responsible for

**Level 2: Module Boundaries (2 minutes)**
- What this module owns and what it delegates
- Its dependencies and dependents
- The key files and their roles

**Level 3: Data Flow (5 minutes)**
- Trace one complete path through the module
- Identify decision points and data transformations
- Show error handling and edge cases along that path

**Level 4: Deep Dive (15+ minutes)**
- Full exploration of all paths
- Design history and trade-off reasoning
- Performance characteristics and bottlenecks
- Testing strategy and coverage gaps

Start at Level 1. Only go deeper when the listener needs it. Most explanations fail because they start at Level 3 or 4 when the listener needs Level 1 or 2.

---

## Glossary

| Term | Definition | Source |
|------|-----------|--------|
| C4 Model | Hierarchical visualization: Context (system), Containers (services), Components (modules), Code (details). | Architecture |
| Module Boundary | The explicit scope of a module's responsibility and its interfaces with other modules. | Architecture |
| Constraint | A non-obvious restriction that shaped the code — a bug fix, performance requirement, or compatibility need. | Design |
| Dependency Direction | Which module depends on which. Dependencies should flow toward stable, low-level modules (Dependency Inversion Principle). | Architecture |

---

## Chaining

**Upstream**:
- `workflow/specify-solution` — The RFD documents the design decisions that produced the code
- `workflow/log-adr` — ADRs capture the trade-offs and constraints that code explanation reveals

**Downstream**:
- `tools/pack-context` — Code explanations are essential context for handoffs between developers
- `tools/debug-code` — Understanding code boundaries and data flow is prerequisite for effective debugging
- `standards/apply-principles` — Architecture communication principles ground every explanation

**Cross-cutting**: Explaining code is a universal skill — applies to code review, onboarding, pair programming, and documentation. Every code owner should be able to explain their module at three levels: system context (30 seconds), module boundaries (2 minutes), and data flow (5 minutes).
