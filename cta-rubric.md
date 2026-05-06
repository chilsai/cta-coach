# CTA Review Board Scoring Rubric

## Overview
The Salesforce Certified Technical Architect (CTA) Review Board assesses candidates on their ability to architect solutions to complex, multi-domain business problems. This rubric mirrors the evaluation criteria used by the Review Board.

## Scoring Dimensions (1-5 scale per dimension)

### 1. Requirements Analysis (Weight: 15%)
| Score | Description |
|-------|-------------|
| 5 | Identifies ALL stated and implied requirements; asks insightful clarifying questions; uncovers hidden NFRs |
| 4 | Identifies most requirements; asks some good clarifying questions |
| 3 | Identifies primary requirements; misses some NFRs; limited clarifying questions |
| 2 | Misses key requirements; jumps to solution too quickly |
| 1 | Fails to analyze requirements; presents generic solution |

### 2. Solution Architecture Quality (Weight: 30%)
| Score | Description |
|-------|-------------|
| 5 | Elegant, scalable solution; clearly justified; well-structured presentation; considers future state |
| 4 | Solid solution with good justification; minor gaps |
| 3 | Adequate solution; some unjustified decisions; misses some scalability concerns |
| 2 | Overly complex or oversimplified; significant unjustified decisions |
| 1 | Solution is incorrect or unworkable |

### 3. Trade-off Analysis (Weight: 20%)
| Score | Description |
|-------|-------------|
| 5 | Presents multiple credible options; explicitly calls out trade-offs; recommends with clear rationale |
| 4 | Presents alternatives with most trade-offs identified |
| 3 | Mentions alternatives but trade-offs are superficial |
| 2 | Only presents one option or trade-offs are wrong |
| 1 | No trade-off analysis |

### 4. Platform Knowledge Depth (Weight: 20%)
| Score | Description |
|-------|-------------|
| 5 | Deep, current knowledge; references specific limits/features accurately; knows when NOT to use platform features |
| 4 | Strong knowledge with minor gaps |
| 3 | Adequate knowledge; some outdated or incorrect details |
| 2 | Superficial knowledge; misses important platform constraints |
| 1 | Incorrect platform knowledge that would lead to failed implementation |

### 5. Response to Questions (Weight: 15%)
| Score | Description |
|-------|-------------|
| 5 | Confident, precise answers; acknowledges uncertainty honestly; pivots gracefully under pressure |
| 4 | Generally good answers; slight hesitation on some areas |
| 3 | Answers most questions; struggles with deep follow-ups |
| 2 | Defensive or evasive; cannot defend architectural decisions |
| 1 | Cannot answer follow-up questions; contradicts own solution |

## Passing Threshold
- **Pass:** Average ≥ 3.5 across all dimensions, with no dimension below 2
- **Fail:** Average < 3.5 OR any dimension scored 1

## Common Failure Modes

### The "Too Much Detail Too Fast" Failure
Candidate dives into implementation details before establishing the architectural vision. Board never sees the big picture.

**Coaching fix:** Practice leading with a 60-second architecture overview before any detail.

### The "Single Solution" Failure  
Candidate presents only one option, as if it's obviously correct. Board always probes: "What else did you consider?"

**Coaching fix:** Always present 3 options minimum. Even if one is clearly best, articulate why you're NOT choosing the others.

### The "Ignoring Constraints" Failure
Candidate designs the perfect solution ignoring the stated budget, timeline, or existing technology constraints.

**Coaching fix:** Repeat constraints back in the solution: "Given the 3-month timeline, I'm recommending declarative-first..."

### The "Crumbling Under Questions" Failure
Candidate makes decisions they can't defend. When a judge probes, they backtrack or change their answer.

**Coaching fix:** Only recommend what you can defend. Use: "That's a great question — here's my reasoning..."

### The "Missing the Forest for the Trees" Failure
Candidate knows details but can't connect them to business value. Judges are executives, not developers.

**Coaching fix:** Always tie technical decisions to business outcomes: "This approach reduces integration latency, which directly impacts customer satisfaction scores."

## Judge Question Bank

### Opening Questions (Always Asked)
1. "Tell us about your solution at a high level before we get into details."
2. "What were the key architectural decisions you made and why?"
3. "What requirements did you feel were ambiguous and how did you address them?"

### Pressure Questions (Asked When Candidate Seems Confident)
1. "Your competitor would design this completely differently — they'd use [alternative]. Why are they wrong?"
2. "The business comes back 6 months later with 10x the users. Does your architecture hold?"
3. "The client just signed a contract with [specific vendor]. How does that change your design?"

### Trap Questions (Testing Knowledge Depth)
1. "Walk me through exactly what happens at the API layer when this fires"
2. "What's the exact governor limit you're referencing there?"
3. "That feature was deprecated in [recent release] — what's the replacement?"

### Business Value Questions
1. "How would you present this architecture to the CFO who controls the budget?"
2. "What's the ROI of your recommended approach vs. the cheaper alternative?"
3. "What would you tell the CEO if this project runs 3 months late?"

## Session Feedback Template

After each mock session, provide feedback in this format:

```
## Mock Review Board Feedback — [Candidate Name] — [Date]

### Overall Score: [X.X / 5.0] — [PASS / NEAR PASS / FAIL]

### Dimension Scores
- Requirements Analysis: [X/5] — [One sentence note]
- Solution Architecture: [X/5] — [One sentence note]  
- Trade-off Analysis: [X/5] — [One sentence note]
- Platform Knowledge: [X/5] — [One sentence note]
- Q&A Response: [X/5] — [One sentence note]

### What You Did Well
1. [Specific positive behavior]
2. [Specific positive behavior]

### Priority Improvements (Focus on These)
1. [Specific gap with actionable coaching tip]
2. [Specific gap with actionable coaching tip]

### Recommended Next Focus Area
[One specific thing to study/practice before next session]

### Suggested Next Session
[Scenario type or domain to tackle next]
```
