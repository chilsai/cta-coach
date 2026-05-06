# Salesforce CTA Coach

An AI-powered coaching system to help you pass the **Salesforce Certified Technical Architect (CTA)** certification. Built on [Claude Code](https://claude.ai/code), it simulates the Review Board experience, coaches architectural decision-making, and adapts to your experience level.

---

## What This Does

- Runs mock Review Board sessions with judge-style questioning
- Coaches you through the SPADE decision framework
- Scores your solutions against the actual CTA rubric
- Covers all seven CTA sub-domains with specialized coaching agents
- Adapts its coaching style to your background (senior architect vs. mid-level developer)
- Keeps its knowledge current with Salesforce release notes via automated updates

---

## Prerequisites

1. **Claude Code CLI** — Install it:
   ```bash
   npm install -g @anthropic-ai/claude-code
   ```
2. **Anthropic API key** — Sign up at [console.anthropic.com](https://console.anthropic.com)
3. Set your key:
   ```bash
   export ANTHROPIC_API_KEY=your-key-here
   ```

---

## Quick Start

```bash
git clone https://github.com/chilsai/cta-coach.git
cd cta-coach
claude
```

Claude Code loads `CLAUDE.md` automatically and starts a coaching session. On first run, the coach will ask you three calibration questions to tailor the experience to your level.

---

## Session Commands

All commands are native Claude Code slash commands — type `/` to see them with tab-completion.

| Command | What It Does |
|---------|-------------|
| `/scenario [domain]` | Generate a fresh CTA-style scenario |
| `/mock` | Start a full Review Board simulation |
| `/review [domain]` | Quiz on a specific domain |
| `/feedback` | Get structured feedback on your last solution |
| `/tradeoffs [topic]` | Deep dive on architectural trade-offs |
| `/antipatterns [domain]` | Common CTA mistakes to avoid |
| `/update-knowledge` | Pull latest Salesforce release notes |
| `/status` | Show session status and domains covered |

**Domain options:** `integration`, `iam`, `sharing`, `data`, `devops`, `platform`, `app-builder`

---

## Coaching Approach

### Adaptive Calibration
On first session, the coach asks three questions:
- Which domains are you strongest in?
- Which domains concern you most?
- Have you attempted the Review Board before?

It adjusts depth, challenge level, and focus for the rest of the session — and won't ask again.

| Profile | Style |
|---------|-------|
| **Architect / Senior Dev** | Skips basics; goes straight to trade-off depth, edge cases, governor limits |
| **Mid-level Admin/Dev** | Explains the *why* behind patterns; builds up to the trade-off layer |
| **Early-career / New to SF** | Starts with mental models; scaffolds platform constraints before architecture |

### The SPADE Framework
Every solution you present is coached against this structure:

| | Stands For | Meaning |
|-|-----------|---------|
| **S** | Scope | What's in and out of scope |
| **P** | Principles | Architecture principles guiding decisions |
| **A** | Alternatives | At least 2-3 options with trade-offs |
| **D** | Decision | Recommended approach with justification |
| **E** | Evolution | How it scales and adapts over time |

### Decision Framing
You'll be coached to always frame decisions as:
> *"I chose X over Y because [business driver], and the trade-off I'm accepting is [trade-off]. I would revisit this if [condition]."*

---

## Review Board Simulation

The `/mock` command runs a full simulation:

1. You receive a scenario — read it and ask clarifying questions (5 minutes)
2. Present your solution (target: 20–25 minutes)
3. The coach asks judge-style probing questions
4. You receive a scored feedback report

### Scoring Rubric

| Dimension | Weight |
|-----------|--------|
| Requirements Analysis | 15% |
| Solution Architecture Quality | 30% |
| Trade-off Analysis | 20% |
| Platform Knowledge Depth | 20% |
| Response to Questions | 15% |

**Pass threshold:** Average ≥ 3.5 with no dimension below 2.

---

## Domains Covered

| Domain | Agent File | Topics |
|--------|-----------|--------|
| Platform App Builder | `agents/app-builder-and-developer-agents.md` | Declarative dev, Flow, process automation |
| Data Architecture | `agents/data-architect-agent.md` | Data modeling, LDV, migration, governance |
| Platform Developer | `agents/app-builder-and-developer-agents.md` | Apex, LWC, APIs |
| Sharing & Visibility | `agents/sharing-visibility-agent.md` | OWD, sharing rules, Shield, row-level security |
| Dev Lifecycle & Deployment | `agents/devops-agent.md` | CI/CD, ALM, sandboxes, packaging |
| Identity & Access Management | `agents/iam-agent.md` | SSO, OAuth, MFA, Connected Apps |
| Integration Architecture | `agents/integration-agent.md` | APIs, middleware, Platform Events, MuleSoft |

---

## Project Structure

```
cta-coach/
├── CLAUDE.md                               # Main orchestrator (loaded automatically)
├── README.md                               # This file
├── cta-rubric.md                           # Review Board scoring rubric
├── scenario-001-global-manufacturing.md    # Practice scenario
├── .claude/
│   └── commands/                           # Native Claude Code slash commands
│       ├── scenario.md                     # /scenario [domain]
│       ├── mock.md                         # /mock
│       ├── review.md                       # /review [domain]
│       ├── feedback.md                     # /feedback
│       ├── tradeoffs.md                    # /tradeoffs [topic]
│       ├── antipatterns.md                 # /antipatterns [domain]
│       ├── update-knowledge.md             # /update-knowledge
│       └── status.md                       # /status
├── agents/
│   ├── app-builder-and-developer-agents.md # Platform + Developer coach
│   ├── data-architect-agent.md             # Data architecture coach
│   ├── devops-agent.md                     # DevOps & deployment coach
│   ├── iam-agent.md                        # Identity & access coach
│   ├── integration-agent.md                # Integration architecture coach
│   ├── sharing-visibility-agent.md         # Sharing & visibility coach
│   └── knowledge-updater-agent.md          # Automated knowledge sync agent
└── knowledge/
    ├── salesforce-architect-framework.md   # Well-Architected Framework reference
    ├── domain-guides/                      # Per-domain study guides
    │   ├── integration-architecture.md
    │   ├── identity-access-management.md
    │   ├── sharing-and-visibility.md
    │   ├── data-architecture.md
    │   ├── dev-lifecycle-and-deployment.md
    │   └── platform-app-builder-and-developer.md
    ├── scenarios/                          # Practice CTA scenarios
    └── release-notes/                      # Auto-updated by /update-knowledge
```

---

## Keeping Knowledge Current

Salesforce releases three times a year (Spring, Summer, Winter). The knowledge updater keeps coaching content aligned.

### Manual Update
```bash
# Inside a Claude Code session
/update-knowledge
```

### Automated via GitHub Actions
Add `ANTHROPIC_API_KEY` as a repository secret, then create `.github/workflows/knowledge-update.yml`:

```yaml
name: Salesforce Knowledge Update
on:
  schedule:
    - cron: '0 6 * * 1'  # Every Monday at 6 AM UTC
  workflow_dispatch:

jobs:
  update-knowledge:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install Claude Code
        run: npm install -g @anthropic-ai/claude-code
      - name: Run Knowledge Updater
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          claude --agent agents/knowledge-updater-agent.md \
                 --print "Run the full knowledge update workflow"
      - name: Commit updates
        run: |
          git config user.email "cta-coach-bot@noreply.com"
          git config user.name "CTA Coach Bot"
          git add knowledge/
          git diff --staged --quiet || git commit -m "chore: knowledge update $(date +%Y-%m-%d)"
          git push
```

---

## Certification Paths Covered

All seven architect sub-certifications feed into the CTA:

- Salesforce Certified Platform App Builder
- Salesforce Certified Data Architect
- Salesforce Certified Platform Developer I
- Salesforce Certified Sharing and Visibility Architect
- Salesforce Certified Development Lifecycle and Deployment Architect
- Salesforce Certified Identity and Access Management Architect
- Salesforce Certified Integration Architect
- **Salesforce Certified Technical Architect (CTA)** ← the goal

---

## Contributing

Contributions welcome — especially:
- Additional CTA scenarios
- Domain guide improvements
- Knowledge updater coverage for new Salesforce products

Open a PR with a clear description of what changed and why.

---

## Resources

- [Salesforce Architect Center](https://architect.salesforce.com/docs/)
- [Salesforce Well-Architected Framework](https://architect.salesforce.com/well-architected/overview)
- [Trailhead Certification Hub](https://trailhead.salesforce.com/credentials/findacredential)
- [Salesforce Release Notes](https://help.salesforce.com/s/articleView?id=release-notes.salesforce_release_notes.htm)

---

## Disclaimer

This project is not affiliated with, endorsed by, or officially connected to Salesforce. "Salesforce" and "Certified Technical Architect" are trademarks of Salesforce, Inc. This is a community coaching tool built by practitioners, for practitioners.
