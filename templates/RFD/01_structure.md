# Structure & Dependencies

## 1. Architectural Pattern
<!-- 
    What is the high-level software architecture pattern chosen for this project? 
    (e.g., Standard MVC, Hexagonal / Ports & Adapters, Modular Monolith, Layered Architecture).
    Briefly explain WHY this pattern was chosen over others for this specific product.
-->
[Insert Pattern and Rationale Here]

---

## 2. Directory Tree
<!-- 
    Provide an ASCII tree representation of the proposed top-level directory structure. 
    This serves as a map for where different types of code should live.
-->
```text
project_root/
├── [folder_1]/      # [Brief description of what goes here]
├── [folder_2]/      # [Brief description of what goes here]
└── [folder_3]/      # [Brief description of what goes here]
```

---

## 3. Core Module Boundaries & Rules
<!-- 
    Define how the codebase is separated. Is it separated by technical layer 
    (e.g., controllers, services, models) or by business domain (e.g., auth, billing, inventory)?
    
    CRITICAL: What are the strict dependency rules? (e.g., "The Domain layer must never 
    import from the Infrastructure layer" or "Modules can only communicate via Interfaces").
-->

### Module/Layer A: [Name]
*   **Responsibility:** [What is the sole purpose of this area of the code?]
*   **Allowed Imports:** [What other modules is this module allowed to know about?]

### Module/Layer B: [Name]
*   **Responsibility:** [Description]
*   **Allowed Imports:** [Description]

---

## 4. Key Internal Dependencies
<!-- 
    Do not list every minor utility package. List the major frameworks, 
    libraries, or SDKs that will fundamentally shape the internal architecture.
-->

| Dependency | Purpose / Role | Rationale |
| :--- | :--- | :--- |
| **[Framework Name]** | Core application framework | [Why this over alternatives?] |
| **[Tool Name]** | [e.g., Data Access Layer / ORM] | [Why this specific tool?] |
| **[Library Name]** | [e.g., State Management / Validation] | [Required for feature X] |

---

## 5. Third-Party Integrations
<!-- 
    List the external services, APIs, or platforms this system relies on to function.
    Consider authentication providers, payment gateways, email services, etc.
-->

| Integration / Service | Purpose | Integration Method | Potential Risks / Rate Limits |
| :--- | :--- | :--- | :--- |
| **[External Service A]** | [e.g., Payment Processing] | [e.g., Official SDK / REST API] | [e.g., Webhook handling required; API limits] |
| **[External Service B]** | [e.g., Identity Provider] | [e.g., OAuth2 / OIDC] | [e.g., Vendor lock-in risk; uptime dependency] |
| **[External Service C]** | [e.g., Transactional Emails] | [e.g., SMTP / API] | [e.g., Delivery rate limits; domain verification] |
