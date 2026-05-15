Auth & API Security Checklist (32 checks, anonymised)
A. Authentication, sessions and RBAC (10 checks)
AUTH‑01 – Auth guard on every privileged function
Every server‑side function or endpoint that reads or writes user‑specific data enforces an authentication check at the very beginning.

AUTH‑02 – User identity bound to actions
For state‑changing operations, the effective user identity is taken from the authenticated context (token/session), not from a client‑supplied ID field.

AUTH‑03 – Role checked, not just login state
Endpoints that perform admin‑like or coach‑only actions verify the caller’s role/permissions, not just that they are logged in.

AUTH‑04 – Session invalidation rules defined
There is a clear rule for when sessions/tokens are invalidated (logout, password change, role change) and this is enforced consistently.

AUTH‑05 – Password / credential policy
Basic credential hygiene is in place (minimum length, no trivial defaults, rate‑limited login attempts).

AUTH‑06 – Account recovery flows protected
Password reset and account recovery flows require proof of control over the email/identifier and are protected against easy abuse.

AUTH‑07 – No trust in client‑supplied role claims
Role or tenant information from the client is treated as untrusted and re‑derived or verified server‑side.

AUTH‑08 – Token validation includes expiry and audience
Every token is checked for signature, expiry, issuer and audience before being accepted.

AUTH‑09 – Separate paths for admin tooling
Administrative tools are protected behind separate routes or interfaces and are not exposed through the same paths used by normal users.

AUTH‑10 – Least privilege for service accounts
Any internal service account or integration runs with the minimum permissions needed, not full admin across all data.

B. BOLA / data access / row‑level security (10 checks)
DATA‑01 – Ownership enforced on read operations
Read‑type endpoints (e.g. get booking, get message thread) enforce that the current user is authorised to see the specific record, even if the ID is guessed.

DATA‑02 – Ownership enforced on write operations
Update/delete actions verify that the record belongs to the caller (or to their tenant/organisation) before applying changes.

DATA‑03 – No direct trust in client‑supplied IDs
APIs do not rely solely on IDs from the client; the server checks that IDs are consistent with the authenticated user’s scope.

DATA‑04 – Tenant / account scoping applied
Multi‑tenant data (e.g. across coaches/organisations) is always filtered by tenant/account scope, not just raw IDs.

DATA‑05 – Row‑level security (RLS) or equivalent in place
Where supported, the database enforces row‑level security or an equivalent mechanism to prevent cross‑tenant access.

DATA‑06 – Sensitive joins reviewed
Queries that join multiple tables are checked to ensure the join conditions do not accidentally broaden access beyond the intended user/tenant.

DATA‑07 – Pagination and list endpoints hardened
List endpoints (e.g. list bookings, list clients) always apply filters based on user/role/tenant and do not return all records by default.

DATA‑08 – Indirect object references avoided
Endpoints avoid exposing easily guessable identifiers where unnecessary, or pair them with additional checks (e.g. scoped tokens).

DATA‑09 – Soft deletes and archives guarded
Historical or archived data is subject to the same access controls as active records.

DATA‑10 – Bulk operations constrained
Bulk operations (e.g. update many records) are limited and filtered to the caller’s legitimate scope to reduce blast radius.

C. Secrets, configuration and cloud usage (6 checks)
CLOUD‑01 – Secrets not hard‑coded
No API keys, access tokens or secret values are hard‑coded in source files; they are injected via secure configuration mechanisms.

CLOUD‑02 – Environment files and history reviewed
Environment/config files are excluded from version control by default, and commit history has been checked for past accidental leaks.

CLOUD‑03 – Principle of least privilege in cloud roles
Application‑level roles/identities in the cloud platform have only the permissions needed for their tasks (e.g. scoped to specific databases/buckets).

CLOUD‑04 – Storage permissions minimised
Storage buckets/containers are not publicly readable by default, and access is restricted to the application and authorised users.

CLOUD‑05 – Outbound connections controlled
Outbound connectivity to external services (including payments and email providers) is documented, authenticated and monitored.

CLOUD‑06 – Basic backup and restore assumptions
There is at least a minimal understanding of how data would be restored in case of corruption or accidental deletion, even if this is provided by the cloud service.

D. Logging, errors and monitoring (6 checks)
LOG‑01 – Authentication events logged
Successful and failed logins, password resets and unusual login activity are logged with enough context (time, user, source) for investigation.

LOG‑02 – Sensitive actions logged
Security‑relevant actions (e.g. role changes, payout updates, admin changes to accounts) produce audit logs.

LOG‑03 – No sensitive data in logs
Logs avoid storing secrets, full tokens, passwords or unnecessary personal data.

LOG‑04 – Error messages sanitised
External error messages are generic and do not reveal stack traces, SQL queries or internal implementation details.

LOG‑05 – Basic alerting on abnormal patterns
There is at least basic alerting for error spikes, repeated failed logins or other clearly suspicious patterns.

LOG‑06 – Log retention and access controlled
Log retention periods are defined, and access to logs is limited to authorised staff or services.