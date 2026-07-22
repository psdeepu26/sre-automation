# ⚡ Safe Runbook Automation: Idempotency, Preconditions, and Rollback

**SRE & Automation · Runbook Safety** · 23 July 2026 · Thursday

---

## 🧒 Like you're 5

Imagine a robot mechanic told to “tighten the loose wheel.” A careless robot
keeps turning the bolt forever. A safe robot first checks whether the wheel is
loose, tightens it once, checks again, and stops—or undoes the change if
something looks wrong.

Automation should behave like the safe robot: inspect, act only when needed,
prove the result, and know how to retreat.

---

↓ Now let’s call it what it really is ↓

## 🔧 For the engineer

A production runbook becomes safe automation when every action has an explicit
contract. Re-running it must converge on the same desired state, stale
observations must not trigger changes, and failure must remain inside a bounded
blast radius.

**Observe → Validate → Act → Verify → Rollback / Escalate**

| Guardrail | Contract |
|---|---|
| **Idempotency** | Repeated execution reaches the same state. Read before writing; use desired-state APIs, compare-and-swap, or idempotency keys. |
| **Preconditions** | Act only while assumptions remain true: alert still firing, version unchanged, quorum healthy, observation fresh. |
| **Blast-radius limits** | Change one shard, host, zone, or percentage at a time. Cap retries, concurrency, duration, and affected resources. |
| **Postconditions** | Define measurable success first. Verify service health and user signals—not only command exit code. |
| **Rollback** | Capture prior state and define a compensating action. If rollback is unsafe, stop and page a human. |
| **Auditability** | Record trigger, evidence, actor, decision, mutation, verification, and correlation ID. |

### Minimal remediation contract

```python
def remediate(target, observed_version):
    state = read_state(target)

    if state.version != observed_version:       # stale evidence
        return "abort: state changed"
    if state.desired or not alert_still_firing(target):
        return "noop: action no longer needed"  # idempotent
    if affected_targets_last_hour() >= 1:
        return "abort: blast-radius budget exhausted"

    previous = state.snapshot()
    apply_desired_state(target, if_version=state.version)

    if not verify_user_signal(target, timeout="5m"):
        restore(previous)
        page_human(target)
        return "rolled back"

    return "verified"
```

### Promotion path

```text
Manual runbook
  → dry-run with evidence output
  → human-approved execution
  → one-target canary automation
  → bounded auto-remediation
```

Promote only after measuring false positives, rollback success, and mean time
to verify.

> **Safety rule:** automate the decision boundary, not only the shell command.
> A perfectly executed command can still be the wrong action when its evidence
> is stale.

---

## ⚡ Micro-action (5 minutes)

Pick one manual runbook step. Add four lines beneath it: `precondition`,
`idempotency check`, `success signal`, and `rollback/stop condition`. If any
line is unclear, that step is not ready for unattended automation.

---

**SRE & Automation · Runbook Safety** · 💫 Small steps. Every day.
