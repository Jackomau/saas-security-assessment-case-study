# 8. Cloud and Monitoring Review

## 8.1 Purpose of this document

This document reviews the cloud infrastructure security posture and operational monitoring maturity of the anonymised SaaS marketplace platform. It evaluates how the platform leverages cloud services, manages infrastructure security, and implements observability for security operations.

The review is based on the architectural descriptions in **03-architecture-summary.md**, the attack surface analysis in **05-attack-surface.md**, the data security review in **07-api-and-data-security-review.md**, and the 32-point security checklist in **artifacts/auth-api-security-checklist.md**.

The goal is to identify gaps in cloud configuration, infrastructure hardening, and monitoring capabilities and provide actionable recommendations.

---

## 8.2 Cloud Infrastructure Review

### 8.2.1 Cloud Provider and Architecture Context

The platform is assumed to run on a major cloud provider (e.g., AWS or equivalent) with a serverless or container-based architecture. The modern TypeScript stack suggests deployment via services such as AWS Lambda, ECS/Fargate, or similar managed compute.

| Component | Assumed Implementation | Security Consideration |
|-----------|----------------------|----------------------|
| Compute | Serverless functions or containers | Patch management handled by provider; runtime security still required |
| API Gateway | Managed gateway service | Rate limiting, authentication, WAF attachment points |
| Database | Managed RDS/serverless (PostgreSQL or equivalent) | Encryption at rest; network isolation; backup encryption |
| Object Storage | Private S3-compatible buckets | Bucket policies; signed URLs; access logging |
| CDN | Cloud CDN for static assets | Cache headers; WAF at edge |

### 8.2.2 Cloud Configuration Security

| Checklist Item | Review Finding | Status |
|---------------|---------------|-------|
| CLOUD-01: No hard-coded secrets | Risk of secrets in source or config files | Needs verification |
| CLOUD-02: Environment files excluded from version control | Should use .gitignore and secret scanning | Needs verification |
| CLOUD-03: Cloud roles use least privilege | Application IAM roles should be scoped to required services only | Assumed adequate but requires audit |
| CLOUD-04: Storage buckets not publicly readable | Default-private buckets with signed URLs for access | Needs configuration verification |
| CLOUD-05: Outbound connections documented | Third-party integrations (payments, email) should use authenticated channels | Assumed adequate |
| CLOUD-06: Backup and restore procedures defined | Database backups and disaster recovery should exist | Needs documentation |

**Key findings:**
- The platform likely benefits from the cloud provider's shared responsibility model: physical security, hypervisor, and much of the infrastructure are managed by the provider.
- However, the application layer (code, data, authentication, APIs) remains the customer's responsibility. The findings in documents 06 and 07 identify the primary risks in this layer.

### 8.2.3 Network Security Controls

| Control | Assessment |
|---------|-----------|
| VPC/network isolation | The application should run in isolated virtual networks with no direct public database access |
| Security groups / firewalls | Inbound rules should permit only API gateway traffic to compute; database should only accept connections from application tier |
| WAF (Web Application Firewall) | Should be enabled at the API gateway or edge layer to filter common attacks (SQLi, XSS, path traversal) |
| DDoS protection | Cloud provider DDoS mitigation (e.g., AWS Shield Standard) provides baseline protection; cascading failures should still be considered |
| TLS/HTTPS enforcement | All traffic should use TLS 1.2+; HTTP should redirect to HTTPS |
| Internal service communication | If microservices exist, internal traffic should use private networking (not public endpoints) |

**Key findings:**
- The absence of a documented WAF configuration in the architecture suggests this may not be currently enabled. This is a gap for a production platform handling authentication and financial data.
- Public-facing endpoints (login, registration, search) are particularly exposed without WAF protection.

---

## 8.3 Monitoring and Observability Review

### 8.3.1 Logging Architecture

| Log Source | Current State | Recommendation |
|-----------|--------------|----------------|
| Application logs | Assumed: structured JSON logs to stdout | Centralise via cloud logging service; retain 90+ days |
| Authentication events | Assumed: logged within app | Ship to dedicated security log stream |
| API gateway logs | Should be enabled | Retain access logs with request IDs for traceability |
| Database logs | Should include slow queries and errors | Monitor for unusual query patterns |
| Cloud infrastructure logs | CloudTrail or equivalent should be enabled | Retain for compliance and forensic purposes |

### 8.3.2 Security Monitoring Capabilities

| Capability | Current State | Gap |
|------------|--------------|-----|
| SIEM or log aggregation | Unclear if implemented | Need centralised log collection and correlation |
| Alerting on security events | Basic threshold alerting may exist | Need specific alerts for failed logins, role changes, payout events (LOG-01, LOG-02) |
| Anomaly detection | Likely absent | Consider ML-based anomaly detection for user behaviour |
| Dashboard and visibility | Unclear | Need security operations dashboard for real-time threat visibility |
| Incident response workflow | Unclear if documented | Need documented runbooks for common security incidents |

### 8.3.3 Monitoring Metrics and Alerting

Drawing from the metrics defined in 7.4.2, the platform should implement:

