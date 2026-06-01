# ADR [Number]: [Short, Descriptive Title]

<!-- 
  INSTRUCTIONS:
  1. Copy this template to docs/ADR/.
  2. Name the file sequentially (e.g., 004_use_postgresql_over_sqlite.md).
  3. Status Flip: Set to 'Proposed' in the branch. Change to 'Accepted' 
     only after review approval and BEFORE merging to main.
-->

*   **Status:** [Proposed | Accepted | Superseded by ADR-XXX]
*   **Date:** [YYYY-MM-DD]
*   **Author(s):** [Name / GitHub Handle]

---

## 1. Context and Problem Statement
<!-- 
    Describe the specific technical problem or requirement that forced 
    this decision. What is the issue we're trying to solve? 
-->
[Insert context here]

---

## 2. Considered Options
<!-- 
    List the different technologies, patterns, or approaches that were 
    considered. Always include the "Do Nothing" option if applicable.
-->
*   **Option 1:** [Description]
*   **Option 2:** [Description]
*   **Option 3:** [Description]

---

## 3. Decision Outcome
<!-- 
    State the chosen option clearly and affirmatively.
-->
**Chosen Option:** [Option X]

### Rationale
<!-- 
    Justify the choice. Why is this better than the alternatives? 
    Reference performance, cost, team expertise, or project principles.
-->
[Insert rationale here]

---

## 4. Consequences
<!-- 
    What happens now that we've made this decision? 
    Be honest about the trade-offs.
-->

### ✅ Positive Consequences
*   [e.g., Immediate resolution of concurrent write errors.]
*   [e.g., Improved developer productivity due to better tooling.]

### ⚠️ Negative Consequences
*   [e.g., Increased operational complexity.]
*   [e.g., We have accepted technical debt in area X to meet the deadline.]

### ⚙️ Implementation Details
*   [e.g., We need to install library Y and update the config file.]

---
*Note: Once merged into main, this record is immutable. If the decision changes later, a new ADR must be authored.*
