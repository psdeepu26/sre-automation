# Blameless Postmortems: Turning Incidents Into Institutional Knowledge

**Topic:** SRE & Automation
**Date:** 2026-06-18
**Category:** sre-automation

---

## 🧒 Like You're 5

Imagine you and your friends are building a big LEGO tower and it suddenly falls down. The *worst* thing you could do is yell "WHO DID THIS?!" — because now nobody wants to help rebuild it, and the same thing will fall again tomorrow.

The *best* thing? Gather everyone around and ask: **"What happened, and how do we make sure it never falls again?"** Nobody gets in trouble. You write down what you learned. Next time, your tower is stronger. That's a blameless postmortem — it's not about who broke it, it's about making the system unbreakable.

---

## 🔧 For the Engineer

In SRE, incidents are inevitable. What separates mature organizations from chaotic ones isn't *fewer* incidents — it's what happens *after*. A blameless postmortem (also called a retrospective or incident review) is a structured, blame-free analysis of an incident that focuses on systemic causes and preventive actions.

### The Core Principles

- **Blameless culture** — People aren't punished for mistakes. The goal is to understand *why* the system allowed the mistake to cause an outage, not *who* made it.
- **Systems thinking** — Every incident has multiple contributing causes. Fixing just the proximate cause (the trigger) misses the latent conditions that made it possible.
- **Actionable follow-ups** — Every postmortem produces concrete action items with owners and deadlines. A postmortem without action items is just a story.
- **Shared learning** — Postmortems are read by other teams. The best orgs have "learning libraries" where anyone can search past incidents.

### Postmortem Template (Minimal)

```markdown
# Incident Postmortem: [Short Title]
**Date:** 2026-06-18
**Severity:** SEV-2 (partial outage)
**Duration:** 47 minutes
**Author:** [Your Name]
**Status:** Draft / Final

## Summary
One-paragraph description of what happened and the impact.

## Impact
- Users affected: ~12,000 requests failed
- Revenue impact: ~$4,200
- Duration: 14:23 - 15:10 UTC

## Timeline (UTC)
| Time | Event |
|------|-------|
| 14:23 | Alert: error rate spike on /api/v2/search |
| 14:28 | On-call acknowledges, begins investigation |
| 14:35 | Identified: new deployment at 14:15 changed DB schema |
| 14:42 | Rollback initiated |
| 14:55 | Service recovering, monitoring |
| 15:10 | Full recovery confirmed |

## Root Cause
The deployment at 14:15 (PR #4821) added a non-nullable column
to the `search_index` table without a default value. The migration
ran successfully on staging (small dataset) but locked the table
for 8 minutes on production (40M rows), causing cascading failures.

## Contributing Factors
1. No canary analysis for schema migrations
2. Migration timeout not configured (default: unlimited)
3. Staging dataset is 1000x smaller than production
4. No automated rollback trigger for migration failures

## Action Items
| Action | Owner | Due | Priority |
|--------|-------|-----|----------|
| Add migration timeout (max 30s) | @alice | 6/25 | P1 |
| Implement canary for schema changes | @bob | 7/2 | P1 |
| Mirror prod data size in staging | @carol | 7/16 | P2 |
| Auto-rollback on migration failure | @dave | 7/9 | P1 |

## What Went Well
- On-call responded within 5 minutes
- Monitoring caught the spike immediately
- Rollback procedure worked as documented

## What Went Poorly
- Migration ran for 8 minutes before anyone noticed
- No automated guard for long-running migrations
- Staging didn't catch the issue due to data size gap
```

### Key Metrics to Track

- **MTTR (Mean Time to Recovery)** — How fast you recover. Track per severity level.
- **MTTD (Mean Time to Detect)** — How fast you notice. If this is high, your monitoring has gaps.
- **Incident frequency by service** — Hotspots that need architectural attention.
- **Action item completion rate** — If postmortem actions don't get done, the process is theater.

### ⚠️ The "Second Victim" Problem

The engineer who pushed the bad change often feels terrible — sometimes worse than anyone else. In a blameless culture, we recognize this person as the "second victim" and support them. They have the most context about what happened and are best positioned to prevent recurrence. Blaming them drives them away and loses that knowledge forever.

### Postmortem Anti-Patterns

- **The blame game** — "Alice shouldn't have merged without review." This shuts down honesty.
- **The novel** — 20-page documents nobody reads. Keep it under 2 pages.
- **The action item graveyard** — Creating JIRA tickets that never get prioritized.
- **The hero narrative** — "Dave saved the day!" This incentivizes heroics over prevention.
- **Skipping postmortems for "small" incidents** — Near-misses are free learning. Capture them too.

---

## ⚡ Micro-Action (5 minutes)

**Create a postmortem template in your team's wiki or repo *today*.** Copy the minimal template above into a file called `postmortem-template.md` in your team's runbook repo. The next time an incident happens, you won't waste the first 30 minutes figuring out what to write — you'll just fill in the blanks. The best time to set up the process is when you *don't* need it.
