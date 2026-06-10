# SLOs & Error Budgets: The Speedometer for Your Service

**Date:** June 11, 2026  
**Topic:** ⚡ SRE & Automation

---

## 🧒 Like you're 5

Imagine your parents say you can watch **99 minutes** of cartoons every day. That's your "budget." Some days you watch 100 minutes — that's *one minute over*. Some days you only watch 30. The minutes you *didn't* use? They roll over. But if you keep going over, your parents notice and say "we need to fix this."

An **SLO** is that "99 minutes" promise — it's how reliable you *promise* to be. The **error budget** is the minutes you're *allowed* to miss. When the budget is gone, you stop shipping new features and focus on fixing reliability.

---

## ↓ Now let's call it what it really is ↓

## 🔧 For the engineer

### SLO (Service Level Objective)
A **measurable target** for service reliability, expressed as a percentage over a time window. Example: "99.9% of requests succeed over 30 days." This is the *contract* between your team and your users.

### SLI (Service Level Indicator)
The **actual measured value** — what your monitoring says *right now*. If your SLO is 99.9% but your SLI is 99.7%, you're burning budget faster than planned.

### Error Budget
The **allowed amount of unreliability**. 99.9% SLO = 0.1% error budget = ~43.2 minutes of downtime per month. This budget is a *first-class resource* — spend it on feature velocity, or save it for stability.

### Burn Rate
How **fast** you're consuming the error budget. A burn rate of 1x means you'll exhaust the budget exactly at the end of the window. 6x means you'll run out 6x faster — *page someone*.

### Multi-Window Alerting (Google SRE)
Alert on *both* fast burns (small window, high burn rate) and slow burns (large window, moderate rate). This catches both incidents and chronic reliability decay.

### Prometheus SLO Alert Rule

```yaml
# Alert when error budget burns too fast (1h window, 5% budget consumed)
- expr: |
    (
      1 - (
        sum(rate(http_requests_total{status=~"5.."}[1h]))
        /
        sum(rate(http_requests_total[1h]))
      )
    ) < (1 - 0.001)  # 99.9% SLO
  for: 5m
  labels:
    severity: critical
    team: sre
  annotations:
    summary: "Fast error budget burn detected"
```

### Error Budget Policy

| Budget Remaining | Action |
|---|---|
| 0-50% | Slow down feature deploys, increase canary percentage |
| 50-20% | Freeze non-critical deploys, focus on reliability backlog |
| <20% | All hands on reliability — no new features until budget recovers |
| 0% exhausted | Service enters "reliability mode" — SRE has deploy veto power |

---

## ⚡ Micro-action

**Today's 5-minute task:** Pick *one* service you own. Write down its current SLI for availability (from your monitoring). Calculate: what's the error budget in *minutes per month*? If you don't have an SLO yet, draft one — even 99% is better than nothing. Put it in a doc. That's your starting point.

---

⚡ SRE & Automation · 💫 Small steps. Every day.