| Metric | Collection Method | Alert Destination |
|--------|------------------|--------------------|
| Failed login rate | Application log aggregation | Security team (email/Slack) |
| API error rate (5xx) | API gateway metrics | DevOps team (PagerDuty/Slack) |
| New registration spike | Application log filtering | Security team |
| Search API query volume | API gateway logs | Security team |
| Admin endpoint access | Application auth logs | Security team (immediate) |
| Unusual data export patterns | Database audit logs | Security team |
| Session token anomalies | Token validation failure logs | Security team |

### 8.3.4 Log Retention and Access Control

| Policy | Recommendation |
|--------|---------------|
| Application and API logs | 90 days minimum; longer for security-relevant events |
| Authentication logs | 1 year minimum; support for incident investigation and compliance |
| Cloud infrastructure logs | 1+ year (e.g., CloudTrail via S3 with lifecycle policies) |
| Log access | Restrict to security team and authorised operations personnel; enforce MFA for log access |
| Log integrity | Use write-once or append-only storage to prevent tampering |

---

## 8.4 Dependency and Software Supply Chain Review

### 8.4.1 Dependency Management

| Control | Assessment |
|---------|-----------|
| Dependency scanning | CI/CD should include SCA (Software Composition Analysis) tools |
| Vulnerability monitoring | Automated alerts for new CVEs in dependencies |
| Dependency pinning | Use lockfiles (package-lock.json) to ensure reproducible builds |
| Third-party library vetting | Prefer well-maintained, widely-used libraries |
| License compliance | Track open-source license obligations |

**Key findings:**
- A TypeScript/Node.js application is particularly exposed to supply chain risks due to the large number of npm dependencies.
- Recent supply chain attacks (e.g., compromised packages, typosquatting) make automated dependency scanning essential.

### 8.4.2 CI/CD Security

| Control | Assessment |
|---------|-----------|
| Secret scanning in CI | CI pipelines should reject commits containing secrets |
| Build reproducibility | Build artifacts should be reproducible from source |
| Deployment access control | Only authorised personnel should trigger production deployments |
| Rollback capability | Infrastructure should support rapid rollback of bad deployments |
| Environment separation | Development, staging, and production should be isolated |

---

## 8.5 Findings Summary

| Finding ID | Description | Checklist Items | Severity |
|----------|-------------|----------------|---------|
| F-13 | WAF not confirmed as enabled; public-facing endpoints unprotected | CLOUD-04, LOG-05 | High |
| F-14 | Monitoring and alerting capabilities undefined or immature | LOG-01, LOG-02, LOG-05, LOG-06 | Medium |
| F-15 | Dependency security scanning not documented | N/A | Medium |
| F-16 | Cloud credential and IAM configurations unverified | CLOUD-01, CLOUD-03 | Medium |
| F-17 | Backup and disaster recovery procedures undocumented | CLOUD-06 | Medium |
| F-18 | Incident response procedures not documented | LOG-05 | Medium |

---

## 8.6 Recommendations

| Priority | Recommendation | Addresses |
|----------|---------------|----------|
| **Immediate** | Confirm WAF is enabled on API gateway or edge layer | F-13 |
| **Immediate** | Audit cloud IAM roles, storage bucket policies, and secret configurations | F-16 |
| **Short-term** | Implement structured logging centralised in a cloud logging service with retention policies | F-14 |
| **Short-term** | Deploy security-focused alerting (failed logins, admin access, payout events) | F-14 |
| **Short-term** | Integrate dependency scanning (SCA) into CI/CD pipeline | F-15 |
| **Short-term** | Document and test backup and restore procedures | F-17 |
| **Medium-term** | Build a security operations dashboard with real-time visibility | F-14 |
| **Medium-term** | Develop incident response runbooks for common security scenarios | F-18 |

These findings complement the logging review in **07-api-and-data-security-review.md** (Section 7.4) and feed into the risk register in **10-risk-register.md** and remediation roadmap in **11-remediation-roadmap.md**.

---

## 8.7 Relationship to Other Documents

| Document | Relationship |
|---------|------------|
| **03-architecture-summary.md** | Describes the cloud infrastructure components and deployment model |
| **04-threat-model.md** | T-5 (availability) and T-6 (operational security) threats relate to cloud and monitoring gaps |
| **05-attack-surface.md** | Section 5.3.4 covers network-facing infrastructure as an attack surface |
| **06-auth-and-authorization-review.md** | LOG-01 refers to authentication event logging covered in that document |
| **07-api-and-data-security-review.md** | LOG-01 through LOG-06 and CLOUD-01 through CLOUD-06 cross-reference here |
| **artifacts/auth-api-security-checklist.md** | Sections C and D of the checklist are fully mapped in this document |
| **10-risk-register.md** | Findings F-13 through F-18 map to risk register entries |
| **11-remediation-roadmap.md** | Recommendations feed into implementation phases |

---

## 8.8 Limitations

This review is based on architectural documentation and checklist-based assessment rather than direct cloud console access or live monitoring configuration review. A production-grade audit would require:

- Access to cloud console to verify IAM policies, bucket configurations, and network security groups.
- Review of actual CloudTrail (or equivalent) logs to confirm logging coverage.
- Validation of alerting rules and dashboard configurations.
- Code review of CI/CD pipeline configurations for security controls.
- Verification of backup restoration through a test restore procedure.
