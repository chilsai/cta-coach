# Domain Guide: Integration Architecture

## What the Board Tests

- Ability to select the right integration pattern for a given requirement
- Understanding of async vs. sync trade-offs
- MuleSoft positioning: when it's overkill vs. when it's essential
- Error handling, retry, and idempotency design
- API versioning and governance

---

## Integration Pattern Decision Framework

### Sync vs. Async

| Use Sync When | Use Async When |
|--------------|---------------|
| User is waiting for a response | Background processing is acceptable |
| < 5 second latency required | Volume is high (>100/hour) |
| Low volume (< 10/minute) | Decoupling is more important than latency |
| Simple request/response | Failures need retry without blocking the user |

### Pattern Selection Guide

| Scenario | Recommended Pattern |
|---------|-------------------|
| Real-time ERP update on Opportunity close | Platform Event → Apex consumer → outbound callout |
| Nightly data sync from data warehouse | Bulk API or ETL tool |
| Customer portal reading external inventory | Salesforce Connect (OData) |
| IoT sensor data ingestion at high volume | Platform Events → Apex Trigger |
| Complex multi-system orchestration | MuleSoft Anypoint Platform |
| Simple outbound notification to one system | Outbound Message or REST callout |
| Salesforce-to-Salesforce sync | Salesforce-to-Salesforce (S2S) or REST API |

---

## Platform Events

- **Use for:** Decoupled, reliable async messaging within or across Salesforce orgs
- **Limits:** 150 publishes per transaction; 250,000 delivered per 24 hours (Enterprise)
- **Retention:** 72 hours (can replay from any point in retention window)
- **Error handling:** Failed subscribers don't affect publisher; use ReplayId for recovery
- **vs. Change Data Capture:** Platform Events = custom; CDC = standard object change tracking

### Common Board Trap
> "Why not just use a trigger with a callout?"

Answer: Synchronous callouts in triggers block the transaction, count against callout limits (100/transaction), and fail silently under load. Platform Events decouple the concern and allow async retry.

---

## MuleSoft — When to Recommend

**Recommend MuleSoft when:**
- 5+ systems need to be integrated
- Complex transformation/orchestration logic (not just pass-through)
- Enterprise iPaaS is already standardized on MuleSoft
- Compliance requires a central integration audit layer
- B2B EDI or legacy protocol support needed

**Do NOT recommend MuleSoft when:**
- Simple point-to-point between Salesforce and one system
- Budget is constrained (MuleSoft licensing is significant)
- Team has no MuleSoft expertise

**Alternative:** Salesforce Flow + Named Credentials + REST for simple cases; AWS EventBridge or Azure Service Bus if already in that ecosystem.

---

## API Security Best Practices

| Flow | Use Case |
|------|---------|
| JWT Bearer Token | Server-to-server (no user context); daemon integrations |
| Web Server Flow | User-context OAuth; user must authorize |
| Username-Password Flow | Avoid — not MFA compatible; being deprecated |
| Client Credentials | Machine-to-machine with client ID/secret |

Always use **Named Credentials** — never store endpoints or credentials in custom fields or custom settings.

---

## Idempotency Design

Every integration must handle duplicate delivery:
- Use External ID fields to prevent duplicate record creation
- Use `upsert` instead of `insert` where possible
- Include a correlation/transaction ID in every message
- Design consumers to check "have I processed this ID before?"

---

## Error Handling Checklist

- [ ] What happens when the external system is unavailable?
- [ ] How are failed messages retried? (Exponential backoff recommended)
- [ ] Where do permanently failed messages go? (Dead-letter queue or custom error object)
- [ ] Who is alerted when errors exceed a threshold?
- [ ] Is the main transaction rolled back or does the integration fail silently?

---

## Key Limits to Know

| Limit | Value |
|-------|-------|
| Callouts per transaction | 100 |
| Callout timeout | 120 seconds |
| Platform Event publishes per transaction | 150 |
| Platform Event delivery per 24h (Enterprise) | 250,000 |
| Outbound messages per hour | 10,000 |
| Bulk API concurrent jobs | 5 (parallel) |
