# Domain Guide: Identity & Access Management

## What the Board Tests

- Ability to design identity for multiple user populations (employees, customers, partners)
- SSO protocol selection: SAML vs. OAuth vs. OpenID Connect
- MFA strategy and enforcement
- Connected App design and OAuth flow selection
- Experience Cloud identity patterns

---

## The Three Identity Populations

Every CTA scenario with identity requirements will have some combination of these:

| Population | Recommended Approach |
|-----------|---------------------|
| Internal employees | SAML SSO via corporate IdP (Azure AD, Okta, Ping Identity) |
| Customer / consumer users | Salesforce Identity or social login (Google, Facebook) via Auth. Provider |
| B2B partners with own IdP | Auth. Provider + custom Registration Handler |

**Board trap:** Candidates often design one identity solution for all users. The board expects explicit treatment of each population separately.

---

## SAML vs. OAuth vs. OpenID Connect

| Protocol | Use For |
|---------|---------|
| **SAML 2.0** | SSO for human users (browser-based); IdP-initiated or SP-initiated login |
| **OAuth 2.0** | API authorization; delegated access; machine-to-machine |
| **OpenID Connect** | OAuth 2.0 + identity layer; returns ID token with user claims |

### OAuth Flow Selection

| Flow | When to Use |
|------|------------|
| **JWT Bearer Token** | Server-to-server integrations; no user interaction; daemon processes |
| **Web Server (Auth Code)** | Web apps where user grants access; most secure for user-context |
| **User-Agent (Implicit)** | Legacy; avoid — token exposed in browser |
| **Username-Password** | Avoid — not MFA-compatible; being phased out by Salesforce |
| **Device Flow** | IoT or CLI tools where browser isn't available |
| **Client Credentials** | Machine-to-machine; client ID + secret; no user context |

---

## My Domain & Enhanced Domains

- **My Domain** is required for SSO, Lightning components, and connected apps
- **Enhanced Domains** (default since Spring '23): org-specific subdomain format
- Impacts SSO: IdP metadata and SP metadata must be updated when My Domain changes
- **Board consideration:** When migrating orgs or changing My Domain, all SSO configurations need updating — flag this as a migration risk

---

## MFA Requirements

- Salesforce mandates MFA for all users accessing Salesforce UIs (effective 2022)
- **Options:** Salesforce Authenticator app, TOTP apps, hardware security keys, built-in authenticator
- **SSO users:** If IdP enforces MFA, Salesforce MFA requirement is satisfied
- **Experience Cloud users:** MFA is optional but recommended for high-security portals
- **High Assurance sessions:** Required for accessing sensitive Setup pages; step-up auth

---

## Connected Apps

Key design decisions:
- **OAuth policies:** Who can authorize? (All users / Admin-approved users only)
- **IP restrictions:** Whitelist IPs for server-to-server integrations
- **Session policies:** Timeout, required assurance level
- **Scopes:** Principle of least privilege — only grant scopes needed (api, refresh_token, web, etc.)

**Board trap:** Candidates forget to address token refresh strategy. JWT Bearer Token flow doesn't use refresh tokens — the app re-requests a token using the certificate.

---

## Experience Cloud Identity

| Requirement | Solution |
|------------|---------|
| Customer self-registration | Self-registration enabled on community; Registration Handler Apex class |
| Social login (Google, Facebook) | Auth. Provider configuration |
| B2B partner with own IdP | Auth. Provider + SAML or OIDC |
| Merging external identity with existing Contact | Registration Handler: `createUser` and `updateUser` methods |
| Headless identity (mobile app) | Headless Identity API (Spring '23+) |

---

## Shield & Security Add-Ons

| Feature | Use When |
|---------|---------|
| **Shield Platform Encryption** | Encrypt PII/sensitive data at rest; GDPR, HIPAA, PCI |
| **Event Monitoring** | Audit user activity, API usage, login history at field level |
| **Transaction Security** | Real-time policy enforcement (block exports, alert on anomalies) |
| **Field Audit Trail** | Retain field history beyond standard 18-month limit |

**Encryption caveat:** Encrypted fields cannot be used in: SOQL WHERE clauses (unless using search index), formula fields, roll-up summary fields, duplicate rules. Design data model before enabling encryption.

---

## Key Limits & Facts

| Item | Detail |
|------|--------|
| SAML assertion validity | Typically 5 minutes (configurable at IdP) |
| OAuth access token expiry | 2 hours (default); configurable per Connected App |
| Refresh token expiry | Configurable; can be set to never expire |
| Max Auth. Providers per org | 20 |
| My Domain propagation time | Up to 24 hours for DNS propagation |
