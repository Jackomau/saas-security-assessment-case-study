# 7. API and Data Security Review

## 7.1 Purpose of this document

This document reviews the API security controls and data protection mechanisms of the anonymised SaaS marketplace platform. It evaluates how the API layer enforces security boundaries, how sensitive data is handled in transit and at rest, and how the platform manages secrets, configuration, and logging.

The review is based on the architectural descriptions in **03-architecture-summary.md**, the attack surface analysis in **05-attack-surface.md**, the authentication review in **06-auth-and-authorization-review.md**, and the 32-point security checklist in **artifacts/auth-api-security-checklist.md**.

The goal is to identify gaps in API security, data handling, and operational security controls and provide actionable recommendations.

---

## 7.2 API Security Review

### 7.2.1 Endpoint Security and Input Handling

The API backend processes all business logic for bookings, messages, profiles, payments, and administrative functions. Security at the API layer depends on consistent enforcement of authentication, authorization, and input validation.

| Control | Assessment |
|---------|-----------|
| Authentication guard on endpoints | Every server-side endpoint should enforce an authentication check before processing (AUTH-01) |
| Identity derived from session context | Effective user identity must come from the token/session, not from client-supplied fields (AUTH-02) |
| Input validation | All user-supplied input (IDs, bookable dates, messages, search queries) must be validated for type, length, and format |
| Parameterised queries | All database queries must use parameterisation to prevent SQL injection (DATA-01) |
| Output encoding | Responses must not echo raw user input; encoding prevents reflected XSS through API responses (DATA-08) |

**Key findings:**
- The platform's TypeScript stack and Node.js runtime commonly use ORM/query builders that provide default SQL parameterisation, reducing injection risk. However, raw SQL or user-supplied search filters may still be vulnerable if not carefully reviewed.
- The presence of a public search API endpoint (*/api/search*) introduces risk. Search parameters must be validated, rate-limited, and filtered to prevent data enumeration or resource exhaustion.

### 7.2.2 Broken Object-Level Authorization (BOLA/IDOR)

BOLA is the primary data access threat for this platform. With three distinct roles and multi-tenant data (clients, coaches, admins), the API must enforce per-record authorization on every read and write.

| Checkpoint | Requirement | Applicable Checklist Item |
|------------|------------|------------------------|
| Read operations | Verify the current user is authorised to view the specific record before returning data | DATA-01 |
| Write operations | Verify record ownership before applying updates or deletes | DATA-02 |
| ID handling | Do not trust client-supplied IDs; re-derive from the authenticated scope | DATA-03 |
| Multi-tenant scope | Filter all queries by tenant/account scope (coach-client relationships) | DATA-04 |
| Row-level security | Apply database-level RLS or equivalent where supported | DATA-05 |
| Sensitive joins | Review joins across bookings, messages, profiles, and payments for scope leakage | DATA-06 |
| List endpoints | Apply pagination filters scoped to the user (not returning all records) | DATA-07 |
| Identifier exposure | Use opaque UUIDs; avoid sequential or guessable numeric IDs in responses | DATA-08 |
| Soft deletes | Ensure archived/deleted records are not accessible via normal queries | DATA-09 |
| Bulk operations | Limit bulk endpoints and filter to the caller's legitimate scope | DATA-10 |

