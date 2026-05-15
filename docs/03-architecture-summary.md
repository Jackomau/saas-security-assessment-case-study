# 3. Architecture Summary

## 3.1 Purpose of this document

This document summarises the high‑level architecture of the anonymised SaaS marketplace platform used in the case study.  
It focuses on the components, data flows, user roles and trust boundaries that are most relevant for security assessment and threat modelling.

The goal is not to be an exhaustive technical design, but to provide enough structure so that risks and threats can be discussed in a clear, consistent way.

---

## 3.2 Core user roles and journeys

The platform supports three primary user roles:

- **Clients** – individuals who search for coaches, make bookings, send messages and manage their own profiles.  
- **Coaches** – professionals who manage their availability, respond to booking requests, communicate with clients and view payout information.  
- **Administrators** – internal staff who perform support tasks, review content and manage certain configuration settings.

Typical high‑level user journeys include:

- Client sign‑up, onboarding and profile management  
- Coach sign‑up, onboarding and verification  
- Login / logout and session handling  
- Browsing and searching for coaches  
- Booking creation, modification and cancellation  
- In‑app messaging between clients and coaches  
- Viewing and managing payouts (for coaches)  
- Admin‑level support and troubleshooting activities

These journeys drive most of the data flows and authorisation decisions considered in this assessment.

---

## 3.3 High‑level system components

At a high level, the platform consists of the following components:

1. **Client applications (web and mobile)**  
   - A single‑page web application and mobile app built with a TypeScript‑based framework.  
   - Communicate with backend APIs over HTTPS.  
   - Handle client‑side routing, basic input validation and user session storage.

2. **API backend**  
   - A set of HTTP APIs providing business logic for onboarding, bookings, messaging and payouts.  
   - Implements authentication, authorisation checks and data access to the main database.  
   - Integrates with external services such as a payments provider and notification services.

3. **Identity and access services**  
   - Cloud‑hosted authentication services (or an application‑level auth layer) used for sign‑up, login and token management.  
   - Role and permission information used to distinguish between client, coach and admin capabilities.

4. **Database and storage**  
   - A managed cloud database storing user accounts, profiles, bookings, messages and other operational data.  
   - Blob/storage services for assets such as profile pictures or attachments, where applicable.

5. **Payments integration**  
   - Integration with a third‑party payments provider for handling card payments, payouts to coaches and transaction records.  
   - The platform typically stores references or tokens rather than full cardholder data.

6. **Logging and monitoring**  
   - Application and platform logs for key events (authentication, bookings, errors, administrative actions).  
   - Basic dashboards and alerts for operational monitoring; security‑focused monitoring may be limited or emerging.

7. **Administrative tooling**  
   - Internal views or tools for support staff to inspect user accounts, bookings and platform health.  
   - Often exposed through the same backend with elevated privileges and additional UI components.

---

## 3.4 Logical data flow (Level‑0 DFD)

The following describes the main data flows at a logical level; diagrams in the `/diagrams` folder can be used to visualise these relationships.

1. **Client / Coach → Frontend application**  
   - Users interact with the web or mobile UI, submitting forms and receiving rendered views.

2. **Frontend application → API backend**  
   - The frontend calls RESTful or GraphQL endpoints over HTTPS using authenticated requests (e.g. bearer tokens, cookies).  
   - Requests cover login, search, bookings, messaging and account management.

3. **API backend → Identity / access services**  
   - For login and token validation, the API communicates with the identity provider or internal auth service.  
   - Access tokens, sessions or user claims are validated before processing requests.

4. **API backend → Database / storage**  
   - Business logic reads and writes data related to profiles, bookings, messages and configuration.  
   - Some calls may join across multiple tables or views representing tenants, coaches and clients.

5. **API backend → Payments provider**  
   - For payments and payouts, the backend calls the third‑party payments API with appropriate identifiers or tokens.  
   - Responses update booking or payout records in the local database.

6. **Components → Logging / monitoring**  
   - API and application components emit logs and metrics to the cloud logging / monitoring service.  
   - Selected events may generate alerts or be reviewed in dashboards.

---

## 3.5 Trust boundaries

Understanding trust boundaries is critical for threat modelling and risk assessment.

Key trust boundaries in this architecture include:

1. **Between user devices and the platform (Internet boundary)**  
   - All traffic between client devices (web browsers, mobile apps) and the platform traverses the public internet.  
   - HTTPS termination and any Web Application Firewall (WAF) controls live at this boundary.

2. **Between frontend and backend services**  
   - The frontend is treated as untrusted; all security decisions must be enforced on the API backend.  
   - Tokens and session information crossing this boundary must be validated server‑side.

3. **Between application services and data stores**  
   - Access to the managed database and storage is controlled via credentials and network rules.  
   - Compromise of application components can lead to direct data access if controls are weak.

4. **Between the platform and third‑party providers**  
   - Communication with the payments provider and other third‑party APIs represents another boundary.  
   - The platform must protect API keys, secrets and webhooks at this interface.

5. **Between standard users and administrative functions**  
   - Administrative interfaces typically expose more powerful operations over the same backend.  
   - Insufficient separation at this boundary can lead to privilege escalation or data exposure.

These boundaries are referenced in the threat model (`04-threat-model.md`) to reason about where spoofing, tampering and other STRIDE categories are most relevant.

---

## 3.6 Architectural constraints and design considerations

The platform exhibits several common constraints for early‑stage SaaS products:

- A small engineering team with limited time for dedicated security work.  
- Preference for managed cloud services and third‑party integrations to reduce operational burden.  
- Evolving requirements, leading to incremental changes in authentication, authorisation and logging patterns.  
- Need to balance user experience for mobile clients with robust session and token handling.

These constraints influence which risks are considered most realistic and which remediation steps are prioritised in the roadmap.

---

## 3.7 How this architecture summary is used

This architecture summary is used as the starting point for:

- The STRIDE‑based threat model in `04-threat-model.md`.  
- The attack surface discussion in `05-attack-surface.md`.  
- The risk register and remediation planning later in the assessment.

When applying this approach to a real system, the architecture summary would be validated with system owners and updated as new components or integrations are identified.

---