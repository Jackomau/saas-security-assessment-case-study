# Anonymous SaaS Security Assessment Case Study

## Overview

This repository presents an anonymised security assessment case study for a mobile-first SaaS marketplace platform connecting coaches and clients.

It is based on real assessment work I completed as part of a supervised university internship, but all names, code and architecture details have been removed or generalised to protect confidentiality.

The purpose of this case study is to demonstrate how I approach end-to-end security assessment for a modern SaaS product, from architecture review and threat modelling through to risk analysis, control mapping and executive reporting.

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

The following files are currently available in the `docs/` folder:

- `01-project-overview.md` – context, goals and purpose of the case study
- `02-scope-and-assumptions.md` – in-scope areas, exclusions and working assumptions
- `03-architecture-summary.md` – high-level system architecture, user roles and trust boundaries
- `04-threat-model.md` – STRIDE-based threat model with representative examples

### Supporting artifacts

Additional supporting material is stored in `artifacts/`:

- `auth-api-security-checklist.md` – anonymised 32-check review checklist covering authentication, API access control, cloud configuration and logging
- `risk-register.csv` – sortable risk register for tracking owner, priority, status and target phase

### Planned next deliverables

The following documents are planned as the next phase of the case study:

- `05-attack-surface.md`
- `06-auth-and-authorization-review.md`
- `07-api-and-data-security-review.md`
- `08-cloud-and-monitoring-review.md`
- `09-grc-gap-analysis.md`
- `10-risk-register.md`
- `11-remediation-roadmap.md`
- `12-executive-summary.md`
- `13-lessons-learned.md`

## Methodology

I followed a structured but lightweight methodology aligned with practical SaaS security assessment work:

1. **Scoping and assumptions**  
   Defined the systems, environments and constraints relevant to the review.

2. **Architecture and asset discovery**  
   Mapped core components, users, trust boundaries and major data flows.

3. **Threat modelling (STRIDE)**  
   Applied STRIDE to key components and interactions to identify plausible threats and attack paths.

4. **Control and configuration review**  
   Reviewed authentication, authorisation, API patterns, secrets handling, logging and monitoring against baseline good practices.

5. **Risk analysis and GRC alignment**  
   Consolidated findings into a risk-oriented view and linked them to control and governance themes relevant to a small SaaS organisation.

6. **Remediation planning and reporting**  
   Grouped recommendations into three implementation phases and prepared both detailed and executive-level summaries for different audiences.

## Key themes

This case study highlights patterns commonly found in early-stage SaaS platforms, including:

- Over-permissive access patterns in selected API routes or internal tooling
- Limited separation of administrative and tenant-level access
- Inconsistent logging and alerting for security-relevant events
- Gaps in operational controls such as access review, key rotation and incident handling

These themes are explored through the threat model, checklist-driven review approach and the planned risk register and remediation roadmap.

## Skills demonstrated

This case study is designed to demonstrate skills relevant to entry-level cyber roles in Australia, including:

- Risk assessment and prioritisation
- Threat modelling using STRIDE
- Secure design and API review
- Cloud and monitoring literacy
- GRC-oriented thinking
- Clear written communication for both technical and non-technical audiences

## Relationship to my experience

This case study is based on work I carried out during a supervised security assessment internship for a real mobile-first SaaS platform completed as part of my Master of Cyber Security.

In the original project I contributed to:

- Reviewing documentation and code in Git-based repositories
- Designing and using structured checklists for auth, API and cloud controls
- Performing threat modelling and risk prioritisation
- Drafting GRC-oriented recommendations and security reporting, including a risk register and remediation plan

All client-specific details have been removed or replaced with generic examples.

The focus of this repository is to demonstrate my process, judgement and communication style rather than expose proprietary information.

## Suggested reading path

If you are a recruiter, hiring manager or interviewer, a suggested reading path is:

1. `README.md`
2. `docs/01-project-overview.md`
3. `docs/03-architecture-summary.md`
4. `docs/04-threat-model.md`
5. `artifacts/auth-api-security-checklist.md`

As the project develops, this reading path will extend to the risk register, remediation roadmap and executive summary.