**Key findings:**
- The booking and messaging endpoints are the highest-risk vectors for BOLA. A coach should only access bookings for their own clients, and a client should only access bookings they created.
- The user profile endpoint (/api/users/*) requires careful scoping. A client viewing a coach's public profile is valid; accessing another client's private profile is not.
- The admin endpoint (/api/admin/*) must be completely isolated from normal user paths and require admin tokens (AUTH-09).

### 7.2.3 API Response Security

| Control | Assessment |
|---------|-----------|
| Sensitive data in responses | Responses must not contain PII, tokens, or internal identifiers beyond what is necessary |
| Error handling | API errors must be sanitised to avoid stack traces, SQL fragments, or internal paths (LOG-04) |
| Response headers | Security headers (CSP, X-Frame-Options, etc.) should be consistently applied |
| Rate limiting per endpoint | Critical endpoints (auth, search, messaging) require rate limits to prevent abuse |

**Key findings:**
- The platform's API responses should never expose internal database IDs in sequential form. Using UUIDs for all resource identifiers (DATA-08) prevents enumeration attacks.
- Error messages from the backend must be generic (e.g., "Request failed") in production, with detailed logs only in server-side monitoring systems.

---

## 7.3 Secrets and Configuration Review

### 7.3.1 Secret Management

| Checklist Item | Requirement | Assessment |
|---------------|------------|----------|
| CLOUD-01 | No API keys, tokens, or secrets hard-coded in source | Must verify no leaks in version control history |
| CLOUD-02 | Environment files excluded from version control | .env files and config history must be checked for accidental leaks |
| CLOUD-03 | Cloud roles/identities use least privilege | App-level cloud identities must be scoped to required resources only |
| CLOUD-04 | Storage buckets not publicly readable | Object storage must be private by default |
| CLOUD-05 | Outbound connections documented and authenticated | All external API calls must use authenticated channels |
| CLOUD-06 | Backup and restore procedures defined | Recovery procedures must exist and be tested |

**Key findings:**
- As a TypeScript/Node.js application, the platform should use environment variables (e.g., via dotenv with .env files) for all secrets. No secrets should be committed to the repository.
- Cloud storage (likely for images, documents, or booking attachments) must not be publicly accessible. Signed URLs should be used for time-limited access.
- Third-party integrations (payment gateways, email providers, analytics) must use scoped credentials with minimal permissions.

### 7.3.2 Configuration Security

- Production configuration must differ from development configuration (no debug endpoints, verbose errors, or mock data in production).
- Feature flags or configuration values that affect security behavior (rate limits, session timeout, MFA enforcement) must not be modifiable by end users.
- API versioning should be enforced to prevent clients from using deprecated, insecure API versions.

---

## 7.4 Logging, Errors and Monitoring Review

### 7.4.1 Audit Logging Requirements

| Checklist Item | Requirement | Assessment |
|---------------|------------|----------|
| LOG-01 | Authentication events logged | Login, logout, password reset, and unusual activity must be logged |
| LOG-02 | Security-relevant actions logged | Role changes, payout updates, and admin actions require audit trails |
| LOG-03 | Logs must not contain secrets or excessive PII | Tokens, passwords, and unnecessary personal data must be redacted |
| LOG-04 | Error messages sanitised | Production errors must not expose internal details to users |
| LOG-05 | Monitoring for abnormal patterns | Spikes in errors, failed logins, or unusual API usage must trigger alerts |
| LOG-06 | Log retention and access controlled | Logs must have defined retention periods and access must be restricted |

**Key findings:**
- The platform should log all authentication events with ISO 8601 timestamps, user IDs (not emails), IP addresses, and user agents. This supports incident investigation (LOG-01).
- Payout-related actions are high-risk events that must generate audit logs linked to the initiating user and session (LOG-02).
- Log entries must never contain full JWT tokens, passwords, or health data. A logging middleware or structured format should enforce redaction (LOG-03).

### 7.4.2 Monitoring and Alerting

| Metric | Baseline | Alert Threshold |
|--------|---------|----------------|
| Failed login attempts | Normal: < 5 per user per hour | Alert: > 10 in 5 minutes |
| API error rate (5xx) | Normal: < 1% of requests | Alert: > 5% sustained |
| New user registrations | Normal: platform-dependent | Alert: sudden spike (bot farm) |
| Search API calls | Normal: < 100 per user per day | Alert: > 500 (enumeration attempt) |
| Admin endpoint access | Expected: admin tokens only | Alert: any non-admin access |

---

## 7.5 Data Protection Review

### 7.5.1 Data Classification

| Data Type | Sensitivity | Applicable Protections |
|-----------|------------|----------------------|
| User credentials (password hashes) | Critical | Salted hashing (bcrypt/argon2); never in logs |
| Session tokens | Critical | Short-lived; httpOnly; https-only; rotation |
| Payment/payout data | Critical | Tokenised via payment gateway; PCI-DSS alignment |
| Personal contact information | High | Encrypted at rest; least-privilege access |
| Booking and messaging data | Medium-High | Scoped access; retention policies |
| Public profiles (coach listings) | Low | Cacheable; no auth required for read |

### 7.5.2 Data in Transit

- All API communication must use HTTPS (TLS 1.2+) with valid certificates.
- Mobile app clients must implement certificate pinning to prevent MITM attacks.
- Internal service-to-service communication (if applicable) should use mutual TLS or private networking.

### 7.5.3 Data at Rest

- Sensitive fields in the database (contact details, token refresh fields) should be encrypted at rest.
- File storage (e.g., uploaded profile images) must be stored in private buckets with access mediated through the API.
- Database backups must be encrypted and stored separately from production data.

---

## 7.6 Findings Summary

| Finding ID | Description | Checklist Items | Severity |
|----------|-------------|----------------|---------|
| F-07 | BOLA risks: insufficient ownership checks on booking and messaging endpoints | DATA-01 to DATA-07 | High |
| F-08 | Potential secret exposure: risk of API keys in environment files or version control | CLOUD-01, CLOUD-02 | High |
| F-09 | Insufficient audit logging: security events may not generate sufficient context | LOG-01, LOG-02, LOG-06 | Medium |
| F-10 | Search API lacks rate limiting and enumeration protection | DATA-07, LOG-05 | Medium |
| F-11 | Storage bucket configuration may permit public access | CLOUD-04 | Medium |
| F-12 | Error handling may expose internal details to users | LOG-04 | Low-Medium |

---

## 7.7 Recommendations

| Priority | Recommendation | Addresses |
|----------|---------------|----------|
| **Immediate** | Enforce ownership checks on all read/write API endpoints (DATA-01 to DATA-03) | F-07 |
| **Immediate** | Audit version control history and environment configs for leaked secrets | F-08 |
| **Immediate** | Restrict storage bucket access; enforce private defaults with signed URLs | F-11 |
| **Short-term** | Implement structured logging with redaction for sensitive fields | F-09 |
| **Short-term** | Add rate limiting to search and public-facing API endpoints | F-10 |
| **Short-term** | Sanitise error responses in production; send detailed errors to monitoring only | F-12 |
| **Medium-term** | Implement database-level row-level security (RLS) for critical tables | F-07 |
| **Medium-term** | Deploy monitoring with threshold-based alerting for security events | F-09, F-10 |

These findings complement the authentication findings in **06-auth-and-authorization-review.md** and feed into the risk register in **10-risk-register.md** and remediation roadmap in **11-remediation-roadmap.md**.

---

## 7.8 Relationship to Other Documents

| Document | Relationship |
|---------|------------|
| **03-architecture-summary.md** | Describes the API layer and data storage architecture |
| **04-threat-model.md** | T-2 and T-4 threats directly relate to BOLA and data exposure |
| **05-attack-surface.md** | Sections 5.3.2 and 5.3.3 map attack surfaces to API and data controls |
| **06-auth-and-authorization-review.md** | AUTH-01 through AUTH-10 controls are prerequisites for this review |
| **artifacts/auth-api-security-checklist.md** | DATA-01 through DATA-10, CLOUD-01 through CLOUD-06, and LOG-01 through LOG-06 referenced throughout |
| **10-risk-register.md** | Findings F-07 through F-12 map to risk register entries |
| **11-remediation-roadmap.md** | Recommendations feed into implementation phases |

---

## 7.9 Limitations

This review is based on architectural documentation and checklist-based assessment rather than live testing or code review. A production-grade audit would require:

- Static code analysis to verify secret management practices and query parameterisation.
- Dynamic API testing (e.g., OWASP ZAP, Burp Suite) to confirm authentication guards and BOLA enforcement.
- Review of actual log files and monitoring dashboards to validate logging completeness.
- Verification of cloud infrastructure configuration (bucket policies, IAM roles, network security groups).
