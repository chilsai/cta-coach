# Domain Guide: Data Architecture

## What the Board Tests

- Data model design: standard vs. custom objects, relationships
- Large Data Volumes (LDV) strategy
- Data migration approach and tooling
- Data quality, deduplication, and governance
- GDPR/compliance and data residency

---

## Data Model Design Principles

1. **Use standard objects first** — Salesforce optimizes for them; reports, integrations, and AppExchange packages expect them
2. **Master-Detail vs. Lookup:** Master-Detail = tight coupling, roll-ups, cascading delete; Lookup = loose coupling, optional
3. **Avoid EAV anti-pattern** — don't use generic `Name`/`Value` custom objects to store arbitrary attributes; model explicitly
4. **Polymorphic lookups** — use sparingly; harder to report on and query

### Relationship Decision Guide

| Need | Relationship |
|------|-------------|
| Child must always have a parent | Master-Detail |
| Roll-up summary (count, sum, min, max) | Master-Detail |
| Child can exist without parent | Lookup |
| Many-to-many | Junction object (two Master-Details) |
| Self-referential hierarchy | Hierarchical Lookup (User only) or self-lookup |

---

## Large Data Volumes (LDV)

Threshold: **> 1 million records** on an object requires deliberate LDV strategy.

### Indexing Strategy
- Standard indexed fields: Id, Name, OwnerId, CreatedDate, SystemModstamp, RecordTypeId, Master-Detail fields
- Custom indexes: request via Salesforce Support for frequently queried non-selective fields
- **Selectivity rule:** A query is selective if it returns < 10% of total records (or < 333K, whichever is less)

### Skinny Tables
- Salesforce-side optimization: a copy of frequently queried fields on a separate narrow table
- Request via Salesforce Support
- Use when: reports and queries consistently join many fields from a large object

### Archiving Strategy
- BigObjects: store historical data on-platform, queryable but limited SOQL support
- External archiving: move to data warehouse/data lake via Bulk API
- Soft delete + archive pattern: add `IsArchived__c` flag, exclude from standard queries

### Data Skew (Repeat for LDV)
- Account ownership skew: > 10K records owned by one user
- Account data skew: > 10K child records on one parent Account
- Both cause locking, sharing recalculation slowdowns, and DML contention

---

## Data Migration

### Tooling Decision

| Tool | Use When |
|------|---------|
| **Data Loader** | < 5M records; one-time or infrequent; technical user |
| **Salesforce CLI (Bulk API)** | Scripted, automated migrations; developer-led |
| **MuleSoft / ETL** | Complex transformations; multi-source; ongoing sync |
| **Data Import Wizard** | < 50K records; non-technical user; standard objects |
| **Third-party (Informatica, Talend)** | Enterprise ETL; existing tool standardization |

### Migration Phases
1. **Discovery:** Profile source data — volumes, quality, nulls, duplicates
2. **Mapping:** Source field → target field; transformation rules
3. **Cleansing:** Deduplicate, standardize, validate before load
4. **Pilot load:** Load 1% to sandbox; validate results
5. **Incremental loads:** Load in batches; use External ID for upsert idempotency
6. **Delta migration:** Capture changes since pilot; apply before cutover
7. **Cutover:** Final delta + freeze source; validate; go live

### External IDs
- Always use External IDs for migration: enables upsert, prevents duplicates on retry
- Set `externalId=true` on the field; optionally index it
- Include source system ID as External ID on all migrated records

---

## Data Quality & Deduplication

| Feature | Use When |
|---------|---------|
| **Duplicate Rules** | Prevent duplicate creation in real-time; alert or block |
| **Matching Rules** | Define what "duplicate" means (fuzzy name match, exact email, etc.) |
| **Duplicate Jobs** | Find existing duplicates in bulk |
| **Third-party (Ringlead, Cloudingo)** | Advanced fuzzy matching; large-scale dedup projects |

**Board tip:** Duplicate Rules only run on insert/update via UI or API (with `DuplicateRuleHeader`). They do not prevent duplicates loaded via Bulk API unless explicitly configured.

---

## Data Governance

### GDPR Compliance
- **Right to erasure:** Use Data Mask (sandboxes) + anonymization strategy for production
- **Data classification:** Shield Data Classification for field-level sensitivity labels
- **Consent management:** Use standard Individual object or third-party Consent Management apps
- **Data residency:** Hyperforce region selection; Shield Data Residency Policies

### Data Classification (Shield)
Field-level labels: Public → Internal → Confidential → Restricted  
Drives encryption, event monitoring, and access policy decisions.

### Backup & Recovery
- Salesforce does not provide point-in-time restore by default
- Options: Salesforce Backup (native, additional cost), OwnBackup, Spanning, Veeam
- **Board question:** "What's your RTO/RPO for data loss?" — always address this in data architecture

---

## Key Limits & Facts

| Item | Detail |
|------|--------|
| Custom objects per org (Enterprise) | 800 |
| Custom fields per object | 500–800 depending on type |
| Master-Detail relationships per object | 2 |
| Lookup relationships per object | 25 |
| Roll-up summary fields per object | 25 |
| Records returned by SOQL | 50,000 |
| Bulk API batch size | 10,000 records |
| LDV threshold (planning) | > 1M records |
| Data Skew threshold | > 10,000 child records per parent |
