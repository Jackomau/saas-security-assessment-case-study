# 6. Authentication and Authorization Review

## 6.1 Purpose of this document

This document reviews the authentication and authorization controls of the anonymised SaaS marketplace platform. It evaluates how identities are managed, how access decisions are enforced, and how session handling is implemented across the system.

The review is based on the architectural descriptions in **03-architecture-summary.md**, the attack surface analysis in **05-attack-surface.md**, and the 32-point security checklist in **artifacts/auth-api-security-checklist.md**.

The goal is to identify gaps in identity and access management and provide actionable recommendations for improvement.

## 6.2 Authentication Review

### 6.2.1 Authentication Flow

The platform supports user registration, login, and password reset flows.

| Flow | Mechanism | Current Implementation (as observed) | Risk |
|------|-----------|-------------------------------------|------|
| User registration | Email + password | New users create accounts via a web or mobile form | Need to ensure email verification to prevent fake accounts |
| Login | Username / email + password | Returns a session token (JWT or opaque) | Must protect against credential stuffing and brute force |
| Password reset | Email-based link | A time-limited token is sent to the registered email | Token must be single-use and expire quickly |
| Logout | Token invalidation | Implementation unclear; may rely on client-side token deletion | Without server-side invalidation, stolen tokens remain valid |

**Key observations:**
- The platform appears to use token-based authentication (likely JWT) given its modern TypeScript stack. This reduces some session management complexity but introduces risks if tokens are not properly validated and rotated.
- Password reset flows are a common attack vector. If reset tokens are predictable or not properly expired, account takeover is possible.

### 6.2.2 Password and Credential Policy

| Requirement | Assessment |
|-------------|-----------|
| Minimum password length | Should enforce at least 8-12 characters |
| Complexity requirements | Mixed case, numbers, and special characters recommended |
| Rate limiting on login | Critical to prevent brute force and credential stuffing |
| Account lockout | Temporary lockout after repeated failures reduces guessing attacks |
| MFA availability | Not currently implemented; recommended for coach and admin accounts |

**Key findings:**
- MFA is absent. This is a significant gap for a platform handling financial transactions (payouts) and sensitive personal data. At minimum, MFA should be required for administrative accounts and recommended for coaches.
- Rate limiting on the login endpoint is essential given the public-facing login page identified in the attack surface analysis.

### 6.2.3 Session and Token Management

| Control | Assessment |
|---------|-----------|
| Token storage (client-side) | Tokens should be stored in httpOnly cookies or HTTPS-secured storage, not localStorage (XSS risk) |
| Token validation (server-side) | Must verify signature, expiry, issuer, and audience on every request (AUTH-08) |
| Token expiry | Short-lived access tokens (minutes to hours) with refresh tokens for long-term sessions |
| Token refresh | Refresh token rotation should be enforced to limit replay attacks |
| Session invalidation | Logout, password change, and role change must invalidate all active sessions (AUTH-04) |

**Key findings:**
- If tokens are stored in localStorage, the platform is vulnerable to XSS attacks where malicious scripts can exfiltrate tokens. httpOnly cookies should be preferred.
- Without proper session invalidation on logout, a stolen token remains valid indefinitely.

---

## 6.3 Authorization Review

### 6.3.1 Role-Based Access Control (RBAC)

The platform has three user roles: Clients, Coaches, and Administrators.

