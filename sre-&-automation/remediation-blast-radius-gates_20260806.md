# Remediation Blast-Radius Gates: Automate Without Amplifying Failure

**Topic:** SRE & Automation  
**Date:** 2026-08-06  
**Type:** Personal learning insight

## 🧒 Like you're 5

A robot sees one wilted plant and decides every plant needs water. If its guess is wrong, it floods the whole garden. A safer robot waters one pot, waits, checks whether it improved, and stops immediately if the soil gets worse.

## ↓ Now let's call it what it really is ↓

## 🔧 For the engineer

Automated remediation is a control loop, not a shell command. A **blast-radius gate** limits how much infrastructure each loop iteration may change before fresh evidence proves the action helped.

### 1. Preconditions

Act only when independent signals agree, the target is healthy enough to mutate, and no deployment or incident freeze is active.

### 2. Scope limits

Cap targets by count and percentage. Start with one instance or one failure domain; never let a fleet-wide selector become the default.

### 3. Feedback gate

After each batch, wait through a stabilization window. Continue only if the triggering signal improves and guardrail metrics stay healthy.

### 4. Escape paths

Require a retry ceiling, cooldown, global kill switch, audit trail, and safe handoff to a human when evidence is ambiguous.

```yaml
policy:
  max_targets: 1
  max_percent: 5
  cooldown: 10m
  require:
    - trigger_for: 5m
    - no_active_deploy
  verify:
    window: 3m
    success: error_rate_decreased
  abort_if:
    - latency_p99_increased
    - attempts >= 2
```

**Design rule:** automation confidence should control scope. Low confidence means observe or change one target. Scope expands only after measured success—not because the first command returned exit code 0.

## ⚡ Micro-action (5 minutes)

Pick one auto-remediation runbook. Write down four values: **maximum targets per run, stabilization window, success signal, and abort signal**. If any value is undefined, keep that automation in approval-required mode.

---

*SRE & Automation · 💫 Small steps. Every day.*
