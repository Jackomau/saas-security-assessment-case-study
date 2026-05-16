# 10. Risk Register

## 10.1 Purpose of this document

This document records the key security risks identified during the anonymised SaaS security assessment case study.  
Its purpose is to translate technical findings into a structured, prioritised view that can support remediation planning, stakeholder communication and future tracking.

The register is intentionally lightweight and designed for a small SaaS organisation.  
It focuses on practical risk ownership, clear treatment actions and simple qualitative scoring rather than a complex enterprise GRC workflow.

---

## 10.2 How to read this register

Each entry includes:

- A unique risk ID  
- A short risk statement  
- The affected asset or process  
- The likely cause or weakness  
- The business impact if the risk materialises  
- A qualitative likelihood and impact rating  
- An overall priority rating  
- Current controls or observations  
- Recommended treatment actions  
- A notional owner  
- A treatment status

This format is intended to keep the register readable for both technical and non-technical audiences.

---

## 10.3 Scoring approach

This case study uses a simple qualitative model:

### Likelihood

- **Low** – unlikely under normal conditions; would usually require specific circumstances or multiple weaknesses  
- **Medium** – plausible and realistic given the current environment  
- **High** – likely to occur or easy to exploit if the weakness remains unaddressed

### Impact

- **Low** – limited operational disruption or data exposure, manageable with minimal business effect  
- **Medium** – meaningful disruption, moderate data exposure, or customer trust impact  
- **High** – major confidentiality, integrity or availability impact; financial, legal or reputational consequences

### Priority

Priority is derived from the combination of likelihood and impact:

| Likelihood | Impact | Priority |
|------------|--------|----------|
| Low        | Low    | Low      |
| Low        | Medium | Low      |
| Low        | High   | Medium   |
| Medium     | Low    | Low      |
| Medium     | Medium | Medium   |
| Medium     | High   | High     |
| High       | Low    | Medium   |
| High       | Medium | High     |
| High       | High   | Critical |

This simple matrix is sufficient for a portfolio case study and helps keep prioritisation consistent across findings.

---
The entries below are written in narrative form for readability.  
A spreadsheet or CSV version can be used separately for sorting, filtering and status tracking.

## 10.4 Risk register entries

### R-01 – Weak object-level access control in selected API workflows

- **Affected asset / process:** Booking records and user-linked data  
- **Risk description:** Some application workflows may rely too heavily on client-supplied identifiers, creating a risk that a user could access or modify records outside their authorised scope.  
- **Likelihood:** Medium  
- **Impact:** High  
- **Priority:** High  
- **Current controls / observations:** Authentication appears to exist, but ownership validation may be inconsistent across endpoints.  
- **Recommended treatment:** Enforce ownership checks server-side for all read/write operations, review endpoints for BOLA/IDOR patterns, and add negative test cases.  
- **Owner:** Engineering Lead  
- **Status:** Open  

### R-02 – Inconsistent role and permission enforcement

- **Affected asset / process:** Administrative and privileged functions  
- **Risk description:** If role checks are applied inconsistently, a normal authenticated user may gain access to coach-only or admin-level actions.  
- **Likelihood:** Medium  
- **Impact:** High  
- **Priority:** High  
- **Current controls / observations:** Role concepts appear to exist, but separation of privileged paths may be incomplete.  
- **Recommended treatment:** Review the role model, centralise authorisation checks, and separate admin functions from normal user workflows.  
- **Owner:** Engineering Lead  
- **Status:** Open  

### R-03 – Overexposure of sensitive data through API responses or logs

- **Affected asset / process:** Personal data, booking records and operational logs  
- **Risk description:** API responses, logs or error messages may reveal more information than needed, increasing confidentiality risk and supporting attacker reconnaissance.  
- **Likelihood:** Medium  
- **Impact:** Medium  
- **Priority:** Medium  
- **Current controls / observations:** Logging exists at some level, but security-focused review of output and error handling is needed.  
- **Recommended treatment:** Minimise response payloads, sanitise errors, remove sensitive values from logs, and define secure logging guidelines.  
- **Owner:** Engineering Lead  
- **Status:** Open  

### R-04 – Insufficient logging for security-relevant events

- **Affected asset / process:** Authentication, admin actions and payout workflows  
- **Risk description:** If important actions are not logged consistently, malicious or suspicious behaviour may be difficult to investigate or prove later.  
- **Likelihood:** Medium  
- **Impact:** Medium  
- **Priority:** Medium  
- **Current controls / observations:** Operational visibility exists, but audit-quality logging may be incomplete.  
- **Recommended treatment:** Define minimum audit events, log authentication failures, role changes, payout actions and admin operations, and restrict access to log data.  
- **Owner:** Platform / Ops Owner  
- **Status:** Open  