| Control | Assessment |
|---------|-----------|
| Role enforcement at API layer | Each endpoint must check the caller's role, not just their login state (AUTH-03) |
| Role claims not trusted from client | Role must be derived server-side from the validated token, not accepted from client input (AUTH-07) |
| Admin path separation | Administrative endpoints should use separate routes (/api/admin/*) not accessible to regular users (AUTH-09) |
| Least privilege | Service accounts and internal integrations should have minimal permissions (AUTH-10) |

**Key observations:**
- The distinction between coach and client roles must be strictly enforced at the data layer, not just the presentation layer.
- Admin endpoints, if not properly separated and protected, are a critical risk (identified as Critical severity in 05-attack-surface.md).

### 6.3.2 Object-Level Authorization (BOLA/IDOR)

A recurring risk identified in the threat model and attack surface analysis is Broken Object-Level Authorization (BOLA), where users can access or modify resources belonging to other users by manipulating resource IDs.

| Check | Requirement | Source |
|-------|------------|--------|
| Ownership on read | Before returning a resource, verify the authenticated user owns or is permitted to view it (DATA-01) | auth-api-security-checklist |
| Ownership on write | Before updating/deleting a resource, verify the authenticated user owns it (DATA-02) | auth-api-security-checklist |
| No trust in client IDs | Resource IDs should never be trusted directly from client input (DATA-03) | auth-api-security-checklist |
| Tenant scoping | All queries must include tenant or account filtering (DATA-04) | auth-api-security-checklist |
| Row-level security | Database-level RLS or equivalent constraints should enforce access boundaries (DATA-05) | auth-api-security-checklist |
| Bulk operation scope | Bulk operations (e.g., export all bookings) must be scoped to the caller (DATA-10) | auth-api-security-checklist |

**Key findings:**
- BOLA is one of the most common and impactful vulnerabilities in API-driven applications. The checklist includes 10 explicit checks (DATA-01 through DATA-10) to address this category.
- Without row-level security at the database layer, authorization must be implemented perfectly at the application layer — a fragile assumption.

---

## 6.4 Data Access Patterns

### 6.4.1 Secure Data Access Design

| Pattern | Description | Assessment |
|---------|-------------|-----------|
| Server-side enforcement | Security decisions are enforced in API/backend code, not in the frontend | Required |
| Query parameterization | All database queries are parameterized; no string concatenation with user input | Required |
| Pagination hardening | List endpoints filter results by role and tenant before returning (DATA-07) | Required |
| Indirect references | User-facing IDs should be opaque (UUIDs or hashed); sequential numbers leak information (DATA-08) | Recommended |
| Soft delete handling | Soft-deleted records must not be exposed via queries or API responses (DATA-09) | Required |

### 6.4.2 Sensitive Data Handling

| Data Type | Storage | Access Control Required |
|-----------|---------|------------------------|
| User profile data | Cloud database | Tenant-scoped, ownership-enforced |
| Booking history | Cloud database | Both parties (client + coach) have legitimate access |
| Messaging data | Cloud database | Both parties should have access; message content should be protected |
| Payment tokens | Third-party processor + stored tokens | Minimal data stored; tokens never logged |
| Audit logs | Logging system | Admin-only access; no PII in logs |

**Key findings:**
- The payment integration should not store cardholder data. Only non-cardholder identifiers (e.g., customer IDs, last four digits) should be stored locally.
- Messaging content contains sensitive personal data and should be treated with the same access controls as bookings and profile data.

---

## 6.5 Checklist Summary

The 32-point security checklist (AUTH-01 through LOG-06) is the detailed technical reference for this review. Key takeaways grouped by category[web:3]:

### Authentication, Sessions, and RBAC (AUTH-01 to AUTH-10)

| Check | Description | Status to Verify |
|-------|-------------|-----------------|
| AUTH-01 | Auth guard on every privileged function | Verify all endpoints have auth middleware |
| AUTH-02 | Identity bound from authenticated context | No use of client-supplied IDs for actions |
| AUTH-03 | Role checked, not just login state | Admin endpoint protection |
| AUTH-04 | Session invalidation rules defined | Logout, password change, role change |
| AUTH-05 | Password/credential hygiene and rate limiting | Brute force protection |
| AUTH-06 | Account recovery flows protected | Reset token security |
| AUTH-07 | No trust in client role claims | Server-side role derivation |
| AUTH-08 | Token validation includes expiry, issuer, audience | JWT claims verification |
| AUTH-09 | Separate admin paths | /api/admin/* isolation |
| AUTH-10 | Least privilege for service accounts | Internal integrations |

### BOLA / Data Access (DATA-01 to DATA-10)

All 10 checks address the BOLA category described in 6.3.2 above. These should be verified at the code review and penetration testing stages.

---

## 6.6 Findings Summary

| ID | Finding | Category | Severity | Related Threat |
|----|---------|----------|----------|----------------|
| F-01 | MFA not implemented for any user role | Authentication | High | T-2 (account takeover via stolen credentials) |
| F-02 | Token storage mechanism may be insecure (e.g., localStorage) | Authentication | High | T-2 (token theft via XSS) |
| F-03 | BOLA risks across multiple API endpoints | Authorization | High | T-1 (unauthorised data access) |
| F-04 | Admin endpoint separation may be insufficient | Authorization | Critical | T-3 (privilege escalation) |
| F-05 | Session invalidation may not be enforced server-side | Authentication | Medium | T-2 (stale token use) |
| F-06 | Password reset token expiry and single-use not confirmed | Authentication | Medium | T-2 (account takeover) |

---

## 6.7 Recommendations

| Priority | Recommendation | Addresses |
|----------|---------------|-----------|
| **Immediate** | Implement rate limiting on login, registration, and password reset endpoints | F-01, AUTH-05 |
| **Immediate** | Enforce server-side session invalidation on logout and password change | F-05, AUTH-04 |
| **Immediate** | Verify all API endpoints enforce ownership checks before data access | F-03, DATA-01 through DATA-10 |
| **Immediate** | Separate and restrict /api/admin/* routes to admin-only tokens | F-04, AUTH-09 |
| **Short-term** | Implement MFA for admin and coach accounts | F-01, AUTH-05 |
| **Short-term** | Replace localStorage token storage with httpOnly cookies | F-02, AUTH-08 |
| **Short-term** | Implement row-level security at the database layer | F-03, DATA-05 |
| **Short-term** | Use opaque identifiers (UUIDs) instead of sequential IDs in API responses | F-03, DATA-08 |
| **Medium-term** | Add MFA for all user accounts | F-01 |
| **Medium-term** | Implement password reset token rotation (single-use, short expiry) | F-06, AUTH-06 |

These findings feed into the risk register in **10-risk-register.md** and the remediation roadmap in **11-remediation-roadmap.md**.

## 6.8 Relationship to Other Documents

| Document | Relationship |
|----------|-------------|
| **03-architecture-summary.md** | Describes the identity and access services architecture |
| **04-threat-model.md** | T-1, T-2, T-3 threats directly relate to auth/authz gaps |
| **05-attack-surface.md** | Sections 5.3.2 and 5.3.3 map attack surfaces to these controls |
| **artifacts/auth-api-security-checklist.md** | Detailed 32-item checklist referenced throughout this review |
| **10-risk-register.md** | Findings F-01 through F-06 map to risk register entries |

## 6.9 Limitations

This review is based on architectural documentation and high-level observations rather than live testing or code review. A production-grade audit would require:
- Full source code review against the 32-point checklist.
- API testing with tools such as Burp Suite or OWASP ZAP to validate authz enforcement.
- Token inspection to confirm storage, validation, and rotation behavior.
