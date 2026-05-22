# 11. Remediation Roadmap

## 11.1 Purpose of this Document

This document consolidates all security findings from the technical assessment (**04** through **08**) and governance analysis (**09**) into a prioritised, time-bound remediation plan. Each recommendation is assigned a priority level, estimated effort, target timeframe, and mapped to the risk register entries established in **10-risk-register.md**.

The roadmap is structured to enable the platform engineering and security teams to implement improvements incrementally while maintaining operational continuity.

---

## 11.2 Remediation Priorities

Priority levels are defined as follows:

| Priority | Risk Level | Description | Target Timeframe |
|----------|-----------|-------------|------------------|
| P1 | Critical | Immediate action required; active exploit or severe impact likely | 0--2 weeks |
| P2 | High | Significant risk; should be addressed in current sprint cycle | 2--4 weeks |
| P3 | Medium | Important improvement; schedule in next planning cycle | 1--3 months |
| P4 | Low | Best practice; include in ongoing improvement backlog | 3--6 months |

---

## 11.3 Consolidated Remediation Actions

### 11.3.1 Authentication and Authorization (from 06)

| Ref | Action | Priority | Linked Risk | Effort | Owner |
|-----|--------|----------|-------------|--------|--------|
| A-01 | Implement TOTP-based MFA for all admin and coach accounts | P1 | R-01 | 1 week | Platform Eng |
| A-02 | Enforce MFA at login for all user roles | P2 | R-01 | 2 weeks | Platform Eng |
| A-03 | Implement session revocation API and invalidate existing sessions | P1 | R-02 | 1 week | Platform Eng |
| A-04 | Reduce session timeout to 30 minutes of inactivity | P2 | R-02 | 2 days | Platform Eng |
| A-05 | Implement role-based access control (RBAC) with least-privilege enforcement | P2 | R-03 | 3 weeks | Platform Eng |
| A-06 | Remove direct RDS access; enforce role-based database permissions | P2 | R-03 | 1 week | DevOps |
| A-07 | Document and enforce password policy (min 12 chars, complexity, rotation) | P3 | R-01 | 1 week | Security |
| A-08 | Implement account lockout after 5 failed login attempts | P3 | R-01 | 3 days | Platform Eng |

### 11.3.2 API and Data Security (from 07)

| Ref | Action | Priority | Linked Risk | Effort | Owner |
|-----|--------|----------|-------------|--------|--------|
| D-01 | Enforce HTTPS-only traffic; redirect HTTP to HTTPS at load balancer | P1 | R-04 | 2 days | DevOps |
| D-02 | Disable TLS 1.0 and 1.1; enforce TLS 1.2+ only | P1 | R-04 | 2 days | DevOps |
| D-03 | Implement and enforce Content-Security-Policy header across all endpoints | P2 | R-05 | 1 week | Platform Eng |
| D-04 | Restrict CORS to specific trusted domains; remove wildcard (*) origins | P1 | R-05 | 3 days | Platform Eng |
| D-05 | Implement rate limiting (100 req/min per IP) on all API endpoints | P2 | R-06 | 1 week | Platform Eng |
| D-06 | Enable AES-256 encryption for data at rest on RDS | P2 | R-07 | 2 weeks | DevOps |
| D-07 | Hash all stored passwords using bcrypt or Argon2 (min cost factor 12) | P1 | R-08 | 1 week | Platform Eng |
| D-08 | Implement API authentication (API keys or JWT) for all endpoints | P2 | R-09 | 2 weeks | Platform Eng |
| D-09 | Enable WAF rules for SQLi, XSS, and common OWASP Top 10 patterns | P2 | R-05, R-06 | 1 week | DevOps |
| D-10 | Implement input validation and output encoding on all user-facing forms | P2 | R-05 | 2 weeks | Platform Eng |

### 11.3.3 Cloud Infrastructure and Monitoring (from 08)

| Ref | Action | Priority | Linked Risk | Effort | Owner |
|-----|--------|----------|-------------|--------|--------|
| C-01 | Enable AWS CloudTrail with log delivery to S3 with encryption | P1 | R-10 | 3 days | DevOps |
| C-02 | Implement log analysis and alerting on failed authentication events | P2 | R-10 | 1 week | DevOps/Sec |
| C-03 | Enable S3 bucket encryption (SSE-S3 or SSE-KMS) | P2 | R-07 | 2 days | DevOps |
| C-04 | Implement CloudWatch dashboards for key security metrics | P3 | R-10 | 1 week | DevOps |
| C-05 | Set up SNS alerts for security-relevant CloudTrail events | P3 | R-10 | 3 days | DevOps |

### 11.3.4 Governance and Policy (from 09)

| Ref | Action | Priority | Linked Risk | Effort | Owner |
|-----|--------|----------|-------------|--------|--------|
| G-01 | Draft and approve an information security policy | P3 | R-11 | 2 weeks | Management |
| G-02 | Define and document incident response procedures | P3 | R-06 | 2 weeks | Security |
| G-03 | Establish a data classification scheme (public, internal, confidential, PII) | P3 | R-07 | 2 weeks | Security/Legal |
| G-04 | Conduct security awareness training for all staff | P4 | R-11 | 1 month | HR/Security |
| G-05 | Implement a formal vulnerability management process (scan, assess, patch, verify) | P3 | R-06 | 3 weeks | Security |
| G-06 | Document supplier security requirements for third-party integrations | P4 | R-07 | 2 weeks | Procurement/Security |
| G-07 | Establish privacy governance programme aligned with APP (Australia) | P4 | R-07 | 1 month | Legal/Security |

