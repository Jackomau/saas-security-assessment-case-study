
# Anonymous SaaS Security Assessment Case Study

## 1. Overview

This repository presents an anonymous security assessment case study for a mobile‑first SaaS marketplace platform connecting coaches and clients.  
It is based on real assessment work I completed as part of a supervised university internship, but all names, code and architecture details have been fully anonymised to respect confidentiality.[cite:18][cite:19][cite:74]

The goal of this case study is to demonstrate how I approach end‑to‑end security assessment for a modern SaaS product – from architecture and threat modelling through to risk register, control mapping and executive reporting.

---

## 2. Scenario

The (fictional) platform is a TypeScript‑based SaaS marketplace with:

- Mobile‑first client and coach applications
- Backend APIs for onboarding, booking and messaging
- Third‑party payments integration
- Cloud‑hosted database and storage
- Basic logging and monitoring in the cloud platform

The company is preparing to scale its product and wants a structured view of security risks, gaps against common controls, and a practical remediation roadmap.

---

## 3. Objectives

This case study focuses on five objectives that mirror real client needs:[cite:25][cite:74][web:86][web:92]

1. Understand the current architecture and identify key assets and trust boundaries.  
2. Perform threat modelling to identify likely attack paths against the platform.  
3. Review authentication, authorisation and API/data handling practices.  
4. Assess cloud configuration, logging and monitoring from a security perspective.  
5. Produce a risk register, control‑oriented gap analysis and a remediation roadmap that non‑technical stakeholders can understand.

---

## 4. What this case study covers

The assessment is documented across the `docs/` folder:

- `01-project-overview.md` – context, goals and scope
- `02-scope-and-assumptions.md` – what is in scope vs out of scope
- `03-architecture-summary.md` – high‑level system architecture and data flows
- `04-threat-model.md` – STRIDE‑based threat model with diagrams
- `05-attack-surface.md` – main entry points and exposure
- `06-auth-and-authorization-review.md` – login, sessions, roles and access controls
- `07-api-and-data-security-review.md` – endpoints, input validation and data protection
- `08-cloud-and-monitoring-review.md` – logging, alerting and configuration
- `09-grc-gap-analysis.md` – mapping to baseline controls and policies
- `10-risk-register.md` – consolidated risk list with ratings and owners
- `11-remediation-roadmap.md` – 30/60/90‑day remediation plan
- `12-executive-summary.md` – 1‑page overview for non‑technical readers
- `13-lessons-learned.md` – what I learned and would do differently next time

Supporting artefacts such as CSV risk registers, control mapping spreadsheets and sample findings templates live under `artifacts/`.[cite:74][web:86][web:92]

---

## 5. Methodology

I followed a structured but lightweight methodology aligned with how SaaS security assessments are performed in practice:[web:86][web:92]

1. **Scoping and assumptions**  
   Clarified what systems, environments and timeframes were in scope, and documented key assumptions and constraints.

2. **Architecture and asset discovery**  
   Mapped core components, data stores, data flows, user roles and trust boundaries into simple diagrams.

3. **Threat modelling (STRIDE)**  
   Applied STRIDE to key components and flows to identify plausible threats and attack paths, then prioritised by likelihood and impact.[cite:75][cite:78]

4. **Control and configuration review**  
   Assessed authentication, authorisation, API patterns, secrets management, logging and monitoring against common good practices and baseline control expectations.[cite:18][cite:74]

5. **Risk register and GRC gap analysis**  
   Consolidated findings into a risk register, mapped them to governance and control themes, and highlighted gaps that matter most for a small SaaS business.[cite:18][cite:24]

6. **Remediation roadmap & reporting**  
   Grouped recommendations into 30/60/90‑day milestones and created both detailed and executive‑level summaries for different audiences.

---

## 6. Key findings (summarised)

This case study highlights patterns commonly found in early‑stage SaaS platforms, such as:

- Over‑permissive access patterns for certain API routes and internal tooling
- Limited segmentation of admin functions and tenant data access
- Inconsistent logging and alerting for security‑relevant events
- Gaps in formal policies around access reviews, key rotation and incident response

The detailed findings and recommendations are documented in the risk register, control mapping and remediation roadmap files.

---

## 7. Skills demonstrated

This case study is designed to demonstrate skills that are directly relevant to entry‑level cyber roles in Australia:[web:46][web:48][web:50][web:85]

- **Risk assessment:** converting technical issues into structured risks with likelihood, impact and priority.  
- **Threat modelling:** using STRIDE and data‑flow diagrams to think about attacker paths.  
- **Secure design review:** assessing auth, API and data handling patterns in a modern SaaS stack.[cite:18][cite:78]  
- **Cloud & monitoring literacy:** understanding why logs, audit trails and alerting matter for detection and investigation.[cite:26][cite:74]  
- **GRC mindset:** linking technical findings to control frameworks and governance concepts.[cite:24][cite:22]  
- **Communication:** producing both detailed technical notes and concise executive‑style summaries.

---

## 8. How this relates to my experience

This case study is based on work I carried out during a supervised security assessment internship for a real mobile‑first SaaS marketplace, completed as part of my Master of Cyber Security.[cite:16][cite:18][cite:25]  
In the original project I contributed to:

- Reviewing documentation and code in Git‑based repositories  
- Designing and using structured checklists for auth/API/cloud controls  
- Performing threat modelling and risk prioritisation  
- Drafting GRC‑oriented recommendations and security reports, including a risk register and remediation plan.[cite:18][cite:19][cite:74]

All client‑specific details have been removed or replaced with generic examples.  
The focus here is on demonstrating my process and judgement, not on exposing proprietary information.

---

## 9. How to read this repo

If you are a recruiter or hiring manager, a suggested reading path is:

1. This `README.md`  
2. `docs/01-project-overview.md` and `docs/03-architecture-summary.md`  
3. `docs/04-threat-model.md`  
4. `docs/10-risk-register.md` and `docs/11-remediation-roadmap.md`  
5. `docs/12-executive-summary.md`

I’m happy to walk through this case study in an interview and explain what I would do next as the platform and organisation mature.

---
