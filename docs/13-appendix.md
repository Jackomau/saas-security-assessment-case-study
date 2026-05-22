# 13. Appendix

## 13.1 Purpose of this Document

This appendix provides supplementary technical detail that supports the main assessment documentation (**01** through **12**). It includes the complete 32-point security checklist, framework mappings, technical reference data, and a glossary of terms used throughout the assessment.

---

## 13.2 32-Point Security Checklist

The following checklist was developed based on industry best practices and aligns with NIST CSF v2.0 and ISO 27001:2022 controls. Each control is marked with its assessment status.

### Authentication and Access Control

| # | Control | Status | Evidence | Linked Risk |
|---|---------|--------|----------|-------------|
| 1 | Multi-factor authentication (MFA) for all privileged accounts | Not implemented | No MFA configured in auth system | R-01 |
| 2 | MFA for all user accounts | Not implemented | MFA not available | R-01 |
| 3 | Session timeout after inactivity (max 30 min) | Not implemented | Sessions persist indefinitely | R-02 |
| 4 | Session revocation on logout | Not implemented | No session invalidation | R-02 |
| 5 | Role-based access control (RBAC) | Partial | Basic role system, no enforcement | R-03 |
| 6 | Least privilege enforcement | Not implemented | Broad access permissions | R-03 |
| 7 | Account lockout after failed attempts | Not implemented | No lockout policy | R-01 |
| 8 | Password complexity requirements | Not documented | No policy observed | R-01 |
| 9 | Password rotation policy | Not documented | No policy observed | R-01 |
| 10 | Secure password storage (hashing) | Not implemented | Plaintext passwords observed | R-08 |

### Data Protection and Encryption

| # | Control | Status | Evidence | Linked Risk |
|---|---------|--------|----------|-------------|
| 11 | TLS 1.2+ for all data in transit | Not implemented | TLS 1.0/1.1 supported | R-04 |
| 12 | Force HTTPS redirect | Not implemented | HTTP accepted | R-04 |
| 13 | Encryption at rest for databases | Not implemented | RDS unencrypted | R-07 |
| 14 | Encryption at rest for file storage | Not verified | S3 encryption status unknown | R-07 |
| 15 | Encryption at rest for backups | Not verified | Backup encryption not confirmed | R-07 |
| 16 | Data classification scheme | Not implemented | No classification observed | R-07 |
| 17 | PII data handling procedures | Not documented | No procedures observed | R-07 |

### API and Application Security

| # | Control | Status | Evidence | Linked Risk |
|---|---------|--------|----------|-------------|
| 18 | API authentication | Not implemented | No auth on API endpoints | R-09 |
| 19 | API rate limiting | Not implemented | No rate limiting observed | R-06 |
| 20 | Input validation and sanitisation | Not implemented | Accepts arbitrary input | R-05 |
| 21 | Output encoding | Not implemented | No output encoding observed | R-05 |
| 22 | Secure CORS configuration | Not implemented | Wildcard origins (*) allowed | R-05 |
| 23 | Content-Security-Policy headers | Not implemented | No CSP headers observed | R-05 |
| 24 | WAF or equivalent protection | Not implemented | No WAF configured | R-05, R-06 |
| 25 | Security headers (HSTS, X-Frame-Options) | Not implemented | Missing security headers | R-05 |

### Cloud Infrastructure and Monitoring

| # | Control | Status | Evidence | Linked Risk |
|---|---------|--------|----------|-------------|
| 26 | CloudTrail or equivalent logging | Not implemented | CloudTrail not enabled | R-10 |
| 27 | Security event monitoring | Not implemented | No monitoring dashboards | R-10 |
| 28 | Alerting on security events | Not implemented | No alerts configured | R-10 |
| 29 | Secure network segmentation | Partial | Basic VPC, no micro-segmentation | R-03 |
| 30 | Secure key and credential management | Partial | Some secrets in environment variables | R-03 |
| 31 | Regular penetration testing | Not implemented | No pentesting history | - |

### Governance and Compliance

| # | Control | Status | Evidence | Linked Risk |
|---|---------|--------|----------|-------------|
| 32 | Information security policy | Not implemented | No policy document | R-11 |

---

## 13.3 NIST CSF v2.0 Mapping