---

## 11.4 Implementation Timeline

### Phase 1: Critical Fixes (Weeks 1--2)

Focus: Eliminate active risks that could lead to immediate compromise.

- **Week 1:**
  - D-01: Enforce HTTPS-only
  - D-02: Disable legacy TLS versions
  - A-03: Session revocation
  - C-01: Enable CloudTrail
  - A-01: Implement TOTP MFA for admin/coach accounts

- **Week 2:**
  - D-07: Hash passwords with bcrypt/Argon2
  - D-04: Restrict CORS
  - G-01: Draft information security policy
  - D-09: Enable WAF rules

### Phase 2: High-Priority Improvements (Weeks 3--6)

Focus: Address significant security gaps with measurable risk reduction.

- **Weeks 3--4:**
  - A-05: Implement RBAC
  - D-06: Enable RDS encryption at rest
  - D-08: API authentication
  - D-05: Rate limiting
  - C-03: S3 bucket encryption

- **Weeks 5--6:**
  - A-02: MFA for all users
  - A-04: Session timeout reduction
  - D-10: Input validation
  - G-02: Incident response procedures
  - G-05: Vulnerability management process

### Phase 3: Medium-Term Enhancements (Months 2--3)

Focus: Strengthen security posture and governance framework.

- Implement comprehensive logging and alerting (C-02, C-04, C-05)
- Establish data classification and privacy governance (G-03, G-07)
- Document supplier security requirements (G-06)
- Conduct security awareness training (G-04)
- Account lockout and password policy enforcement (A-07, A-08)

### Phase 4: Ongoing Improvement (Months 4--6)

Focus: Continuous security improvement and compliance maturity.

- Regular security assessments and penetration testing
- Security metrics reporting to management
- Policy review and update cycles
- Staff recruitment for dedicated security roles
- Preparation for formal ISO 27001 certification audit

---

## 11.5 Resource Requirements

### 11.5.1 Personnel

| Role | Allocation | Duration |
|------|-----------|----------|
| Platform Engineer | 1 FTE | 6 weeks (Phases 1--2) |
| DevOps Engineer | 0.5 FTE | 6 weeks (Phases 1--2) |
| Security Lead | 0.5 FTE | 12 weeks (Phases 1--3) |
| Legal/Compliance | 0.25 FTE | 8 weeks (Phases 2--3) |
| Management Sponsor | 0.1 FTE | 12 weeks (Oversight) |

### 11.5.2 Budget Estimates

| Category | Estimated Cost | Notes |
|----------|---------------|-------|
| Engineering time | AUD $45,000--65,000 | Based on 1.75 FTE for 6 weeks |
| Security tooling | AUD $5,000--15,000 | WAF, vulnerability scanner, MFA solution |
| Training | AUD $2,000--5,000 | Staff security awareness programme |
| External audit/prep | AUD $10,000--25,000 | ISO 27001 readiness assessment |
| Contingency (15%) | AUD $9,000--16,500 | Unforeseen issues |
| **Total estimated range** | **AUD $71,000--126,500** | |

---

## 11.6 Success Criteria and KPIs

| KPI | Baseline | Target | Measurement Method |
|-----|----------|--------|--------------------|
| MFA adoption rate | 0% | 100% admin/coach; 80% all users | Auth system logs |
| Encrypted data at rest | Partial | 100% databases and storage | Cloud configuration audit |
| Failed login lockout rate | None | < 1% legitimate users | Auth logs |
| Mean time to detect (MTTD) | Not measured | < 1 hour for critical events | SOC/CloudWatch metrics |
| Policy coverage | None | 100% of APP mandatory controls | Policy gap analysis |
| Staff security training completion | 0% | 90% within 3 months | LMS/training records |
| Open critical vulnerabilities | Unknown | 0 | Vulnerability scan reports |

---

## 11.7 Relationship to Other Documents

| Document | Relationship |
|----------|-------------|
| **04-threat-model.md** | T-1 to T-6 threats inform remediation priorities |
| **05-attack-surface.md** | Attack surface items map to specific remediation actions |
| **06-auth-and-authorization-review.md** | A-01 to A-08 directly address auth findings |
| **07-api-and-data-security-review.md** | D-01 to D-10 directly address API/data findings |
| **08-cloud-and-monitoring-review.md** | C-01 to C-05 directly address cloud findings |
| **09-grc-gap-analysis.md** | G-01 to G-07 directly address governance gaps |
| **10-risk-register.md** | R-01 to R-11 risks are the basis for remediation prioritisation |
| **12-executive-summary.md** | Summarises key roadmap milestones and resource requirements |

---

## 11.8 Limitations

This remediation roadmap is based on findings from a technical security assessment conducted over a defined engagement period. The following should be noted:

1. **Assessment scope:** The roadmap reflects the platform architecture and security controls as observed during the assessment. Changes to the platform after the assessment may introduce new risks not captured here.

2. **Resource dependencies:** timelines and effort estimates assume availability of appropriately skilled personnel. Competing priorities or resource constraints may extend implementation.

3. **Third-party dependencies:** Some remediations (e.g., MFA integration, WAF configuration) may depend on third-party service availability or vendor support timelines.

4. **Evolving threat landscape:** The threat environment is dynamic. New vulnerabilities and attack techniques may emerge requiring additional remediation actions beyond those identified here.

5. **Business context:** Priorities are technical risk-based. Business constraints, regulatory deadlines, or commercial considerations may require re-prioritisation in consultation with stakeholders.

