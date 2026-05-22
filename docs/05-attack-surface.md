# 5. Attack Surface Analysis

## 5.1 Purpose of this document

This document maps the identified attack surface of the anonymised SaaS marketplace platform.
It organises potential entry points into logical categories, describes the associated risks, and links each surface to the STRIDE threats documented in **04-threat-model.md** and the risk register in **10-risk-register.md**.

The goal is to provide a comprehensive, structured view of where an attacker could attempt to interact with or compromise the platform.

## 5.2 What is the attack surface?

For this case study, the attack surface is defined as the sum of all points where an attacker could try to interact with the platform, extract data, or cause unintended behaviour.
These include user-facing interfaces, network-accessible services, third-party integrations, and internal controls.

The analysis below covers the attack surface within the in-scope boundaries defined in **02-scope-and-assumptions.md**.

## 5.3 Attack Surface Categories

### 5.3.1 Public-facing Web and Mobile Interfaces

These are the primary touchpoints through which unauthenticated and authenticated users interact with the platform.

| Surface | Description | Associated Threats | Severity |
|---------|-------------|-------------------|----------|
| Signup / onboarding pages | Public registration flows for coaches and clients | Credential stuffing, account enumeration, weak password requirements | Medium |
| Login page | Unauthenticated endpoint accepting credentials | Credential stuffing, brute force, token replay after login | High |
| Password reset flow | Email-based reset links | Token enumeration, account takeover if tokens are predictable | Medium |
| Public search / discovery pages | Coach search and listing views | Enumeration, scraping of coach data, information disclosure | Low-Medium |
| Booking request form | Authenticated user submits booking to a coach | BOLA if booking IDs are exposed; input validation gaps | Medium |
| Messaging interface | Authenticated messaging between users | XSS, IDOR on message threads, information disclosure | Medium |
| Profile and settings pages | User profile management | BOLA, privilege escalation if admin fields exposed | Medium |
| Mobile application binaries | iOS/Android app packages | Reverse engineering, hardcoded secrets, insecure storage | Medium |

**Key observations:**
- The login and password reset flows are the highest-priority public surfaces because they provide the initial foothold for account takeover attacks.
- The absence of visible rate limiting or CAPTCHA on public pages suggests potential for automated attacks.
- Mobile app attack surface is limited in this assessment but should be noted for future hardening work.

**Linked STRIDE threats:** T-1, T-2

---

### 5.3.2 Authenticated API Endpoints

The API backend is the core processing layer for all business logic. It presents the most significant attack surface due to its broad functionality.

