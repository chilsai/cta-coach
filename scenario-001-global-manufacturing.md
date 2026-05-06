# CTA Practice Scenario 001: Global Manufacturing Digital Transformation

## Scenario Category: Multi-Domain Enterprise
## Estimated Presentation Time: 20-25 minutes
## Domains Tested: Integration, Data Architecture, IAM, Sharing & Visibility, DevOps

---

## Background
TechManufacture Inc. is a global industrial equipment manufacturer with $4B in annual revenue, 8,000 employees across 22 countries, and manufacturing facilities in the US, Germany, and Malaysia.

## Current State
- **CRM:** Salesforce Sales Cloud (Enterprise Edition) — 1,200 Sales users, implemented 2017
- **ERP:** SAP S/4HANA (on-premises, Germany datacenter)
- **Service:** ServiceNow for IT tickets; customer service handled via email and phone
- **Data:** 3.2M Account records, 18M Opportunity records (7 years of history)
- **Identity:** Microsoft Azure Active Directory for all employees
- **Current Integration:** Nightly batch file transfer between SAP and Salesforce (CSV via SFTP)
- **Development:** 2 Salesforce admins, no dedicated developers; all changes via Change Sets

## Business Requirements

### R1: Service Cloud Implementation
Deploy Salesforce Service Cloud for 400 customer service agents handling 15,000 cases/month. Cases must be auto-created from:
- Customer email (via existing email infrastructure)
- IoT sensors on manufactured equipment (REST API, ~500 events/hour per customer)
- Customer self-service portal (new, to be built)

### R2: Real-Time SAP Integration
Replace nightly batch with real-time bidirectional sync:
- When Opportunity closes-won in Salesforce → create Sales Order in SAP (< 5 second latency)
- When SAP ships product → update Opportunity delivery status in Salesforce
- When SAP processes invoice → update Account billing status in Salesforce

### R3: Customer Self-Service Portal
Build a portal where customers can:
- View their equipment, warranties, and service history
- Submit and track service cases
- Access product documentation
- View real-time equipment IoT status

### R4: Unified Identity
- All 1,200 internal users: SSO via Azure AD (currently using username/password in Salesforce)
- 5,000 customer portal users: email/social login; NO Azure AD access
- 200 partner dealers: limited Salesforce access; use their own company identity providers (3 different IdPs)

### R5: Data Governance & Compliance
- GDPR applies (EU customers, German operations)
- Customer PII must be identifiable and erasable on request
- Germany data residency requirement for EU customer records
- Salesforce currently has no data classification or governance framework

### R6: DevOps Modernization
- No more Change Sets; need repeatable, auditable deployments
- 2 admins need to continue making declarative changes without code expertise
- Compliance requires full audit trail of every production change
- Target: deploy to production twice per week

## Constraints
- **Budget:** $1.2M total (licenses + implementation) over 18 months
- **Timeline:** Service Cloud must go live in 6 months (board-level commitment)
- **Team:** Current 2 admins + 3 new hires (1 developer, 1 architect, 1 project manager)
- **Technology:** Company standardized on Microsoft Azure for cloud infrastructure
- **Salesforce Edition:** Currently Enterprise; can upgrade to Unlimited if justified

---

## Candidate Instructions
You have 10 minutes to review this scenario. You may ask the panel up to 5 clarifying questions. Then present your architectural solution for 20-25 minutes.

## Suggested Clarifying Questions (What a Strong Candidate Would Ask)
1. "Are the 5,000 customer portal users globally distributed? What's the split between EU and non-EU?"
2. "For the IoT integration — 500 events/hour per customer — how many customers will be actively monitored simultaneously at peak?"
3. "Are the 200 partner dealers external users who need Experience Cloud licenses, or do they access Salesforce directly?"
4. "For the Germany data residency requirement — is this all German operations, or specifically EU customer PII?"
5. "What's the current Azure AD structure — single tenant, multi-tenant?"

## Scoring Guide for Coaches

### Green Flags (Candidate is on track)
✅ Immediately identifies IoT at 500 events/hour × N customers as a scaling/Platform Events sizing question
✅ Separates the 3 identity populations: employees (Azure AD SSO), customers (Salesforce Identity/social), partners (multi-IdP)
✅ Flags GDPR + Hyperforce Germany instance as the data residency solution
✅ Recommends Experience Cloud (not custom portal) for customer self-service
✅ Suggests async integration for SAP (not sync Apex callout in trigger)
✅ Addresses admin-friendly DevOps (DevOps Center) given team constraints
✅ Sizes the Salesforce edition correctly (Unlimited for IoT volume + 400 service agents)

### Red Flags (Coaching intervention needed)
🚩 Suggests synchronous Apex callout to SAP for close-won trigger
🚩 Proposes username/password or basic auth for any integration
🚩 Doesn't address partner IdP federation (misses 3-IdP complexity)
🚩 Ignores data residency; proposes single global Salesforce org without Hyperforce
🚩 Proposes Change Sets with better process (not a DevOps modernization)
🚩 Doesn't identify data skew risk with 18M Opportunity records
🚩 Proposes custom-built portal instead of Experience Cloud

## Model Solution Highlights (For Coach Reference Only — Don't Give to Candidate)

**Architecture pillars:**
1. **Hyperforce Germany instance** for EU data residency (or Shield Data Residency Policies as alternative)
2. **Experience Cloud** (Customer Community Plus) for self-service portal
3. **Platform Events** for IoT ingestion → Service Cloud case auto-creation
4. **MuleSoft or Integration Proc** for SAP bidirectional sync (event-driven, not batch)
5. **Azure AD SAML SSO** for employees; **Salesforce Identity** for customer portal; **Auth. Providers** for partner IdPs
6. **DevOps Center** for admin-friendly pipeline + GitHub for source control
7. **Shield Platform Encryption** for PII fields (GDPR) + Data Mask for sandboxes
8. **Duplicate Rules + External ID** strategy for the 3.2M Account dedup risk
