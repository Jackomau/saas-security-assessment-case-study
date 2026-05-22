# 9. GRC Gap Analysis

## 9.1 Purpose of this document

This document provides a governance, risk, and compliance (GRC) gap analysis for the anonymised SaaS marketplace platform. It maps the technical and operational findings from the security assessment against recognised frameworks and identifies where the platform's current security posture falls short of best-practice governance standards.

The review is based on the architectural descriptions in **03-architecture-summary.md**, the threat model in **04-threat-model.md**, the technical review findings in **05-attack-surface.md** through **08-cloud-and-monitoring-review.md**, and the risk register entries in **10-risk-register.md**.

The goal is to provide a bridge between technical security findings and executive-level governance themes, supporting the remediation roadmap in **11-remediation-roadmap.md** and the executive summary in **12-executive-summary.md**.

---

## 9.2 GRC Framework Selection

This analysis uses two complementary frameworks:

### 9.2.1 NIST Cybersecurity Framework (CSF) v2.0

The NIST CSF v2.0 provides six core functions with which to evaluate the platform's security posture:

| Function | Focus Area |
|----------|----------|
| **Govern** | Security strategy, risk management, and oversight |
| **Identify** | Asset management, business environment understanding, risk assessment |
| **Protect** | Access control, data security, protective technology |
| **Detect** | Anomalies and events detection, continuous monitoring |
| **Respond** | Incident response planning and execution |
| **Recover** | Recovery planning and restoration of services |

### 9.2.2 ISO 27001:2022 (Information Security Management)

ISO 27001:2022 Annex A provides a comprehensive set of security controls organised into four themes:

| Theme | Control Areas |
|-------|-------------|
| **Organisational** (37 controls) | Policies, roles, access control, supplier relationships, information security policies |
| **People** (8 controls) | Security awareness, screening, disciplinary processes |
| **Physical** (14 controls) | Facilities, equipment security |
| **Technological** (34 controls) | Authentication, cryptography, network security, secure development, logging |

---

## 9.3 NIST CSF v2.0 Gap Analysis

| CSF Function | Expected State | Current State | Gap Severity |
|-------------|---------------|--------------|-------------|
| **GOVERN** | Security strategy, policies, and oversight formally defined and reviewed | Security work is emerging but not yet standardised (R-10) | **High** |
| **IDENTIFY** | Documented asset inventory, data flows, and risk assessments | Architecture and threat model completed via this assessment (03, 04) but not previously documented | **Medium** |
| **PROTECT** | Access controls, data protection, and security technology in place | Partial controls exist; significant gaps in MFA, RBAC enforcement, and session management (R-01, R-02, R-08) | **High** |
| **DETECT** | Continuous monitoring, anomaly detection, and alerting | Monitoring and alerting capabilities are immature or undocumented (R-04) | **High** |
| **RESPOND** | Documented incident response procedures and defined roles | No documented incident response plan | **High** |
| **RECOVER** | Disaster recovery and backup restoration procedures | Backup and restore procedures exist but are undocumented (R-17) | **Medium** |

---

## 9.4 ISO 27001:2022 Gap Analysis

### 9.4.1 Organisational Controls

| Control Area | Expected State | Current State | Gap Severity |
|-------------|---------------|--------------|-------------|
| Information security policies (5.1) | Formal security policies documented and approved | No formal policies documented | **High** |
| Roles and responsibilities (5.3) | Defined security roles with clear accountability | Notable security and privacy roles and responsibilities are unclear | **Medium** |
| Supplier relationships (5.19–5.23) | Third-party risk assessments for payment gateways, email, analytics | Integration logic documented but security terms incomplete (R-06) | **Medium** |
| Access control policy (5.15) | Documented access control policy covering all systems and roles | Access control is partial; policies not formally documented (R-02, R-07) | **High** |
| Secure development lifecycle (5.25) | Secure development policy integrating security into development lifecycle | No documented secure development policy | **High** |

### 9.4.2 People Controls

| Control Area | Expected State | Current State | Gap Severity |
|-------------|---------------|--------------|-------------|
| Security awareness and training (6.3) | All staff complete security awareness training annually | No documented training programme | **High** |
| Background verification (6.1) | Pre-employment security screening for sensitive roles | Not assessed in this review | **Unknown** |
| Disciplinary process (6.4) | Defined disciplinary procedures for security violations | No documented disciplinary process | **Medium** |

### 9.4.3 Technological Controls

| Control Area | Expected State | Current State | Gap Severity |
|-------------|---------------|--------------|-------------|
| Authentication (5.17, 5.18) | Strong authentication including MFA for all privileged access | MFA not implemented for any user type (R-08) | **High** |
| Cryptography (5.23, 5.33) | Strong encryption for data in transit and sensitive data at rest | TLS in transit assumed; data-at-rest encryption not confirmed | **Medium** |
| Secure coding (5.25) | Secure coding standards and code review process | Not documented; dependency scanning not confirmed (R-15) | **High** |
| Logging and monitoring (5.28) | Comprehensive logging of security-relevant events | Logging requirements identified but capabilities immature (R-04) | **High** |
| Network security (5.20) | Network segmentation and security controls documented | WAF not confirmed; cloud network controls unverified (R-16) | **High** |
| Data retention and deletion (5.34) | Documented retention and secure deletion practices | No documented retention or data deletion policy | **Medium** |

