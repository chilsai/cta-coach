# Domain Guide: Sharing & Visibility

## What the Board Tests

- OWD design decisions and their performance implications
- When to use each sharing mechanism (and why not to over-engineer)
- Sharing rule performance at scale
- Experience Cloud sharing model
- Shield encryption impact on visibility

---

## The Sharing Model Stack (Evaluate in Order)

```
1. Organisation-Wide Defaults (OWD)       ← Most restrictive baseline
2. Role Hierarchy                         ← Vertical access (managers see subordinates)
3. Sharing Rules                          ← Horizontal access (criteria or ownership-based)
4. Manual Sharing                         ← Ad hoc, user-initiated
5. Apex Managed Sharing                   ← Programmatic, complex rules
6. Teams (Account/Opportunity/Case)       ← Collaborative records
```

Always start with the most restrictive OWD that meets requirements, then open up.

---

## OWD Decision Guide

| OWD Setting | Use When |
|------------|---------|
| **Private** | Users should only see their own records by default; use role hierarchy + sharing rules to open up |
| **Public Read Only** | All users need to read all records; edits restricted to owner/above in hierarchy |
| **Public Read/Write** | All users need to read and edit all records (rare — e.g., shared reference data) |
| **Controlled by Parent** | Child object visibility follows parent (detail records on master-detail) |

**Board principle:** Start Private. The board will ask "why did you set OWD to Public?" — have an answer.

---

## Sharing Rules: Criteria vs. Owner-Based

| Type | Use When |
|------|---------|
| **Owner-based** | Share records owned by a role/group with another role/group |
| **Criteria-based** | Share records matching field conditions (e.g., Region = 'EMEA') |

**Performance warning:** Sharing recalculation is triggered by:
- OWD changes (full recalc — can take hours on large orgs)
- Sharing rule changes
- Role hierarchy changes
- Record ownership transfers at scale

For LDV orgs (>1M records), always flag sharing recalculation as a performance risk.

---

## When to Use Apex Managed Sharing

Use Apex sharing when:
- Sharing logic is too complex for criteria-based rules
- Sharing depends on related record data (e.g., "share with the team on the parent Account")
- Sharing must change dynamically based on business events

**Key:** Apex sharing requires a custom sharing reason. Records shared via Apex are not removed when OWD changes — must be managed programmatically.

---

## Data Skew & Sharing Performance

**Account Data Skew:** > 10,000 child records (Contacts, Opportunities, Cases) on one Account
- Causes lock contention on DML operations
- Sharing recalculation becomes slow
- Solution: Flatten the model; consider Contact/Opportunity not under a single parent Account

**Ownership Skew:** > 10,000 records owned by one user in a Private OWD object
- Sharing recalculation causes performance issues when that user's role changes
- Solution: Use a queue instead of a user for high-volume record ownership

---

## Experience Cloud Sharing

| User Type | License | Default Sharing |
|-----------|---------|----------------|
| Customer Community | Community (external) | OWD for external users (separate from internal) |
| Customer Community Plus | Community Plus | Role hierarchy for external users enabled |
| Partner Community | Partner Community | Full role hierarchy; can own records |

**External OWD is separate from internal OWD** — set independently per object.  
Community users only see records shared to them explicitly, or via the sharing model.

**High-Volume Community Users (HVCU):**
- No role in hierarchy — cannot use role-based sharing
- Use Sharing Sets or Share Groups for access
- Much cheaper license than standard community users
- Appropriate for portal users who only see their own data

---

## Shield Platform Encryption & Visibility

Encrypting a field has these visibility implications:
- **Cannot filter (WHERE) on encrypted fields** unless Deterministic Encryption is used
- **Cannot use in reports** as filter criteria
- **Cannot use in formula fields** or roll-up summaries
- **Full search** (SOSL) works; SOQL WHERE does not (without Deterministic)

**Deterministic Encryption:** Allows exact-match filtering on encrypted fields. Use for fields that need to be queried (e.g., SSN, Tax ID). Not suitable for fields needing fuzzy/like search.

---

## Key Limits & Facts

| Item | Detail |
|------|--------|
| Max sharing rules per object | 300 (owner-based) + 300 (criteria-based) |
| Sharing recalculation trigger | OWD or sharing rule change = full async recalc |
| Role hierarchy depth recommended | ≤ 10 levels for performance |
| Manual shares per record | No hard limit; but performance degrades at scale |
| External OWD | Configured separately from internal OWD per object |