| Surface | Description | Associated Threats | Severity |
|---------|-------------|-------------------|----------|
| /api/auth/* | Token issuance, refresh, session management | Token theft, replay, session fixation | High |
| /api/bookings/* | CRUD for booking management | BOLA/IDOR, tampering, unauthorised status changes | High |
| /api/messages/* | Message send/receive/retrieve | IDOR, XSS payloads via message content | High |
| /api/coaches/* | Coach profile and availability management | BOLA, unauthorised availability changes | Medium |
| /api/users/* | User profile CRUD | BOLA, enumeration of valid user IDs | Medium |
| /api/admin/* | Administrative endpoints (access logs, user management) | Privilege escalation if RBAC is absent or bypassable | Critical |
| /api/payments/* | Payment-related actions (payout info, transaction history) | BOLA on payout data, financial data exposure | High |
| /api/search | Search API for coaches and services | SQLi or injection if search is not properly parameterised | Medium |
| /api/health or /api/debug | Health check or debug endpoints | Information disclosure if exposed to unauthenticated users | Low-Medium |

**Key observations:**
- The /api/admin/* surface is the most critical; if RBAC is not properly enforced at the API layer, any authenticated user could escalate privileges.
- BOLA (Broken Object-Level Authorization) patterns are a recurring risk across all resource APIs where IDs are predictable (e.g., sequential numeric IDs).
- Token-based authentication reduces some session risks but introduces new surfaces if token validation is weak.

**Linked STRIDE threats:** T-1, T-2, T-3, T-5

---

### 5.3.3 Authentication and Session Management

This category focuses on the mechanisms that control identity verification and session lifecycle.

| Surface | Description | Associated Threats | Severity |
|---------|-------------|-------------------|----------|
| JWT / session token storage | How tokens are stored client-side | Token theft via XSS, insecure local storage | High |
| Token validation logic | Server-side verification of tokens | Weak validation, improper expiring logic, replay attacks | High |
| Token refresh mechanism | Automatic or manual token renewal | Refresh token theft, unlimited refresh loops | Medium |
| Multi-tenant isolation (coach vs. client) | Separation of user contexts | Cross-tenant data access | Medium |
| Admin / non-admin role separation | RBAC enforcement | Privilege escalation | Critical |
| Logout and session invalidation | Session termination handling | Session persistence after logout | Low-Medium |

**Key observations:**
- Role separation between admins and regular users is a critical control point (linked to T-3).
- Token refresh loops without proper rate limiting could allow indefinite access with a single compromised token.

**Linked STRIDE threats:** T-2, T-3

---

### 5.3.4 Database and Data Storage

The database holds sensitive operational and personal data.

| Surface | Description | Associated Threats | Severity |
|---------|-------------|-------------------|----------|
| Public API to database path | Any API call that triggers database queries | SQLi, data exfiltration, tampering | High |
| Database access credentials | Stored secrets for DB connectivity | Credential theft, lateral movement if leaked | Critical |
| Backup and export mechanisms | Data export or snapshot features | Bulk data exfiltration, excessive permissions | High |
| Encryption at rest | Whether stored data is encrypted | Data exposure if database is compromised | Medium |
| Tenant data separation | Logical isolation of coach/client data | Cross-tenant data leakage | High |

**Key observations:**
- Encryption at rest is a fundamental control; without it, any database breach results in full data exposure.
- The assessment assumes the cloud provider manages infrastructure-level encryption, but application-level sensitive field encryption should be considered for PII.

**Linked STRIDE threats:** T-1, T-5

---

### 5.3.5 Third-Party Payment Integration

Integration with an external payment processor introduces shared attack surfaces.

| Surface | Description | Associated Threats | Severity |
|---------|-------------|-------------------|----------|
| Payment webhooks | Inbound callbacks from payment provider | Webhook spoofing, replay, unauthorised status updates | Critical |
| API key / secret storage | Credentials for payment API calls | Credential theft, environment leakage | High |
| Payout data handling | Non-cardholder identifiers for payouts | Exposure of financial routing info | Medium |
| Redirect / return URLs | Post-payment redirect handling | Open redirect vulnerabilities | Medium |

**Key observations:**
- Webhook spoofing is the most critical threat at this boundary; without signature verification, a malicious actor could fabricate payment confirmation.
- API keys should never be stored client-side or logged.

**Linked STRIDE threats:** T-4, T-5

---

### 5.3.6 Logging and Monitoring Systems

Logging infrastructure is itself an attack surface.

| Surface | Description | Associated Threats | Severity |
|---------|-------------|-------------------|----------|
| Log ingestion endpoints | Where application emits logs | Log flooding, log injection | Low-Medium |
| Log storage and access | Access to log data | PII exposure, forensic data leakage | Medium |
| Alert and dashboard systems | Monitoring views | Information disclosure, alert fatigue | Low |
| Log flushing / rotation | Log lifecycle management | Log tampering or deletion | Medium |

**Key observations:**
- Logging gaps were already identified in the threat model (T-5); this surface analysis reinforces that incomplete logging creates forensic blind spots.
- PII in logs is a common anti-pattern and should be explicitly prohibited.

**Linked STRIDE threats:** T-5

---

### 5.3.7 Administrative Tooling

Internal admin interfaces represent high-privilege surfaces.

| Surface | Description | Associated Threats | Severity |
|---------|-------------|-------------------|----------|
| Admin web portal | Internal dashboard for support and management | Privilege escalation, full data access | Critical |
| Admin API endpoints | Direct API access for admin functions | Direct privilege escalation via API | Critical |
| User impersonation features | Support staff impersonating users | Abuse, audit gaps if not logged | High |
| Bulk actions (suspend, export, notify) | Administrative bulk operations | Abuse, accidental misuse | Medium |

**Key observations:**
- Admin surfaces should never be publicly routable; they should be behind VPN or IP allowlisting.
- All admin actions must be logged with user identity for auditability.

**Linked STRIDE threats:** T-3, T-5

---

## 5.4 Attack Surface Summary by Severity

| Severity | Count | Key Surfaces |
|----------|-------|-------------|
| Critical | 4 | Admin API, Admin portal, Payment webhooks, DB credentials |
| High | 8 | Login page, Auth API, Booking API, Messaging API, Payments API, Token storage, DB query path, Backup/export |
| Medium | 10 | Signup, Password reset, Profile pages, Coach API, User API, Search API, Token refresh, Multi-tenant isolation, Log access, Bulk admin actions |
| Low | 3 | Public search pages, Debug endpoints, Log ingestion |

---

## 5.5 Reduction Strategies

Based on the attack surface analysis, the following categories of controls should reduce exposure:

| Strategy | Description | Surfaces Addressed |
|----------|-------------|-------------------|
| Rate limiting and CAPTCHA | Protect public-facing endpoints from automated attacks | Login, signup, password reset |
| Input validation and output encoding | Prevent injection and XSS across all user-facing surfaces | All APIs and frontend |
| Strict RBAC and tenant isolation | Prevent privilege escalation and cross-tenant access | Admin surfaces, data APIs |
| Webhook signature verification | Authenticate inbound third-party callbacks | Payment integration |
| Token security hardening | Secure storage, short expiry, and proper validation | Auth/session surfaces |
| Security logging and alerting | Detect and respond to suspicious activity | All surfaces |
| Network segmentation and allowlisting | Restrict admin surfaces to trusted paths | Admin tooling |

These strategies inform the remediation roadmap in **11-remediation-roadmap.md** and the control mapping in **07-api-and-data-security-review.md** and **08-cloud-and-monitoring-review.md**.

## 5.6 Limitations

This analysis is based on a high-level architectural review and threat model. A deeper attack surface enumeration would require:
- Full API documentation and endpoint discovery (e.g., via code review or OpenAPI specs).
- Infrastructure-level network scanning.
- Source code analysis for hidden or undocumented endpoints.
- Third-party dependency review.

## 5.7 Relationship to Other Documents

| Document | Relationship |
|----------|-------------|
| **03-architecture-summary.md** | Provides the component and data-flow basis for this surface mapping |
| **04-threat-model.md** | STRIDE threats are linked to each surface |
| **10-risk-register.md** | Surfaces with "High" or "Critical" severity feed into risk register entries |
| **artifacts/auth-api-security-checklist.md** | Checkpoint items correspond to specific surfaces in this analysis |
