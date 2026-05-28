# 🚨 Incident Response Flow

## 🧒 Like You're 5

Imagine you're in a **school kitchen** and the **fire alarm goes off**. Here's what you do:

1. **🔔 "Is it real?"** — Check if it's a drill or actual smoke.
2. **🔍 "How bad?"** — Whole kitchen (SEV1) or just one pan (SEV2)?
3. **🪣 "Put out the fire FIRST"** — Don't debug the cause while it's burning.
4. **✅ "All clear"** — Fire's out. Tell everyone.
5. **📝 "How did this happen?"** — After the dust settles, figure out root cause.

> **The golden rule:** 🚒 Put out the fire FIRST. Figure out the cause AFTER.

---

## 🔧 For the Engineer

### 1. Detection
Alert fires via **Prometheus AlertManager**, **Grafana**, or **PagerDuty**. First responder acknowledges within SLA. Use hysteresis thresholds to reduce noise.

### 2. Triage
- **SEV1:** Users impacted, revenue down
- **SEV2:** Degraded experience
- **SEV3:** Cosmetic/internal

### 3. Mitigation
Fix the symptom FIRST: rollback, scale up, redirect traffic, feature flag toggle off.

### 4. Resolution
Service healthy, monitoring baseline. De-escalate, notify "all clear."

### 5. Postmortem
Blameless postmortem within 48h. **Timeline → Root cause → Impact → Action items.** Fix the system, not the person.

### Error Budgets
99.9% SLO = 8.76h downtime/year. Every minute shaved off MTTR increases your error budget.

---

⚡ **Micro-action:** Check if your team has an incident response runbook. If not, start one with these 5 stages.