# View: [Screen Name]

**Associated User Story:** [e.g., US-01]  
**Device Target:** [e.g., Desktop / Mobile Web / Native App]

---

## 1. AI Generation Prompt (The Master Prompt)
<!-- 
    INSTRUCTIONS: Feed this section into your AI UI generator.
    It includes instructions for "System Thinking" and "Graceful Degradation".
-->

**System Role:** Act as an expert UX/UI designer and frontend developer. Generate a high-fidelity, accessible UI component based on the following structural constraints.

**Design System & Styling:**
*   **Vibe/Theme:** [e.g., Minimalist, SaaS dashboard, high-contrast, modern, enterprise].
*   **Framework/Library:** [e.g., Tailwind CSS, Material UI, Shadcn, plain HTML/CSS].
*   **Color Palette:** [e.g., Primary blue, slate grays for backgrounds, semantic colors for alerts].

**Information Architecture (Layout Logic):**
*   **Container:** [e.g., Full-width screen with a sticky left sidebar and a top navigation bar].
*   **Header Area:** [e.g., Needs a search input (with a Cmd+K shortcut hint), a notification bell, and a user avatar dropdown].
*   **Sidebar Area:** [e.g., Vertical navigation menu with icons for 'Home', 'Projects', and 'Settings'].
*   **Main Content Area:** [e.g., A breadcrumb trail at the top. Below that, a data table with 4 columns: Name, Status, Date, Owner. Include a pagination control at the bottom].

**Strategic UX Directives:**
*   **System Transparency:** Provide a dedicated "System Thinking" state. If a process takes >2 seconds, show a progress indicator that explains *what* the system is doing (e.g., "Analyzing data...", "Generating response...").
*   **Graceful Degradation:** Design a "Fallback Mode." If the primary data source is unavailable, the component should display [e.g., cached data or a simplified functional version].
*   **Actionable Errors:** Error messages must never be just codes. They must explain the problem and provide a clear "Next Step" (e.g., a 'Retry' button or a link to support).

**Interactive Behavior:**
*   [e.g., The table rows should have hover states. The 'Status' column should use colored badge indicators].

---

## 2. Human-in-the-Loop (HITL) & Safety Logic
<!-- 
    Define the points where human judgment is required to ensure 
    safety or accuracy before the system proceeds.
-->

*   **Critical Action Gate:** [e.g., "Deleting a project requires the user to type the project name to confirm."]
*   **Approval Workflow:** [e.g., "The AI generates the report, but it remains in 'Draft' state until the user clicks 'Approve & Send'."]
*   **Transparency Check:** [e.g., "When the system makes an automated adjustment, provide a 'Why?' tooltip explaining the data behind the choice."]

---

## 3. Component Data Binding (RFD Link)
<!-- 
    Map the UI components generated above to the actual data contracts defined in the RFD.
    This prevents frontend developers from guessing where data comes from.
-->

| UI Component | Action / Event | Data Source (RFD Contract) |
| :--- | :--- | :--- |
| **Global Search** | `onChange` / `onSubmit` | `GET /api/v1/search?q={query}` |
| **Main Data Table** | `onLoad` | `GET /api/v1/resources` (Returns `ResourceList` schema) |
| **Create Button** | `onClick` (Opens Modal) | `POST /api/v1/resources` |

---

## 4. UI State Specifications
<!-- 
    The AI must generate (or the designer must account for) these specific states.
-->
- [ ] **Empty State:** If the API returns no data for the Main Content Area, show an illustration and a prompt to "Create your first record."
- [ ] **Loading State:** When fetching data, the Main Content Area should display skeleton row loaders. The Header and Sidebar remain interactive.
- [ ] **Error State:** If the API fails, display a non-intrusive, dismissible red toast notification at the top right.
- [ ] **Success State:** When the user completes the Primary Action, show a green confirmation badge.
- [ ] **Degraded State:** How the view looks when a non-critical dependency fails.

---

## 5. Visual Assets & Output
<!-- 
    Once the AI generates the UI and the team refines it in the design tool,
    link the final, approved asset here.
-->
*   **Approved Figma / Design Link:** `[Insert Link Here]`
*   *(Optional) Static Snapshot:* `![Screen Name Snapshot](./snapshot_US01.png)`
*   **Distributed Trace Link (Dev Only):** `[Link to APM trace for this view's initialization]`

---
*Maintenance Note: This specification is the contract for the Frontend implementation. It prioritizes human trust and system resilience.*