---

## 9.5 Australian Privacy Principles (APP) Alignment

As the platform operates in the Australian market sector, alignment with the Australian Privacy Principles is relevant:

| APP | Requirement | Gap Assessment |
|-----|------------|---------------|
| APP 1.4 | Clear, up-to-date privacy policy | Not assessed; policy existence unknown |
| APP 11 | Reasonable steps to protect personal information from misuse, loss, unauthorised access | Significant gaps in access control and monitoring (R-01, R-03, R-04) | **High** |
| APP 11.1–11.2 | Data breach notification | No documented data breach response procedure (R-04, R-18) | **High** |
| APP 13 | Reasonable steps to correct personal information | Not assessed; data correction mechanisms unknown |

---

## 9.6 NIST Privacy Framework Alignment

The NIST Privacy Framework provides a useful lens for data protection governance:

| Privacy Function | Gap Assessment |
|-----------------|---------------|
| **IDENTIFY-P** (Privacy governance) | No documented privacy governance or data inventory | **High** |
| **CONTROL-P** (Data processing consent) | Unclear evidence of privacy consent mechanisms | **Unknown** |
| **PROTECT-P** (Data protection) | Authentication and access controls partially in place but significant gaps (R-01, R-02, R-03) | **High** |
| **INFORM-P** (Notification and transparency) | Privacy policy and notification mechanisms not assessed | **Unknown** |
| **RESPOND-P** (Privacy breach handling) | No documented privacy breach response procedure | **High** |

---

## 9.7 Key Governance Themes

Across all frameworks, four consistent governance themes emerge:

| Theme | Associated Risks | Priority |
|-------|----------------|---------|
| **Policy and governance foundation** | No formal security policies, no documented roles and responsibilities, no privacy governance | **Critical** |
| **Access management maturity** | Weak access control enforcement, no MFA, incomplete session lifecycle, BOLA vulnerabilities | **High** |
| **Security observability and response** | Inadequate logging, no monitoring or alerting, no incident response procedures | **High** |
| **Third-party and supply chain governance** | Weak supplier risk assessment, incomplete payment integration review, undocumented dependency management | **Medium** |

---

## 9.8 Findings Summary

| Finding ID | Governance Theme | Associated Risks (R-IDs) | Severity |
|----------|-----------------|-------------------------|---------|
| G-01 | No formal security policies or governance structure | R-10 | Critical |
| G-02 | Lack of organisational security roles and accountability | R-02, R-07, R-10 | High |
| G-03 | No security awareness training programme | N/A | Medium-High |
| G-04 | No documented secure development lifecycle | R-15 | High |
| G-05 | Weak authentication governance (no MFA policy) | R-08 | High |
| G-06 | No incident response or data breach procedures | R-04, R-18 | High |
| G-07 | Inadequate supplier/third-party governance | R-06 | Medium |
| G-08 | No privacy governance or data protection policy | R-03, R-09 | Medium-High |

---

## 9.9 Recommendations

| Priority | Recommendation | Governance Themes Addressed |
|----------|---------------|----------------------------|
| **Immediate** | Draft and approve core security policies (access control, data protection, logging, incident response) | G-01, G-04, G-06 |
| **Immediate** | Define security roles and responsibilities (Security Owner, Security Champion) | G-02 |
| **Short-term** | Implement MFA for admin and coach accounts; document authentication policy | G-05 |
| **Short-term** | Initiate security awareness training for all staff | G-03 |
| **Short-term** | Draft incident response and data breach notification procedures | G-06 |
| **Short-term** | Review and document supplier security requirements (payment gateway, email, analytics) | G-07 |
| **Medium-term** | Develop and document secure development lifecycle standards | G-04 |
| **Medium-term** | Establish privacy governance programme and data inventory | G-08 |
| **Medium-term** | Conduct a formal ISO 27001 gap audit to identify remaining control gaps | G-01, G-04 |

These findings consolidate all governance-level risks identified across documents **06-auth-and-authorization-review.md** through **08-cloud-and-monitoring-review.md** and align with the risk register in **10-risk-register.md**.

---

## 9.10 Relationship to Other Documents

| Document | Relationship |
|---------|------------|
| **01-project-overview.md** | Defines the project context and audience for the GRC analysis |
| **02-scope-and-assumptions.md** | Documents the governance scope and assumptions used in this analysis |
| **03-architecture-summary.md** | Describes the systems and components evaluated against governance frameworks |
| **04-threat-model.md** | T-1 to T-6 threats inform the governance priorities |
| **05-attack-surface.md** through **08-cloud-and-monitoring-review.md** | Technical findings feed into the GRC gap themes |
| **10-risk-register.md** | R-01 to R-10 are mapped to governance themes G-01 to G-08 |
| **11-remediation-roadmap.md** | GRC findings inform governance track recommendations |
| **12-executive-summary.md** | Summarises this GRC analysis for executive stakeholders |

---

## 9.11 Limitations

This GRC gap analysis is based on technical findings from a security assessment rather than a full audit of governance documents or direct interviews with leadership. A production-grade GRC assessment would require:

- Review of existing security policies, procedures, and training materials.
- Interviews with management and key stakeholders to understand governance priorities.
- Direct examination of supplier contracts and service provider security terms.
- Assessment of the organisation's risk appetite and tolerance levels.
- Formal privacy impact assessment to evaluate data protection compliance.
