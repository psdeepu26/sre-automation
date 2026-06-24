# Autoscaling Policy Thresholds: Tuning Your Safety Nets

> SRE & Automation · Thursday, June 25, 2026

## 🧒 Like You're 5

Imagine a swimming pool with an automatic "add more water" machine. If you set it to turn
on whenever someone jumps in, it'll be switching on and off constantly — wasting energy and
wearing out the motor. But if you set it to wait until half the pool is empty, everyone will
be swimming in puddles before it kicks in.

Autoscaling is that machine. The threshold is the line you draw that says "OK, NOW we need
more water." Draw it too low → sluggish response. Too high → chaotic on-off cycling
(thrashing). The art is picking the right line and adding a grace period — "wait 2 minutes
before deciding" — so panic doesn't trigger unnecessary outrage.

## ↓ The threshold is a policy decision disguised as a technical one.

## 🔧 For the Engineer

### Scaling Type Matrix

| Scaling Type | When It Shines | Common Pitfall |
|---|---|---|
| HPA (reactive) | Predictable HTTP workloads | Cold-start latency hiding behind metrics |
| VPA (reactive) | Right-sizing containers | Conflicts with HPA on same metric |
| Predictive | Diurnal traffic patterns | Over-provisioning at startup |
| KEDA (event-driven) | Queue/backlog-based services | Lag between event and metric visibility |

### HPA Tuning (Kubernetes)

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: api-server
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: api-server
  minReplicas: 3
  maxReplicas: 50
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300  # Wait 5 min before scale-down
      policies:
      - type: Percent
        value: 25                    # Max 25% removed per 60s
        periodSeconds: 60
    scaleUp:
      stabilizationWindowSeconds: 0   # React immediately on spike
      policies:
      - type: Percent
        value: 100                   # Double pods per 60s if needed
        periodSeconds: 60
      - type: Pods
        value: 4                     # Or flat 4 pods, whichever is more
        periodSeconds: 60
      selectPolicy: Max
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 65      # Not 50 — accounts for startup lag
```

### Anti-Thashing KPIs

| Problem | Solution |
|---|---|
| Rapid up/down cycling | Stabilization window (sustained-signal guard) |
| Hovering near threshold | Hysteresis gap (separate up/down thresholds) |
| Shock from large changes | Step boundaries (max pods changed per interval) |

## ⚡ Micro-Action

**Audit your HPA stabilization windows today:**

```bash
kubectl get hpa -o yaml | grep -A5 stabilizationWindow
```

If `scaleDown.stabilizationWindowSeconds` is 0 or 30, bump it to 300 (5 minutes).
This single change prevents most flapping without affecting scale-up responsiveness.
