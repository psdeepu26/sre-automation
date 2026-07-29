# Alert Fatigue Budgets: Make Paging Scarce

**Topic:** SRE & Automation  
**Date:** 2026-07-30  
**Type:** Personal learning insight

## 🧒 Like you're 5

A fire alarm that screams every time someone burns toast gets ignored. A good alarm only rings when there is a real fire, because everyone trusts it. Your on-call system needs the same rule: every page should be important enough that interrupting sleep is worth it.

## ↓ Now let's call it what it really is ↓

## 🔧 For the engineer

An **alert fatigue budget** is a lightweight reliability guardrail for the paging surface. It limits low-value interruptions and forces each alert to prove that it is actionable, urgent, and tied to a user-impacting failure mode.

### Page quality test

Page only when all three are true:

- Human action is required now.
- Delay causes material user or business impact.
- The responder has a documented first action.

### Budget signal

Track pages per service per week, plus:

- Repeat pages for the same incident
- False-positive rate
- Pages with no follow-up action

### Routing policy

Not urgent? Route it to a ticket or dashboard annotation. Urgent but automated? Let automation remediate first and page only if the remediation fails.

### Useful PromQL

```promql
sum by (service) (
  increase(oncall_page_total[7d])
)
```

A practical starting policy is: review any service above **5 pages per on-call week**, and retire or downgrade alerts that generated no responder action in the last month. Tune the number to your team; the important part is making the budget visible and reviewable.

## ⚡ Micro-action (5 minutes)

Open the last seven days of paging history. Pick the three most frequent alerts and label each **keep, ticket, automate, or delete**. For every alert marked **keep**, write its one-sentence first responder action in the rule or runbook.

---

*SRE & Automation · 💫 Small steps. Every day.*
