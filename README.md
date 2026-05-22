# Anonymous SaaS Security Assessment Case Study

## Overview

This repository presents an anonymised security assessment case study for a mobile-first SaaS marketplace platform connecting coaches and clients. It demonstrates an end-to-end security assessment process, including architecture review, threat modelling, risk analysis, control mapping, and executive reporting.

It is based on real assessment work I completed as part of a supervised university internship, but all names, code and architecture details have been removed or generalised to protect confidentiality.

This portfolio project is targeted at graduate and junior roles in:

- Cyber risk and GRC
- Security analyst / security operations
- Security assessment and advisory for SaaS and cloud platforms

## Scenario

The fictional platform in this case study is a TypeScript-based SaaS marketplace with:

- Mobile-first client and coach applications
- Backend APIs for onboarding, booking and messaging
- Third-party payments integration
- Cloud-hosted database and storage
- Basic logging and monitoring in the cloud platform

The company is preparing to scale its product and needs a structured view of security risks, key control gaps and a practical remediation plan.

## Objectives

This case study focuses on five objectives that mirror real client needs:

1. Understand the architecture and identify key assets and trust boundaries.
2. Perform threat modelling to identify likely attack paths against the platform.
3. Review authentication, authorisation and API/data handling practices.
4. Assess cloud configuration, logging and monitoring from a security perspective.
5. Produce a risk register, control-oriented gap analysis and a remediation plan that non-technical stakeholders can understand.

## Repository structure

### Current documentation

The following files are available in the `docs/` folder:

- `01-project-overview.md` - context, goals and purpose of the case study
- `02-scope-and-assumptions.md` - in-scope areas, exclusions and working assumptions
- `03-architecture-summary.md` - high-level system architecture, user roles and trust boundaries
- `04-threat-model.md` - STRIDE-based threat model with representative examples
- `05-attack-surface.md` - analysis of external and internal attack surfaces
- `06-auth-and-authorization-review.md` - authentication and access control assessment
- `07-api-and-data-security-review.md` - API design and data handling assessment
- `08-cloud-and-monitoring-review.md` - cloud infrastructure and observability assessment
- `09-grc-gap-analysis.md` - governance, risk and compliance gap analysis against ISO 27001 / SOC 2 themes
- `10-risk-register.md` - prioritised list of identified risks with treatment actions
- `11-remediation-roadmap.md` - phased remediation plan with estimated effort and cost
- `12-executive-summary.md` - high-level findings and strategic recommendations for leadership
- `13-appendix.md` - supporting material including a 32-point security checklist, framework mappings and reference data

### Supporting artifacts

Additional supporting material is stored in `artifacts/`:

- `auth-api-security-checklist.md` - anonymised 32-check review checklist covering authentication, API access control, cloud configuration and logging
- `risk-register.csv` - sortable risk register for tracking owner, priority, status and target phase

### Planned next deliverables

Future phases may include:

- Detailed penetration testing methodology overview
- Incident response tabletop exercise scenarios
- SOC analyst runbook for common alert types
- Expanded compliance mapping (NIST CSF v2.0 controls, Essential Eight mitigation strategies)
- Redacted evidence screenshots for key technical findings

## Methodology

The assessment follows a six-step process:

1. Scoping and assumptions.
2. Architecture and asset discovery.
3. Threat modelling (STRIDE).
4. Control and configuration review.
5. Risk analysis and GRC alignment.
6. Remediation planning and reporting.

## Key themes

The case study addresses common early-stage SaaS security issues:

- Over-permissive API and internal access.
- Poor separation of administrative and tenant-level access.
- Inconsistent logging and alerting.
- Gaps in operational controls (access review, key rotation, incident handling).

## Confidentiality check

All 16 files in this repository have been reviewed to ensure no client-specific confidential information is present:

- **Client/company names** - None present. The platform is referred to generically as "the platform" or "a SaaS marketplace".
- **Real credentials, API keys or secrets** - None present. All references to secrets are generic best-practice descriptions.
- **Real domain names, IP addresses or infrastructure identifiers** - None present.
- **Personal identifiable information (PII)** - None present.
- **Real budget or financial data tied to a specific client** - None present. Cost estimates are generic ranges.
- **Employee names or internal system names** - None present.

All client-specific names, code, configuration details and architecture identifiers have been removed or replaced with generic examples to protect confidentiality.

## Skills demonstrated

- Risk assessment and prioritisation.
- STRIDE threat modelling.
- Secure design and API review.
- Cloud and monitoring literacy.
- GRC-oriented thinking and professional reporting.
- Stakeholder communication across technical and non-technical audiences.

## Relationship to experience

This project is an anonymised portfolio version of a supervised security assessment internship completed during a Master of Cyber Security.

## Suggested reading path

1. `README.md`
2. `docs/01-project-overview.md`
3. `docs/02-scope-and-assumptions.md`
4. `docs/03-architecture-summary.md`
5. `docs/04-threat-model.md`
6. `docs/12-executive-summary.md`
7. `artifacts/auth-api-security-checklist.md`