The assessment findings map to NIST CSF v2.0 functions as follows:

### Identify (ID)

| Assessment Item | NIST CSF Category | Control |
|----------------|-------------------|--------|
| Asset management gaps | ID.AM | Asset inventory, data classification |
| Governance gaps | ID.GV | Security policies, risk management strategy |
| Risk assessment process | ID.RA | Threat modelling, risk register |
| Supply chain risk | ID.SC | Third-party security requirements |

### Protect (PR)

| Assessment Item | NIST CSF Category | Control |
|----------------|-------------------|--------|
| Access control weaknesses | PR.AC | Identity management, MFA, RBAC |
| Data security gaps | PR.DS | Encryption, data classification |
| Platform hardening | PR.PS | Secure configuration management |

### Detect (DE)

| Assessment Item | NIST CSF Category | Control |
|----------------|-------------------|--------|
| No logging/monitoring | DE.AE | Security event monitoring |
| No threat detection | DE.CM | Continuous monitoring |
| No detection processes | DE.DP | Detection process management |

### Respond (RS)

| Assessment Item | NIST CSF Category | Control |
|----------------|-------------------|--------|
| No incident response plan | RS.RP | Incident response procedures |
| No communication procedures | RS.CO | Incident communication |
| No post-incident review | RS.IM | Incident mitigation and improvement |

### Recover (RC)

| Assessment Item | NIST CSF Category | Control |
|----------------|-------------------|--------|
| Recovery planning | RC.RP | Recovery planning and testing |
| Backup procedures | RC.BK | Data backup and restoration |

### Govern (GV)

| Assessment Item | NIST CSF Category | Control |
|----------------|-------------------|--------|
| Policy framework gaps | GV.OC | Organisational cybersecurity context |
| Risk management integration | GV.RM | Cybersecurity risk management integration |

---

## 13.4 ISO 27001:2022 Control Mapping

The assessment findings map to ISO 27001:2022 Annex A controls as follows:

### A.5 Organisational Controls

| Assessment Item | ISO 27001 Control | Status |
|----------------|-------------------|--------|
| No information security policy | A.5.1 | Gap |
| No data classification scheme | A.5.12 | Gap |
| No supplier security requirements | A.5.19 | Gap |
| No security awareness training | A.6.3 | Gap |
| No incident management procedures | A.5.24, A.5.26 | Gap |

### A.7 People Controls

| Assessment Item | ISO 27001 Control | Status |
|----------------|-------------------|--------|
| No awareness training programme | A.6.3 | Gap |
| No disciplinary process for security | A.6.1, A.6.2 | Gap |

### A.8 Physical Controls

| Assessment Item | ISO 27001 Control | Status |
|----------------|-------------------|--------|
| Cloud security (note: AWS managed) | A.7.1-A.7.14 | Partial (cloud provider) |

### A.9 Technological Controls

| Assessment Item | ISO 27001 Control | Status |
|----------------|-------------------|--------|
| MFA not implemented | A.8.2, A.8.3 | Gap |
| Access control weaknesses | A.8.2 | Gap |
| No encryption at rest | A.8.24 | Gap |
| No encryption in transit | A.8.20 | Gap |
| No secure coding practices | A.8.25, A.8.28 | Gap |
| No logging and monitoring | A.8.15, A.8.16 | Gap |
| No vulnerability management | A.8.8 | Gap |
| No network security controls | A.8.20, A.8.21 | Gap |

---

## 13.5 STRIDE Threat Model Reference

| STRIDE Category | Threat ID | Description | DREAD Score |
|----------------|-----------|-------------|-------------|
| Spoofing | T-1a | Credential theft via weak authentication | 7.0 |
| Tampering | T-1b | Data manipulation via API vulnerabilities | 7.4 |
| Repudiation | T-2 | Non-repudiation gaps in audit logging | 5.8 |
| Information Disclosure | T-3 | Data exposure via unencrypted transmission | 6.8 |
| Denial of Service | T-4 | Service disruption via DoS attacks | 6.4 |
| Elevation of Privilege | T-5 | Privilege escalation via RBAC gaps | 6.2 |
| Combined Application | T-6 | Multi-vector attack chain | 7.4 |

---

