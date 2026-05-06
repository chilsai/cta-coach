# Domain Guide: Platform App Builder & Developer

## What the Board Tests

- Declarative vs. custom development decisions
- Flow design and when it reaches its limits
- Apex design patterns and governor limit management
- LWC vs. Aura vs. Visualforce decision
- API selection for external developers

---

## The Declarative-First Principle

The board will always probe: "Why didn't you use a declarative solution here?"

**Evaluation order:**
1. **Configuration** — Can this be solved with standard fields, page layouts, record types?
2. **Flow** — Can this be automated with Flow (Screen, Record-Triggered, Schedule, Platform Event)?
3. **Apex** — Does this require complex logic, external callouts, or performance at scale?
4. **API** — Does this need to be driven externally?

**You must be able to name the specific Flow limitation** that forced you to Apex.

### When Flow Reaches Its Limits

| Scenario | Why Flow Fails | Solution |
|---------|---------------|---------|
| Complex recursive logic | Flow has loop limits (2,000 iterations) | Apex |
| Bulk operations > 2,000 records | Flow per-interview limits | Batch Apex |
| External callout with complex auth | Flow HTTP callout is limited | Apex with Named Credentials |
| Precise transaction control needed | Flow commits at end of interview | Apex with Savepoint/rollback |
| Real-time CPU-intensive calculation | Flow is slower than Apex at scale | Apex |

---

## Flow Best Practices

- **One Flow per object per trigger event** — avoid multiple Record-Triggered Flows on the same object/event (use decision elements to route logic instead)
- **Bulkification:** Flows are automatically bulkified for record-triggered; don't loop SOQL or DML inside loops
- **Error handling:** Use Fault paths on every element that can fail; log errors to a custom object
- **Flow vs. Process Builder vs. Workflow:** Process Builder and Workflow Rules are retired — always recommend Flow

---

## Apex Design Patterns

### Trigger Framework
- One trigger per object, per event
- Delegate logic to a handler class
- Use a trigger disable mechanism (Custom Metadata or Custom Setting) for emergency shutoff

### Bulkification Pattern
```java
// Wrong
for (Account a : accounts) {
    List<Contact> contacts = [SELECT Id FROM Contact WHERE AccountId = :a.Id]; // SOQL in loop!
}

// Right
Map<Id, List<Contact>> contactsByAccount = new Map<Id, List<Contact>>();
for (Contact c : [SELECT Id, AccountId FROM Contact WHERE AccountId IN :accountIds]) {
    // build map outside loop
}
```

### Async Apex Selection

| Need | Solution |
|------|---------|
| Run after transaction commits | Queueable Apex |
| Process large data sets in chunks | Batch Apex |
| Schedule recurring work | Scheduled Apex |
| Fire-and-forget async | Future method (@future) |
| Long-running (> 2 min) | Batch Apex |

**Future method limitations:** Cannot pass sObject; not chainable; limited to 50/transaction.  
**Prefer Queueable** over Future for new development — supports chaining, sObject parameters, better monitoring.

---

## UI Technology Decision

| Technology | Use When |
|-----------|---------|
| **LWC (Lightning Web Components)** | All new UI development; best performance; modern standard |
| **Aura Components** | Legacy; only if LWC lacks a specific capability (rare) |
| **Visualforce** | Legacy only; not recommended for new development |
| **Screen Flow** | Simple wizard-style UIs; admin-buildable; no code needed |
| **Dynamic Forms** | Granular field visibility on record pages without custom components |

---

## API Selection

| API | Use When |
|-----|---------|
| **REST API** | Standard CRUD; external apps; mobile |
| **SOAP API** | Legacy integrations; enterprise Java/.NET stacks |
| **Bulk API 2.0** | > 50K records; async; CSV-based |
| **Streaming API (CometD)** | Real-time push to subscribers (being superseded by Platform Events) |
| **Platform Events API** | Event-driven; pub/sub; preferred over Streaming |
| **Connect API (Chatter)** | Community, Chatter, Experience Cloud operations |
| **Metadata API** | Deployments, config changes, tooling |
| **Tooling API** | IDE integration, code coverage, test execution |
| **GraphQL API** | Flexible queries; mobile/low-bandwidth; selective field retrieval |

---

## Experience Cloud (Community) Development

| Capability | Declarative | Custom |
|-----------|------------|--------|
| Page layout | Experience Builder (drag/drop) | LWC |
| Branding | Theme editor | CSS / Design Tokens |
| Navigation | Navigation menu editor | Custom LWC |
| Data display | Standard components | LWC with wire adapters |
| Complex logic | Flow embedded in community | LWC + Apex |

**LWR (Lightning Web Runtime):** The modern Experience Cloud framework. Faster, more SEO-friendly. Use for all new communities.

---

## Key Governor Limits

| Limit | Sync | Async |
|-------|------|-------|
| SOQL queries | 100 | 200 |
| DML statements | 150 | 150 |
| DML rows | 10,000 | 10,000 |
| Heap size | 6MB | 12MB |
| CPU time | 10,000ms | 60,000ms |
| Callouts | 100 | 100 |
| Future calls | 50 | — |
| Queueable jobs enqueued | 50 | — |
| Batch Apex concurrent jobs | 5 | — |