### R-05 – Weak secret management practices

- **Affected asset / process:** API keys, service credentials and cloud configuration  
- **Risk description:** Poor handling of environment variables, service credentials or secret rotation could expose the platform to unauthorised access or misuse of integrations.  
- **Likelihood:** Medium  
- **Impact:** High  
- **Priority:** High  
- **Current controls / observations:** No direct evidence of compromise is assumed in this case study, but this remains a common SaaS control gap that should be reviewed explicitly.  
- **Recommended treatment:** Remove hard-coded secrets, validate repository hygiene, and implement secure secret storage and rotation practices.  
- **Owner:** Platform / Ops Owner  
- **Status:** Open  

### R-06 – Payment integration trust boundary not fully hardened

- **Affected asset / process:** Payment workflow and payout logic  
- **Risk description:** Weak validation of third-party payment events or misconfiguration of integration logic could lead to incorrect payment state changes or fraud scenarios.  
- **Likelihood:** Low  
- **Impact:** High  
- **Priority:** Medium  
- **Current controls / observations:** The third-party payments provider reduces direct card handling risk, but integration logic remains security-relevant.  
- **Recommended treatment:** Verify webhook authenticity, restrict event handling paths, log payment state changes, and review payout approval logic.  
- **Owner:** Engineering Lead  
- **Status:** Open  

### R-07 – Limited separation between standard and administrative functions

- **Affected asset / process:** Admin tools, support workflows and platform configuration  
- **Risk description:** Admin workflows may share components or routes with standard user functions, increasing the blast radius of authorisation failures.  
- **Likelihood:** Medium  
- **Impact:** High  
- **Priority:** High  
- **Current controls / observations:** Functional separation may exist, but stronger isolation would reduce risk.  
- **Recommended treatment:** Isolate administrative interfaces, enforce stronger role checks, and monitor access to admin features.  
- **Owner:** Engineering Lead  
- **Status:** Open  

### R-08 – Incomplete session and token lifecycle controls

- **Affected asset / process:** User authentication and session management  
- **Risk description:** Weak token validation, inadequate session invalidation or insufficient account recovery protections could increase the likelihood of account takeover.  
- **Likelihood:** Medium  
- **Impact:** Medium  
- **Priority:** Medium  
- **Current controls / observations:** Authentication is present, but lifecycle controls require deeper review.  
- **Recommended treatment:** Validate token expiry and audience, improve session invalidation, review password reset and recovery controls, and consider a future MFA roadmap.  
- **Owner:** Engineering Lead  
- **Status:** Open  

### R-09 – Cloud storage or data access permissions broader than necessary

- **Affected asset / process:** Managed storage and database access  
- **Risk description:** Overly broad storage or database permissions may allow unnecessary access to user data or increase the impact of a compromised component.  
- **Likelihood:** Medium  
- **Impact:** High  
- **Priority:** High  
- **Current controls / observations:** Managed cloud services reduce infrastructure burden, but access design still requires least-privilege review.  
- **Recommended treatment:** Review storage and database access policies, minimise service permissions, and verify tenant and data segregation assumptions.  
- **Owner:** Platform / Ops Owner  
- **Status:** Open  

### R-10 – Gaps in formal governance and operational security practices

- **Affected asset / process:** Access reviews, key rotation and incident readiness  
- **Risk description:** Even where technical controls exist, the absence of lightweight governance practices may reduce consistency and delay response when issues occur.  
- **Likelihood:** Medium  
- **Impact:** Medium  
- **Priority:** Medium  
- **Current controls / observations:** Security work appears to be emerging rather than fully standardised.  
- **Recommended treatment:** Introduce lightweight control ownership, periodic access review, a key rotation schedule, and basic incident response guidance.  
- **Owner:** Founder / Operations Lead  
- **Status:** Open  

---

## 10.5 Status definitions

The following status labels are used in this register:

- **Open** – risk identified and treatment not yet started  
- **In progress** – treatment actions are underway  
- **Mitigated** – agreed treatment actions implemented to an acceptable level  
- **Accepted** – risk formally accepted due to business constraints or low priority  
- **Deferred** – risk recognised but treatment postponed for a later phase

These labels are intentionally simple so that the register remains practical for a small team.

---

## 10.6 Notes on ownership

The named owners in this case study are indicative rather than tied to real individuals.  
For a small SaaS organisation, ownership often sits with 