# Canary Deployments: Releasing Without the Heart Attack

**Date:** June 18, 2026 (Thursday)
**Topic:** SRE & Automation
**Category:** sre-automation

---

## 🧒 Like you're 5

Imagine you have a new recipe for soup. Instead of serving it to everyone at the dinner table right away, you first give a tiny spoonfill to one person. If they say it tastes great, you serve a few more people. If they make a face, you stop and fix the recipe — and only one person had to eat bad soup. That's a canary deployment: test the new version on a small group of real users before rolling it out to everyone.

---

## 🔧 For the engineer

A **canary deployment** shifts a small percentage of production traffic to a new version while monitoring key metrics. If error rates spike or latency degrades, traffic rolls back automatically — blast radius contained.

| Property | Value |
|----------|-------|
| Strategy | Progressive traffic shift |
| Rollback | Automatic on SLO breach |
| Traffic Split | 1% → 5% → 25% → 100% |
| Key Signal | Error rate, p99 latency |

### Flagger + Istio example — automated canary with Prometheus metrics

```yaml
# canary.yaml — Flagger canary resource
apiVersion: flagger.app/v1beta1
kind: Canary
metadata:
  name: payment-service
  namespace: prod
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: payment-service
  service:
    port: 8080
  analysis:
    interval: 30s
    threshold: 5           # max failed checks before rollback
    maxWeight: 50           # max % traffic to canary
    stepWeight: 10          # increment per iteration
    metrics:
      - name: request-success-rate
        thresholdRange:
          min: 99           # 99% success rate required
        interval: 1m
      - name: request-duration
        thresholdRange:
          max: 500          # p99 under 500ms
        interval: 1m
```

### Rollout progression

```
# Flagger automates this loop:
# T+0m:   1% traffic → canary, 99% → primary
# T+1m:   Check metrics — pass? Increase to 10%
# T+2m:   Check metrics — pass? Increase to 20%
# ...
# T+10m:  50% traffic — all checks pass? Promote to 100%
#         Any check fails? Rollback to 0%, alert fired
```

### Canary vs Blue-Green vs Rolling

| Strategy | Description |
|----------|-------------|
| Canary | Gradual % shift, metric-gated |
| Blue-Green | Instant 100% switch, manual gate |
| Rolling | Replace pods one-by-one, no metric check |
| Shadow | Mirror traffic, no user impact |

---

## ⚡ Micro-action (5 min)

Check if your current deployment pipeline has any canary or progressive delivery built in. If you're doing rolling deployments, look up [Flagger](https://flagger.app) or [Argo Rollouts](https://argoproj.github.io/argo-rollouts/) for your Kubernetes cluster. Even a simple 5% → 100% two-stage canary with error-rate gating catches most bad deploys before users notice.

---

*Tags: SRE, Canary, Progressive Delivery, Kubernetes*
*Repo: psdeepu26/sre-automation*
