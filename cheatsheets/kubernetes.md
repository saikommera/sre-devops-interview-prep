# Kubernetes Interview Cheatsheet

## Essential kubectl Commands

```bash
# Context & cluster
kubectl config get-contexts
kubectl config use-context <context>
kubectl cluster-info

# Pod debugging
kubectl get pods -A --sort-by='.status.phase'
kubectl describe pod <pod> -n <ns>
kubectl logs <pod> -c <container> --previous --tail=100
kubectl exec -it <pod> -- /bin/bash

# Events (crucial for debugging)
kubectl get events -n <ns> --sort-by='.lastTimestamp' | tail -20

# Resource usage
kubectl top nodes
kubectl top pods -A --sort-by=cpu

# Scale
kubectl scale deployment <name> --replicas=5
kubectl rollout restart deployment/<name>
kubectl rollout status deployment/<name>
kubectl rollout undo deployment/<name>

# HPA
kubectl get hpa -A
kubectl describe hpa <name>
```

## SRE Concepts

### Error Budget Calculation
```
Error Budget = 1 - SLO target
For 99.9% SLO: Error budget = 0.1% = 43.8 min/month
```

### DORA Metrics
| Metric | Elite | High | Medium | Low |
|---|---|---|---|---|
| Deployment Frequency | Multiple/day | Daily/weekly | Weekly/monthly | Monthly |
| Lead Time | < 1 hour | 1 day–1 week | 1 week–1 month | > 1 month |
| MTTR | < 1 hour | < 1 day | < 1 week | > 1 week |
| Change Failure Rate | 0–5% | 5–10% | 10–15% | > 15% |

## Common Interview Questions & Answers

**Q: How do you debug a pod stuck in CrashLoopBackOff?**
1. `kubectl describe pod` — check Events section for OOMKilled, liveness probe failures
2. `kubectl logs --previous` — get logs from last crashed container
3. Check resource limits (memory limits too low → OOMKilled)
4. Verify liveness/readiness probe paths and timing
5. Check init containers completed successfully

**Q: Explain the difference between Deployment, StatefulSet, DaemonSet**
- Deployment: stateless apps, pods are interchangeable, random names
- StatefulSet: stateful apps (DBs), stable network IDs, ordered deploy/scale
- DaemonSet: runs one pod per node (log collectors, monitoring agents)

**Q: How does HPA work? What metrics can it use?**
HPA queries metrics server every 15s and adjusts replica count based on:
- CPU/memory utilization (built-in)
- Custom metrics (Prometheus adapter)
- External metrics (Datadog, cloud metrics)
Formula: `desiredReplicas = ceil(currentReplicas × currentMetric/targetMetric)`
