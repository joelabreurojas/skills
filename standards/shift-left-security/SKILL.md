---
name: shift-left-security
description: "Shift-left security across the entire development lifecycle. Principle of Least Privilege, secret hygiene, OWASP Top 10, dependency auditing, supply chain security, contributor trust, and CI/CD security integration. Use when adding dependencies, configuring CI, handling secrets, reviewing security posture, setting up cloud deployments, or onboarding new repositories."
license: MIT
trigger: "/shift-left-security, add dependency, configure CI, handle secrets, security review, cloud deploy, OIDC, Dependabot, CodeQL, SAST, .gitignore, branch protection, CODEOWNERS"
metadata:
  author: Joel Abreu Rojas
  version: "1.0.0"
  ssot_chapter: "05: Security Standards & DevSecOps"
---

# Shift-Left Security

## Philosophy

Security is not a feature — it is a fundamental requirement. It is present from day one, not bolted on before launch. Every line of code, every dependency, every deployment carries security implications.

We operate under a **Shift-Left** philosophy: security checks and reviews happen as early in the development lifecycle as possible. The earlier a vulnerability is caught, the cheaper it is to fix and the less damage it can do. A vulnerability found in design costs 10x less than one found in production.

Modern applications rely heavily on open-source libraries. An attacker does not need to hack your code if they can hack your dependencies. Supply chain security is not optional — it is a core engineering discipline. Your dependency tree is an attack surface.

Every user, service, and infrastructure component must operate under the Principle of Least Privilege. Excessive permissions are a vulnerability waiting to be exploited. When in doubt, grant less. Revoking access is easier than recovering from a breach.

**Shift-left in practice**: Every phase below moves security earlier — from design through deployment. The goal is to catch vulnerabilities before they become dependencies, before they become commits, and before they reach production.

---

## Anti-Patterns

### 1. Committing `.env` or Secrets to Version Control
The cardinal sin of development. `.env` files, API keys, passwords, certificates — none of these belong in a Git repository. Even in private repos, even in early prototypes. Secrets in version control are secrets that will eventually be exposed.

**Why it happens**: Convenience. "It's just a prototype." "It's a private repo." These are lies that lead to breaches. Use `.gitignore` from the first commit.

### 2. Using Root/Admin Database Roles for Applications
Connecting to the database as `root`, `postgres`, or `admin`. If the application is compromised, the attacker has full database access. Application services must connect using roles restricted to specific tables and operations.

**Fix**: Create application-specific database roles with `SELECT`, `INSERT`, `UPDATE`, `DELETE` on specific tables only. Never grant `CREATE`, `DROP`, or `ALTER` to application roles.

### 3. Skipping OIDC for Cloud Deployments
Using long-lived, static access keys for CI/CD pipelines deploying to cloud providers. Static keys are a security liability — they persist, they can be leaked, and they grant broad access. OIDC generates temporary, scope-limited credentials that exist only during the pipeline run.

**Fix**: Configure OIDC trust relationships between your CI provider (GitHub Actions) and cloud provider (AWS, Azure, GCP). Use `aws-actions/configure-aws-credentials` with `role-to-assume` instead of `aws-access-key-id`.

### 4. Ignoring Dependency Vulnerabilities
Not monitoring or updating dependencies with known vulnerabilities. Attackers actively scan for outdated libraries with public CVEs. Dependabot/Renovate must be active and PRs must be reviewed promptly.

**Enforcement**: Dependabot PRs must be reviewed and merged within 7 days for critical vulnerabilities, 30 days for high severity.

### 5. Exposing Stack Traces in Production
Returning internal error details, database errors, or stack traces to clients. This leaks implementation details to attackers and creates information disclosure vulnerabilities. Always use a standardized error envelope.

**Fix**: Catch all unhandled exceptions. Return a generic error message with a `trace_id` for correlation. Log the full stack trace server-side only.

### 6. No Branch Protection on Critical Repositories
Repositories without branch protection rules allow direct commits to `main`, force pushes, and unreviewed changes. This bypasses all CI checks and peer review.

**Fix**: Enable branch protection on `main`: require PRs, require reviews, require CI to pass, disable force pushes.

---

## Workflow

