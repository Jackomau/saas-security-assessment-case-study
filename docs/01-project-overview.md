# 1. Project Overview

## 1.1 Purpose of this case study

This document provides an overview of an anonymised security assessment for a mobile‑first SaaS marketplace platform.  
The goal is to show, in a transparent way, how I approach end‑to‑end security assessment for a modern cloud application – from scoping and architecture understanding through to threat modelling, risk register and remediation planning.

The case study is based on work I carried out as part of a supervised university internship for a real SaaS startup.  
All client‑specific names, code and configuration details have been removed or generalised to protect confidentiality, while preserving the assessment approach and reasoning.

This project is intentionally written to be understandable both for technical reviewers and for recruiters or hiring managers evaluating candidates for graduate and junior cyber roles.

---

## 1.2 Scenario: the platform at a glance

The fictional platform in this case study is a mobile‑first SaaS marketplace that connects independent coaches with clients.  
At a high level, it:

- Provides mobile and web interfaces for clients to discover, book and message coaches.  
- Uses a TypeScript‑based stack with a single‑page frontend and API backend.  
- Integrates with a third‑party payments provider to handle payouts and fees.  
- Stores user profiles, bookings and messaging history in a cloud‑hosted database.  
- Relies on cloud platform services for authentication, storage and monitoring.

The company is preparing to scale beyond a small pilot and wants to understand its security posture, key risks and practical next steps before onboarding more users and partners.

---

## 1.3 Assessment objectives

The assessment was designed with five core objectives in mind:

1. **Understand the architecture and assets**  
   Build a clear picture of the system components, data flows, user roles and trust boundaries.

2. **Identify threats and likely attack paths**  
   Apply structured threat modelling (STRIDE) to the architecture to identify realistic threats and abuse scenarios

3. **Review controls around access, data and cloud configuration**  
   Examine authentication and authorisation, API patterns, data handling, secrets management, logging and monitoring.

4. **Translate findings into a risk‑oriented view**  
   Summarise issues in a risk register with likelihood, impact and priority ratings instead of only technical checklists.

5. **Provide a phased remediation plan and clear reporting**  
   Group recommendations into a three‑phase remediation plan and create both detailed and executive‑level summaries for different audiences.

---

## 1.4 In scope and out of scope (summary)

A detailed scope and assumptions list is provided in `02-scope-and-assumptions.md`.  
At a high level, the case study focuses on:

- Application architecture and data flows  
- Authentication and authorisation flows for core user types  
- Public and authenticated API endpoints and data access patterns  
- Cloud configuration at the application layer (logging, monitoring, secrets usage)  
- Governance and control themes relevant to a small SaaS organisation

Explicitly out of scope for this case study are:

- Deep host‑level hardening of underlying infrastructure managed by cloud providers  
- Full penetration testing or exploit development  
- Social engineering and phishing simulations  
- Detailed legal and regulatory interpretation beyond high‑level alignment

These scoping decisions reflect typical constraints in time‑boxed SaaS security reviews and help keep the assessment focused on areas where the organisation has meaningful control.

---

## 1.5 Method at a high level

The assessment followed a simple, repeatable sequence:

1. **Discover and document** – review existing documentation, draw diagrams, list assets and data flows.  
2. **Model threats** – apply STRIDE to key components and flows to identify what could go wrong.  
3. **Inspect controls** – review how the platform currently authenticates users, authorises actions, protects data and logs activity. 
4. **Assess risks** – estimate likelihood and impact, and document findings in a risk register with clear descriptions and owners. 
5. **Plan remediation** – group recommendations into three phases, balancing risk reduction with practicality for a small team.  
6. **Communicate results** – prepare both technical notes and an executive summary so different stakeholders can act on the findings.

---

## 1.6 How this relates to my experience

This case study mirrors work I performed in a supervised internship, where I:

- Reviewed documentation and code in Git‑based repositories for a SaaS startup.  
- Contributed to threat modelling, auth and API review, and cloud/monitoring observations.  
- Helped draft a risk register, remediation suggestions and security reporting for stakeholders.  

The purpose of this document is not to expose any client information, but to provide a transparent view of my assessment process and how I think about security, risk and communication in a SaaS context.

---