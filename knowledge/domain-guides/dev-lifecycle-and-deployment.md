# Domain Guide: Development Lifecycle & Deployment

## What the Board Tests

- Environment strategy for development, testing, and production
- Source control and deployment pipeline design
- Org vs. package development model trade-offs
- Matching DevOps maturity to team capability
- Handling declarative (admin) changes alongside code

---

## Environment Strategy

### Sandbox Types

| Type | Refresh Interval | Data | Use For |
|------|-----------------|------|---------|
| **Developer** | 1 day | No | Individual developer work |
| **Developer Pro** | 1 day | No | Larger storage for dev; ETL testing |
| **Partial Copy** | 5 days | Sample (10K records) | QA/integration testing with representative data |
| **Full** | 29 days | Full copy | UAT, performance testing, training |

**Board tip:** Full sandbox refresh takes 24–72 hours. Factor this into release cadence planning.

### Recommended Environment Pipeline

```
Developer Sandbox (each dev)
        ↓
Integration Sandbox (daily merge)
        ↓
QA / Staging Sandbox (Partial or Full)
        ↓
UAT Sandbox (Full — stakeholder sign-off)
        ↓
Production
```

For large enterprises, add a **Hotfix Sandbox** branched from Production for emergency patches.

---

## Deployment Models

### Org Development Model
- Deploy metadata between orgs using Change Sets, Salesforce CLI, or CI/CD
- Suitable for most customer implementations
- Source of truth is typically a Git repository

### Package Development Model (2GP)
- Modular: application packaged as unlocked or managed package
- Suitable for ISVs, large enterprises with multiple teams/products
- Versioned, installable, upgradeable artifacts
- **2nd Generation Packaging (2GP):** Recommended over 1GP for new projects

| Package Type | Use When |
|-------------|---------|
| **Unlocked Package** | Internal teams; org-specific apps; no namespace required |
| **Managed Package** | ISV/AppExchange; namespace required; code obfuscation |

---

## Source Control & CI/CD

### Source Control Strategy
- Every metadata change tracked in Git (never manual production edits)
- Branch strategy: feature branches → integration → main/release
- Pull request reviews before merge to integration

### CI/CD Pipeline Stages

```
1. Developer pushes to feature branch
2. PR opened → automated checks (linting, unit tests)
3. PR merged to integration → deploy to integration sandbox
4. QA approval → deploy to staging
5. UAT sign-off → scheduled deploy to production
```

### Tooling Options

| Tool | Best For |
|------|---------|
| **DevOps Center** | Admin-friendly; point-and-click pipeline; built into Salesforce |
| **Salesforce CLI (sf/sfdx)** | Developer-driven; scriptable; integrates with any CI |
| **Copado** | Enterprise; compliance-focused; change management built-in |
| **Gearset** | Comparison-based deployments; easy diff between orgs |
| **GitHub Actions / Jenkins** | Custom CI/CD; full control; requires setup expertise |

**Board tip:** Match the tool to the team. Recommending GitHub Actions for a 2-admin team is wrong. DevOps Center is the right answer for admin-led teams.

---

## Handling Declarative + Code Changes Together

The #1 DevOps challenge in Salesforce: admins making declarative changes while developers write code.

**Solutions:**
1. **DevOps Center:** Supports both declarative (point-and-click) and code changes in a unified pipeline
2. **Source tracking:** Scratch orgs and sandboxes with source tracking auto-detect all changes
3. **Process discipline:** All changes — including admin changes — must go through the pipeline, not directly to production
4. **Environment branching:** Admins get a dedicated integration sandbox; changes pulled to source control before merging

---

## Scratch Orgs

- Temporary, configurable orgs spun up from a definition file
- Expire after 1–30 days
- Source-tracked: every change is automatically tracked
- Ideal for: feature development, automated testing, reproducible environments
- Require: Dev Hub enabled in production or dedicated Dev Hub org

---

## Testing Strategy

| Test Type | When | Tool |
|-----------|------|------|
| Unit tests (Apex) | Every commit | @isTest classes; 75% coverage minimum |
| UI tests | Pre-deployment to QA | Selenium, Provar, Copado Robotic Testing |
| Integration tests | Pre-deployment to Staging | Postman, custom Apex tests |
| UAT | Pre-production | Manual; business users |
| Regression | Every release | Automated test suite |

**Coverage ≠ quality.** The board will ask about test strategy, not just coverage percentage.

---

## Key Facts & Limits

| Item | Detail |
|------|--------|
| Apex test coverage required | 75% org-wide; each class individually should aim for 90%+ |
| Change Set components limit | No hard limit, but large change sets are slow and error-prone |
| Scratch org max active | 6 (Developer Edition Dev Hub) / 40+ (paid) |
| Full sandbox refresh | 29-day minimum between refreshes |
| Metadata API deployment timeout | 10 minutes per component deployment |