### Phase 1 — Apply Principle of Least Privilege (PoLP)

Audit permissions for every component that accesses resources:

- [ ] **Database**: Application connects with a role restricted to specific tables (e.g., `app_read_write`), never `admin` or `postgres`
- [ ] **Cloud IAM**: Roles explicitly define allowed actions (e.g., `s3:PutObject` on a specific bucket, not `s3:*` globally)
- [ ] **Team Access**: Developers get production access only when actively debugging an incident; access is revoked immediately after
- [ ] **Service Accounts**: Each service has its own credentials with minimal required permissions
- [ ] **API Keys**: Scoped to specific endpoints and operations, not global access
- [ ] **CI/CD Tokens**: Use short-lived tokens with minimal scopes for pipeline operations

**Verification**: For each component, answer: "If this component is compromised, what is the blast radius?" Minimize that radius.

### Phase 2 — Enforce Secret Hygiene

Ensure no secrets are exposed in the codebase or version control:

- [ ] `.env` is in `.gitignore` (verify, do not assume — check the file)
- [ ] No hardcoded API keys, passwords, or tokens in source code
- [ ] GitHub Secret Scanning is enabled to block commits with known credential patterns
- [ ] Production secrets are injected at runtime via secure vault (AWS Secrets Manager, HashiCorp Vault, GitHub Actions Secrets)
- [ ] Local development uses `.env` files that are never committed
- [ ] Secret rotation policy is documented and followed
- [ ] Pre-commit hooks scan for accidental secret commits (e.g., `gitleaks`, `trufflehog`)

**Rotation schedule**: API keys — every 90 days. Database passwords — every 180 days. SSH keys — every 365 days. Emergency rotation procedure documented for each.

### Phase 3 — Address OWASP Top 10

Review code against the most critical web application security risks:

- [ ] **Injection (SQLi/NoSQLi)**: Never concatenate user input into queries. Use parameterized queries or a trusted ORM.
- [ ] **XSS**: All user input is sanitized and escaped before rendering in a browser. Use framework-provided escaping (React, Vue).
- [ ] **Authentication**: Passwords hashed with strong, salted algorithms (Argon2, bcrypt). Session tokens have short expiration, are `HttpOnly` + `Secure`, and support immediate revocation.
- [ ] **CSRF**: State-changing endpoints require CSRF tokens if using cookie-based authentication.
- [ ] **Broken Access Control**: Authorization checks on every endpoint. No reliance on obscurity. Test with role-based access scenarios.
- [ ] **Security Misconfiguration**: Default credentials changed. Unnecessary features disabled. Error messages do not leak internals.
- [ ] **Sensitive Data Exposure**: Encrypt data at rest and in transit. Classify data by sensitivity level.
- [ ] **XML External Entities**: Disable external entity processing in XML parsers.
- [ ] **Insecure Deserialization**: Validate and sanitize deserialized data. Never deserialize untrusted data without validation.
- [ ] **Insufficient Logging**: Log all authentication attempts, access control failures, and input validation errors.

### Phase 4 — Set Up Dependency Auditing & Supply Chain Security

Defend against attacks targeting your dependencies:

- [ ] **Dependabot or Renovate**: Automated dependency management scans manifest files and opens PRs for known vulnerabilities
- [ ] **Security Advisories**: Subscribe to advisories for core frameworks (Django, Express, React, etc.)
- [ ] **SAST in CI**: Static Application Security Testing (CodeQL or equivalent) runs on every PR
- [ ] **Dependency Lock Files**: Committed to version control for reproducible builds
- [ ] **Minimal Dependencies**: Regularly audit and remove unused packages
- [ ] **Signature Verification**: Verify package signatures where supported (npm, PyPI)
- [ ] **Private Registry**: Use a private registry proxy (Verdaccio, Artifactory) to control what enters your dependency tree

**Dependency update policy**:
| Severity | Update Window | Action |
|----------|--------------|--------|
| Critical | 48 hours | Emergency update, expedited review |
| High | 7 days | Standard update cycle |
| Medium | 30 days | Regular update cycle |
| Low | 90 days | Batch with other updates |

### Phase 5 — Configure Contributor Trust

