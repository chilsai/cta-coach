# Salesforce Well-Architected Framework

Source: https://architect.salesforce.com/well-architected/overview  
Last reviewed: 2025-05

---

## Overview

The Salesforce Well-Architected Framework provides guidance for building trusted, easy, and adaptable solutions on the Salesforce platform. It is the reference framework used by the CTA Review Board to evaluate architectural decisions.

The three pillars:

| Pillar | Definition |
|--------|-----------|
| **Trusted** | Secure, compliant, performant, and reliable |
| **Easy** | Simple to use, maintain, and extend |
| **Adaptable** | Flexible to change as business needs evolve |

---

## Pillar 1: Trusted

### Security
- Apply least-privilege access at every layer (org, object, field, record)
- Default OWD to Private or Public Read Only; use sharing rules to open up
- Encrypt sensitive data at rest (Shield Platform Encryption) and in transit (TLS)
- Use Named Credentials for all external system authentication — never hardcode credentials
- MFA enforcement: mandatory for all internal users; recommended for community users

### Performance
- Avoid SOQL queries inside loops — bulkify all Apex
- Use asynchronous processing (Queueable, Batch Apex, Platform Events) for high-volume operations
- Monitor and avoid data skew: >10,000 child records on a single parent is a warning sign
- Use selective queries: leading field in WHERE clause must be indexed
- Large Data Volumes (LDV): >1M records requires deliberate indexing strategy, skinny tables, and query optimization

### Reliability
- Design for idempotency in integrations — retries should not create duplicates
- Use Platform Events for decoupled, reliable event-driven integration
- Implement dead-letter queues or error handling for failed async operations
- Monitor with Event Monitoring and Transaction Security Policies

---

## Pillar 2: Easy

### Declarative First
- Always evaluate declarative options before writing code
- Decision order: Configuration → Flow → Apex → API
- Justify every custom development: "What declarative option did I rule out and why?"
- Flows replace Process Builder and Workflow Rules (both retired)

### Simplicity
- Avoid over-engineering: the simplest solution that meets requirements is preferred
- Avoid automation redundancy: one process should own one outcome
- Use standard objects before custom objects where semantically appropriate
- Limit the number of triggers per object — use a trigger handler pattern

### Maintainability
- Document architectural decisions and their rationale
- Use meaningful API names; avoid auto-generated names like `Field_1__c`
- Separate configuration from code (Custom Metadata Types, not hardcoded values)

---

## Pillar 3: Adaptable

### Scalability
- Design for 10x current data volumes from day one
- Consider multi-org strategies for global enterprises (data residency, compliance)
- Use platform-native scaling (Salesforce handles infrastructure) — don't reinvent it

### Extensibility
- Use interfaces and abstract classes in Apex to allow future extension
- Build integrations to be loosely coupled (event-driven over point-to-point)
- Design Experience Cloud communities to support future user type additions

### Evolvability
- Avoid dependencies on undocumented APIs or internal Salesforce behavior
- Use Metadata API and source-based deployments — not manual UI changes
- Treat every architectural decision as reversible unless proven otherwise

---

## Key Architecture Principles for CTA Candidates

### 1. The Declarative vs. Custom Decision
The board will always ask: "Why didn't you use Flow/declarative here?"  
You must be able to answer: "I evaluated Flow and ruled it out because [specific limitation]. The custom approach I chose is justified by [specific requirement]."

### 2. The Integration Pattern Hierarchy
| Pattern | Use When |
|---------|---------|
| Native Salesforce features | Data already in Salesforce |
| Salesforce Connect / OData | Read-only external data, no ETL needed |
| Platform Events | Async, decoupled, event-driven |
| REST/SOAP Callouts | Real-time, low-volume, synchronous |
| Bulk API | High-volume data load/extract |
| MuleSoft / iPaaS | Complex orchestration, many systems |
| ETL / Batch | Large volume, non-real-time, data warehouse |

### 3. The Identity Decision Tree
- **Internal employees:** SAML SSO via corporate IdP (Azure AD, Okta, Ping)
- **Customer/consumer users:** Salesforce Identity or social login (Experience Cloud)
- **B2B partners with own IdP:** Auth. Provider + Registration Handler
- **API integrations:** OAuth 2.0 (JWT Bearer for server-to-server; Web Server for user-context)

### 4. The Data Residency Checklist
If a requirement mentions GDPR, data sovereignty, or country-specific data laws:
1. Is Hyperforce available in the required region?
2. Does Shield Data Residency Policy satisfy the requirement?
3. Does the customer need a separate org (compliance isolation)?
4. What crosses the boundary? (integrations, backups, sandboxes)

### 5. The DevOps Maturity Model
| Maturity | Approach |
|---------|---------|
| Level 1 | Change Sets (avoid recommending this) |
| Level 2 | DevOps Center + GitHub (admin-friendly, source-tracked) |
| Level 3 | Salesforce CLI + CI/CD pipeline (developer-driven) |
| Level 4 | Full GitOps with automated testing, packaging (ISV/large enterprise) |

Always match the recommendation to the team's capability, not the ideal state.

---

## Governor Limits Reference (Key Limits for CTA Scenarios)

| Limit | Value |
|-------|-------|
| SOQL queries per transaction | 100 (sync) / 200 (async) |
| DML statements per transaction | 150 |
| Heap size | 6MB (sync) / 12MB (async) |
| CPU time | 10,000ms (sync) / 60,000ms (async) |
| Callouts per transaction | 100 |
| Platform Event publish per transaction | 150 |
| Flow interviews per transaction | 2,000 |
| Max records returned by SOQL | 50,000 |
| Bulk API batch size | 10,000 records |
| API calls per 24 hours | Varies by edition (15,000–unlimited) |

> For LDV scenarios: anything above 10M records, discuss query optimization, skinny tables, and async processing explicitly.

---

## Common CTA Anti-Patterns

| Anti-Pattern | Why It Fails | Better Approach |
|-------------|-------------|----------------|
| Synchronous Apex callout in trigger | Blocks transaction; governor limit risk; poor UX | Platform Events → async callout |
| Hardcoded IDs or values in Apex | Breaks on deployment; unmaintainable | Custom Metadata Types |
| One trigger per use case per object | Execution order unpredictable; conflicts | Trigger handler framework, one trigger per object |
| Custom portal instead of Experience Cloud | Rebuilds what exists; expensive to maintain | Experience Cloud (LWR or Aura) |
| Change Sets for deployment | No version control; not repeatable; risk | Source-based: DevOps Center or CI/CD |
| Storing credentials in custom fields | Security vulnerability | Named Credentials |
| Username/password OAuth flow for integrations | Not MFA-compatible; deprecated direction | JWT Bearer Token flow |
| SOQL in loops | Hits 100-query limit quickly | Bulk query before loop, use Maps |
