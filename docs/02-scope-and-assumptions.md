# 2. Scope and Assumptions

## 2.1 Purpose of this document

This document defines what was considered in scope and out of scope for the SaaS security assessment case study, as well as the key assumptions made during the work.  
Being explicit about scope and assumptions helps keep the assessment focused and makes it easier for stakeholders to understand what the results do – and do not – cover.

---

## 2.2 Assessment scope – in scope

The assessment focuses on the application and cloud components that the SaaS team can realistically influence.  
At a high level, the following areas are considered **in scope**:

### 2.2.1 Application functionality

- Core user journeys for clients and coaches (sign‑up, login, booking, messaging, account management).  
- Web and mobile application flows at the level of architecture and data movement (not detailed UX testing).  
- Business logic related to bookings, payments, and access to client / coach data.

### 2.2.2 Backend services and APIs

- Public and authenticated HTTP APIs exposed to clients and coaches.  
- Internal API routes used by the frontend and mobile applications.  
- Input validation, authorisation checks and error handling behaviours where they can be inferred from documentation or representative code examples.

### 2.2.3 Authentication and authorisation

- User authentication flows (e.g. email/password, token‑based login, session handling).  
- Role‑based access control (RBAC) and separation between client, coach and admin capabilities.  
- Account recovery and session management at a conceptual level.

### 2.2.4 Data handling and storage

- Handling of personal data related to clients and coaches, including basic profile information and booking details.  
- High‑level treatment of sensitive data such as payment‑related identifiers or tokens, with the understanding that the payment processor handles card data directly.  
- Use of encryption at rest and in transit where this is visible from configuration or documentation.

### 2.2.5 Cloud configuration, logging and monitoring

- Application‑level use of cloud services such as managed databases, storage and serverless functions.  
- Logging and monitoring practices relevant to security events (log sources, retention, alerting at a conceptual level).  
- Secrets usage patterns (e.g. environment variables, basic key rotation expectations) as they relate to the application.

### 2.2.6 Governance, risk and control themes

- Presence or absence of key governance elements: access review practices, incident response basics, and change management at a high level.  
- Mapping of major findings to simple control themes inspired by frameworks such as ISO 27001 / SOC 2, without claiming full certification alignment.

---

## 2.3 Out of scope

To keep the assessment realistic and focused for a time‑boxed student project, several areas are explicitly **out of scope**.  
These exclusions do not imply that they are unimportant, only that they were not examined in this case study.

### 2.3.1 Infrastructure and network layer

- Detailed host hardening of underlying virtual machines, containers or managed platform nodes.  
- Low‑level network segmentation, firewall rule sets or VPN configurations administered by the cloud provider or hosting platform.

### 2.3.2 Full penetration testing and exploit development

- Active exploitation, stress testing, or proof‑of‑concept exploit development beyond basic reasoning about plausibility.  
- Denial‑of‑service (DoS) and performance testing of the platform or its dependencies.

### 2.3.3 Social engineering and physical security

- Phishing simulations, credential harvesting tests or staff‑targeted social engineering exercises.  
- Physical security of offices, devices or network equipment.

### 2.3.4 Detailed regulatory and legal analysis

- Formal legal interpretation of privacy or financial regulations.  
- Comprehensive regulatory gap assessments for specific jurisdictions.  
- Contract and vendor management reviews beyond basic observations about third‑party reliance.

### 2.3.5 Production‑only details

- Direct testing against production systems or live customer data.  
- Any activity that would violate availability or confidentiality expectations for a real environment.  
  (In the original internship project, work was restricted to test/staging environments under NDA; this case study remains conceptual and anonymised.)

---

## 2.4 Key assumptions

The following assumptions underpin this case study and should be kept in mind when interpreting the results:

1. **Representative architecture**  
   The described architecture and data flows are representative of a real SaaS marketplace but simplified and anonymised.  
   Some technical details have been omitted or generalised to make the case study easier to read and to avoid exposing client‑specific information.

2. **Cloud provider baseline security**  
   The underlying cloud provider is assumed to maintain its standard physical, infrastructure and hypervisor‑level security controls.  
   The assessment therefore focuses on how the SaaS team configures and uses these services rather than re‑auditing the cloud provider.

3. **Third‑party payment processing**  
   A specialised payment processor is assumed to handle cardholder data and PCI‑DSS obligations.  
   The assessment looks at how the application integrates with the processor and protects related tokens or IDs, not at the processor’s internal systems.

4. **Time‑boxed student project**  
   The assessment is constrained by the typical limits of a student WIL / internship project:  
   a fixed schedule, limited direct system access and reliance on documentation, read‑only code review and discussion with supervisors.

5. **Good‑faith use of findings**  
   This case study is intended for learning and portfolio purposes only.  
   It is not a complete security review of any real system and should not be used as the sole basis for risk decisions without further validation.

---

## 2.5 How to use this scope

Readers should use this document together with the project overview and threat model to understand where the findings apply and where they do not.  
When applying this approach to a real organisation, the first step should always be to re‑confirm scope, constraints and assumptions with stakeholders before relying on any assessment results.

---