## 13.6 Risk Scoring Methodology

The risk register uses the following scoring methodologies:

### Probability Scoring

| Score | Description | Likelihood |
|-------|------------|------------|
| 5 | Almost certain | >80% chance within 1 year |
| 4 | Likely | 60-80% chance within 1 year |
| 3 | Possible | 40-60% chance within 1 year |
| 2 | Unlikely | 20-40% chance within 1 year |
| 1 | Rare | <20% chance within 1 year |

### Impact Scoring

| Score | Data | Financial | Operational | Reputational |
|-------|------|-----------|-------------|-------------|
| 5 | Mass PII breach | >AUD $1M loss | >7 days downtime | National media |
| 4 | Significant PII | AUD $250K-$1M | 3-7 days downtime | Industry-wide |
| 3 | Limited PII | AUD $50K-$250K | 1-3 days downtime | Local media |
| 2 | Internal data | AUD $10K-$50K | <1 day downtime | Customer complaints |
| 1 | Non-sensitive | <AUD $10K | Minimal | No impact |

### Risk Priority Calculation

Risk Score = (Probability x Impact) / 10, rounded to 1 decimal place.

| Risk Score | Priority | Action |
|-----------|----------|--------|
| 3.5-5.0 | Critical | Immediate action, executive attention |
| 2.5-3.4 | High | Address within current planning cycle |
| 1.5-2.4 | Medium | Schedule in next planning cycle |
| 0.5-1.4 | Low | Monitor and include in backlog |

---

## 13.7 Glossary of Terms

| Term | Definition |
|------|-----------|
| APP | Australian Privacy Principles - the foundation of the Privacy Act 1988 |
| API | Application Programming Interface - extends platform functionality |
| CORS | Cross-Origin Resource Sharing - web security mechanism |
| CSP | Content Security Policy - prevents XSS and code injection attacks |
| DREAD | Damage, Reproducibility, Exploitability, Affected Users, Discoverability - risk scoring model |
| FTE | Full-Time Equivalent - personnel allocation measure |
| GRC | Governance, Risk and Compliance - organisational security framework |
| HSTS | HTTP Strict Transport Security - enforces HTTPS connections |
| ISO 27001:2022 | International standard for Information Security Management Systems |
| JWT | JSON Web Token - standard for secure authentication |
| KPI | Key Performance Indicator - measurable security metric |
| MFA | Multi-Factor Authentication - additional verification beyond password |
| NIST CSF v2.0 | National Institute of Standards and Technology Cybersecurity Framework |
| OWASP Top 10 | List of most critical web application security risks |
| PII | Personally Identifiable Information - data that identifies individuals |
| RBAC | Role-Based Access Control - access management based on user roles |
| RDS | Amazon Relational Database Service - managed database service |
| SaaS | Software as a Service - cloud-based software delivery model |
| SOC | Security Operations Centre - centralised security monitoring |
| SQLi | SQL Injection - database attack via malicious SQL input |
| STRIDE | Spoofing, Tampering, Repudiation, Information Disclosure, DoS, Elevation of Privilege |
| TLS | Transport Layer Security - cryptographic protocol for secure communication |
| TOTP | Time-based One-Time Password - MFA mechanism |
| WAF | Web Application Firewall - filters and monitors HTTP traffic |
| XSS | Cross-Site Scripting - injection of malicious scripts into web pages |

---

## 13.8 References and Standards

The assessment referenced the following standards and frameworks:

1. **NIST Cybersecurity Framework v2.0** - National Institute of Standards and Technology
2. **ISO/IEC 27001:2022** - International Organization for Standardization
3. **OWASP Top 10 2021** - Open Web Application Security Project
4. **MITRE ATT&CK Framework** - Adversarial Tactics, Techniques, and Common Knowledge
5. **Australian Privacy Principles** - Office of the Australian Information Commissioner
6. **CIS Controls v8** - Center for Internet Security
7. **Cloud Security Alliance Cloud Controls Matrix** - Cloud Security Alliance

---

## 13.9 Document Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2025-12 | Security Assessment Team | Initial appendix documentation |

---

*This appendix supports the complete SaaS security assessment documentation suite. For questions or clarification, refer to the contact details in 01-project-overview.md.*