Verify the integrity of the commit history:

- [ ] **GPG/SSH Commit Signatures**: Contributors sign commits to prove authorship
- [ ] **2FA**: Mandatory for all organization members on GitHub
- [ ] **Branch Protection**: PRs required, reviews mandatory, CI must pass before merge
- [ ] **CODEOWNERS**: Critical paths require specific reviewers
- [ ] **Commit Signing Enforcement**: Branch rules require signed commits
- [ ] **Invitation Policy**: New members require admin approval

### Phase 6 — Integrate Security Into CI/CD

Shift-left by making security checks part of the pipeline:

- [ ] Linting catches common security anti-patterns
- [ ] Type checking prevents type-related vulnerabilities
- [ ] SAST scans run on every PR
- [ ] Secret scanning blocks commits with credentials
- [ ] Dependency audit runs on every build
- [ ] Container scanning (if applicable) runs on image builds
- [ ] Security findings block the merge (not just warn)
- [ ] DAST (Dynamic Application Security Testing) runs against staging environments

**CI security pipeline**:
```
PR Opened → Lint → Type Check → SAST → Dependency Audit → Secret Scan → Tests → Build → Deploy to Staging → DAST → Ready for Review
```

---

## Security Checklist by Project Phase

### New Repository Setup
- [ ] `.gitignore` includes `.env`, secrets, and credentials
- [ ] Branch protection enabled on `main`
- [ ] Dependabot or Renovate configured
- [ ] Secret scanning enabled
- [ ] CODEOWNERS file created
- [ ] SECURITY.md with vulnerability reporting instructions
- [ ] 2FA required for all organization members

### First Commit
- [ ] No hardcoded secrets in initial code
- [ ] Database connection uses application-specific role
- [ ] Error handling does not expose internals
- [ ] Logging is configured (no secrets in logs)

### Pre-Production
- [ ] All OWASP Top 10 items addressed
- [ ] SAST integrated into CI
- [ ] Dependency audit passing
- [ ] Secret rotation schedule documented
- [ ] Incident response procedure documented
- [ ] Access control tested with role-based scenarios

### Production Release
- [ ] All CI security checks green
- [ ] No critical or high-severity dependency vulnerabilities
- [ ] OIDC configured for cloud deployments
- [ ] Monitoring and alerting configured for security events
- [ ] Rollback procedure documented and tested

---

## Glossary

| Term | Definition | Full Reference |
|------|-----------|---------------|
| [PoLP](standards/GLOSSARY.md#principle-of-least-privilege-polp) | Minimum permissions necessary for each user, service, and component. | Permission model |
| [Shift-Left](standards/GLOSSARY.md#shift-left) | Security checks happen as early as possible in the development lifecycle. | Process philosophy |
| [OIDC](standards/GLOSSARY.md#oidc-openid-connect) | Temporary, scope-limited credentials via trust relationships instead of static keys. | Cloud deployment |
| [Supply Chain Security](standards/GLOSSARY.md#supply-chain-security) | Defending against attacks that compromise open-source dependencies. | Dependency defense |
| [SAST](standards/GLOSSARY.md#supply-chain-security) | Static Application Security Testing — automated code analysis for vulnerabilities in CI. | CI integration |
| [OWASP](standards/GLOSSARY.md#owasp-top-10) | Open Web Application Security Project — standard list of critical web security risks. | Security standard |
| [Blameless](standards/GLOSSARY.md#blameless) | Treat security incidents as system failures, not personal failures. | Incident culture |

---

## Chaining

**Upstream**:
- `standards/apply-principles` — Principle of Least Privilege, automation, and blameless culture ground security decisions

**Downstream**:
- `standards/write-tests` — Security tests (injection, auth, CSRF) are part of the test pyramid
- `standards/design-api` — Error envelopes, authentication, and CSRF protection are API design concerns
- `workflow/create-pr` — Security checks must pass before merge; CODEOWNERS review critical paths
- `workflow/ship-release` — CI security gates must be green before any release

**Cross-cutting**: Shift-left security applies to every phase of the lifecycle. Every skill that touches code, dependencies, or deployment inherits these standards. When in doubt, ask: "What is the security implication of this change?"
