# ⚡ SLOs & Error Budgets: The Reliability Contract

**SRE & Automation** · Day #1 · 11 Jun 2026

---

## 🧒 Like You're 5

Imagine your parents say you can have **30 minutes of screen time** every day. That's your "budget." Some days you use 20 minutes, some days you use all 30. As long as you don't go over, you're fine.

But what if you went over yesterday? Well, now you have *less* time today. And if you keep going over every day, your parents might say: "No more games this week — you need to fix your habits first."

That's exactly how **error budgets** work for websites and services. You get a small "budget" of allowed failures (say, 0.1% of requests can fail). As long as you stay within budget, you can keep shipping new features. But once you burn through it? **Feature freezes** — all hands on deck to improve reliability.

---

## 🔧 For the Engineer

| Concept | What It Is |
|---------|------------|
| **SLO** | A measurable target for reliability (e.g., "99.9% of requests succeed over 30 days"). Derived from SLIs. 99.9% ≈ 43 min downtime/month. |
| **SLI** | The actual metric you measure. Availability SLI = `good_requests / total_requests`. Latency SLI = % of requests under threshold (e.g., p99 < 200ms). |
| **Error Budget** | The allowed unreliability — gap between SLO and 100%. 99.9% SLO = 0.1% budget. At 1M req/day = 1,000 allowed failures/day. |
| **Error Budget Policy** | Rules for when budget burns: Green → ship features, Yellow → investigate, Red → freeze releases, focus on reliability. |
| **Burn Rate** | How fast you're consuming the budget. Alert on "will exhaust in 3 days" not "spike right now." |

### SLO Quick Reference

| SLO | Error Budget | Monthly Downtime | Use Case |
|-----|-------------|-----------------|----------|
| 99.9% | 0.1% | ~43 min | Standard production |
| 99.95% | 0.05% | ~22 min | Critical services |
| 99.99% | 0.01% | ~4.3 min | Tier-1 / revenue |
| 99.999% | 0.001% | ~26 sec | Telecom / medical |

### Example SLO Definition (Pyrra)

```yaml
apiVersion: pyrra.dev/v1alpha1
kind: ServiceLevelObjective
metadata:
  name: api-availability
  namespace: monitoring
spec:
  target: "99.9"
  window: 30d
  indicator:
    ratio:
      errors:
        metric: http_requests_total{status=~"5.."}
      total:
        metric: http_requests_total
```

---

## 💡 Why This Matters for You

At NVCF, you're building observability for managed and self-managed clusters. SLOs are the **bridge between raw metrics and business decisions**. Instead of alerting on every spike, SLO-based alerting (using burn rates) tells you: "You're on track to exhaust your budget in 3 days" — giving you time to act *before* users are impacted.

Tools like **Pyrra** (open-source, Kubernetes-native) can auto-generate Prometheus recording rules from SLO definitions. Pair it with **Sloth** or **OpenSLO** for a full SLO-as-code workflow that fits right into your existing Prometheus + Thanos + Grafana stack.

---

⚡ **Micro-action:** Pick **one service** you own or depend on. Write down: (1) What's a good SLI for it? (2) What SLO would you set — 99.9% or 99.99%? (3) How much error budget does that give you per month? Just the act of writing it down changes how you think about reliability.
