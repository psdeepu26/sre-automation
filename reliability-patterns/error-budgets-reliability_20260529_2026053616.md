# ⚡ Error Budgets & Reliability

**Reliability Patterns** · Day #4 · Thu, 29 May 2026

---


## 🧒 Like You're 5

Imagine you have a jar of 100 marbles, and each day you can spend 5 marbles doing fun risky things (like trying skateboard tricks). If you're too risky and lose all your marbles, no more fun until you earn them back. That's an error budget - the amount of "failure" you can afford before everything must stop for repairs.

Google's SRE teams use error budgets to balance innovation with stability. When they exceed their budget, all feature work stops and 100% of focus shifts to reliability.

---

## 🔧 For the Engineer

| Concept | What It Is |
|---------|------------|
| **SLO** | Service Level Objective - the reliability target (e.g., 99.9% uptime) |
| **Error Budget** | The allowed downtime - if SLO is 99.9%, error budget is 0.1% (about 43 minutes/month) |
| **Burn Rate** | How quickly you're consuming your error budget - fast burn = urgent reliability work needed |
| **Postmortem** | After-action review when budget is exceeded to prevent recurrence |
| **Your Stack** | NVCF's observability system tracks SLOs and error budgets automatically |

---

⚡ **Micro-action:** Identify one service where improving monitoring could recover 1+ hour of error budget weekly.