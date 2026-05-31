# SRE Concepts Cheatsheet

## SLO / SLI / SLA / Error Budget

| Term | Definition | Example |
|---|---|---|
| **SLI** | Metric being measured | Request success rate |
| **SLO** | Target for the SLI | 99.9% success rate |
| **SLA** | Contract with consequences | 99.5% or credit back |
| **Error Budget** | 1 - SLO target | 0.1% = 43.8 min/month |

### Error Budget Math
```
Monthly error budget (99.9% SLO) = 30d × 24h × 60m × 0.001 = 43.2 minutes
Weekly error budget = 7 × 24 × 60 × 0.001 = 10.08 minutes

Burn rate = (actual error rate) / (1 - SLO)
Fast burn (page now) = 14.4x burn rate for 1h window
Slow burn (ticket) = 6x burn rate for 6h window
```

## Incident Severity Levels

| Level | Impact | Response Time | Example |
|---|---|---|---|
| **P0** | Full outage | Immediate, all hands | Site down |
| **P1** | Major degradation | < 15 min | Core feature broken |
| **P2** | Partial impact | < 1 hour | Non-critical feature down |
| **P3** | Minor issue | < 4 hours | UI bug, slow endpoint |
| **P4** | No user impact | Next sprint | Tech debt, monitoring gap |

## DORA Metrics — Elite Benchmarks (2024)

| Metric | Elite | Target |
|---|---|---|
| Deployment Frequency | Multiple/day | Daily |
| Lead Time for Changes | < 1 hour | < 1 day |
| Change Failure Rate | 0–5% | < 5% |
| Time to Restore (MTTR) | < 1 hour | < 1 day |

## Incident Response Runbook Template

```markdown
## Incident: [Title]
**Severity:** P1 | **Status:** Active | **IC:** @name

### Timeline
- HH:MM — Alert fired: [alert name]
- HH:MM — IC declared incident
- HH:MM — [action taken]

### Impact
- Services affected:
- Users affected:
- Error rate:

### Root Cause (working theory)
[Hypothesis]

### Mitigation Steps
1. [Step 1]
2. [Step 2]

### Resolution
[What fixed it]

### Action Items (post-mortem)
- [ ] Owner: Fix | Due: Date
```

## Key SRE Interview Questions

**Q: How do you decide what to page on vs. ticket on?**
Page = user-facing SLO burn rate is fast (P0/P1). Ticket = slow burn, internal only, recoverable. Avoid alert fatigue — noisy on-call leads to alert blindness.

**Q: What makes a good post-mortem?**
Blameless, focused on systemic issues not individuals, 5-why root cause, clear action items with owners and dates, shared widely. Bad ones: blame-focused, shallow RCA, no follow-through.

**Q: How do you set an SLO for a new service?**
1. Start with user expectations / business impact thresholds
2. Measure current baseline over 30 days
3. Set target slightly above baseline to establish baseline budget
4. Tighten quarterly as reliability improves
5. Never set 100% — it incentivizes zero risk-taking
