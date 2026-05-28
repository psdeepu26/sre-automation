# 🚨 Incident Response Flow

**SRE** · From alert to postmortem in 5 stages

---

### 01 🔔 Detection
Alert fires (Prometheus AlertManager, Grafana). Is it real or noise? First responder acknowledges within SLA.

### 02 🔍 Triage
Assess severity: SEV1 (users impacted), SEV2 (degraded), SEV3 (cosmetic). Declare incident, notify stakeholders, open bridge.

### 03 🛠️ Mitigation
Fix the symptom first — rollback, scale up, redirect traffic. Restore service before debugging root cause.

### 04 📋 Resolution
Service healthy, monitoring back to baseline. De-escalate, close bridge, notify stakeholders.

### 05 📖 Postmortem
Blameless postmortem within 48h. Timeline, root cause, action items. Fix the system, not the person.

---

> 💡 **Key SRE Principle:** During an incident, the goal is NOT to find root cause — it's to restore service. Root cause analysis happens in stage 5 (postmortem), not stage 3 (mitigation).

---

⚡ **Micro-action:** Does your team have a documented incident response runbook?