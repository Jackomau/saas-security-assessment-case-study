# 4. Threat Model (STRIDE)

## 4.1 Purpose of this document

This document captures a high‑level threat model for the anonymised SaaS marketplace platform using the STRIDE framework.  
The goal is to identify realistic threats against key components and data flows so that risks can be prioritised and appropriate controls can be planned.

This is not an exhaustive list of every possible attack, but a structured starting point that can be refined as the platform and architecture evolve.

---

## 4.2 Methodology

The threat modelling approach in this case study follows a simple sequence aligned with common STRIDE practices:

1. **Define scope and diagrams**  
   Use the architecture summary and data‑flow descriptions from `03-architecture-summary.md` to understand components, data stores, user roles and trust boundaries.

2. **Identify STRIDE threats per element**  
   For each key component or data flow, consider the STRIDE categories:  
   - S – Spoofing identity  
   - T – Tampering with data  
   - R – Repudiation  
   - I – Information disclosure  
   - D – Denial of service  
   - E – Elevation of privilege

3. **Document threats and potential impact**  
   Describe what could go wrong in business terms (not just technical) and which assets are at risk.

4. **Link to controls and risks**  
   Map threats to existing or missing controls, and carry them forward into the risk register in `10-risk-register.md`.

---

## 4.3 Assets and security objectives (summary)

Key assets considered in this threat model include:

- Client and coach accounts and profile data  
- Booking records and messaging history  
- Payout and transaction‑related identifiers (non‑cardholder)  
- Administrative tools and configuration settings  
- Authentication tokens, sessions and API keys  
- Application logs and audit trails

High‑level security objectives are to preserve:

- **Confidentiality** of personal and sensitive data  
- **Integrity** of bookings, messages and payouts  
- **Availability** of core services (search, booking, messaging)  
- **Accountability** for important actions through logging and audit trails

---

## 4.4 STRIDE analysis by component (examples)

This section provides example threats for a subset of components, using the STRIDE categories as a guide.  
In a full assessment, each component and data flow from the architecture summary would be examined in similar fashion.

A more detailed, implementation‑level checklist for authentication, API access control,
cloud configuration and logging is provided in
`artifacts/auth-api-security-checklist.md`. That checklist was used during the original
review to ensure consistent coverage across endpoints.

### 4.4.1 Frontend applications (web and mobile)

**Spoofing**

- Attacker attempts to reuse stolen access tokens or session cookies to impersonate a legitimate client or coach on the frontend.  
- Weak or missing protections against token theft (e.g. lack of secure cookie flags or poor session management) increase this risk.

**Tampering**

- Malicious user manipulates client‑side requests (e.g. changing IDs in API calls) to attempt unauthorised actions such as viewing or modifying other users’ bookings.  
- Relying solely on client‑side checks without server‑side validation makes tampering more impactful.

**Information disclosure**

- Sensitive error messages or debug info returned to the frontend accidentally reveal implementation details, internal IDs or configuration hints that could aid further attacks.

*(Related risks and controls are expanded in the attack surface and API review sections.)*

---

### 4.4.2 API backend

**Spoofing**

- API endpoints accept tokens without properly validating signature, audience or expiry, allowing forged or replayed tokens to be used. 

**Tampering**

- Insufficient validation of request bodies allows an attacker to modify fields such as `userId` or `coachId` and affect resources they do not own (Insecure Direct Object Reference / BOLA).

**Repudiation**

- Critical actions (e.g. admin changes, payout approvals) are not consistently logged with who did what and when, making it difficult to prove or dispute actions.

**Information disclosure**

- Error responses from the API inadvertently leak stack traces, internal table names, or configuration details.

**Denial of service**

- Certain endpoints (e.g. search, messaging or booking operations) lack rate limiting, making them easier to abuse for resource exhaustion.

**Elevation of privilege**

- Missing or inconsistent authorisation checks on admin‑only endpoints allow a normal coach or client token to call high‑privilege APIs.

---

### 4.4.3 Authentication and session management

**Spoofing**

- Weak password policies or lack of multi‑factor authentication make account takeover via credential stuffing or phishing more likely.  
- If session invalidation on logout/password change is incomplete, attackers could keep using old sessions.

**Repudiation**

- Login attempts, failed logins and suspicious activities are not reliably logged, making it difficult to investigate credential‑related incidents.

**Elevation of privilege**

- Role or permission information is trusted solely from client‑supplied claims without server‑side verification, enabling manipulation of tokens to gain higher privileges.

---

### 4.4.4 Database and storage

**Tampering**

- If application‑level controls are weak, an attacker who gains elevated access could modify bookings, payouts or messaging history without sufficient detection.  

**Information disclosure**

- Misconfigured access controls or insufficient segregation of tenant data could allow one coach or admin to see records belonging to another, beyond legitimate need.  
- Inadequate encryption at rest or overly broad read access may increase the impact of a data store compromise.

**Repudiation**

- Lack of proper audit tables or change tracking for critical entities (e.g. bookings, payouts) makes it difficult to reconstruct who changed what.

---

### 4.4.5 Payments integration

**Spoofing**

- If webhooks from the payments provider are not properly authenticated and verified, an attacker could spoof payment events to mark bookings as paid or payouts as completed.

**Tampering**

- Manipulation of request parameters to the payments API (e.g. mis‑bound customer identifiers) could result in funds being directed to the wrong recipient.

**Information disclosure**

- Logging of payment tokens or identifiers in plaintext logs could increase exposure if log storage is compromised.

---

### 4.4.6 Logging and monitoring

**Repudiation**

- Incomplete or inconsistent logging of security‑relevant events (auth failures, changes to roles, payout updates) allows malicious actions to go undetected or be denied.

**Denial of service**

- Lack of alerting on error spikes, failed logins or unusual request patterns may delay detection of attacks and prolong service impact.

**Information disclosure**

- Sensitive data accidentally written to logs (e.g. secrets, personal details) could be exposed to a wider audience than intended.

---

## 4.5 Example threat entries (table format)

The threats above can be turned into structured entries that feed the risk register.  
Below is a small illustrative subset:

| ID  | Component            | STRIDE | Threat description                                                        | Potential impact                         |
|-----|----------------------|--------|---------------------------------------------------------------------------|------------------------------------------|
| T‑1 | API backend          | T/BOLA | User manipulates IDs in requests to view or modify other users’ bookings  | Unauthorised data access, data integrity |
| T‑2 | Auth / sessions      | S      | Stolen or replayed tokens used to impersonate users                       | Account takeover, fraud                  |
| T‑3 | Admin tooling        | E      | Missing checks allow non‑admin to call admin endpoints                    | Privilege escalation                     |
| T‑4 | Payments integration | S      | Spoofed payment webhooks mark bookings as paid                            | Financial loss, trust damage             |
| T‑5 | Logging              | R/I    | Critical actions not logged, or logs leak sensitive data                  | Poor forensics, confidentiality risks    |

These entries are expanded and prioritised in `10-risk-register.md`, where likelihood, impact, existing controls and recommended treatments are recorded.

---

## 4.6 Limitations

This threat model is intentionally high‑level and tailored for a portfolio‑friendly case study.  
In a real engagement, threats would be refined with system owners, informed by more detailed architecture diagrams, code review and targeted testing, and revisited as the system changes over time.

---