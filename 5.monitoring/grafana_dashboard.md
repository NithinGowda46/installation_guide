# Prometheus Dashboard Access

## Port Forward Prometheus

```bash
kubectl port-forward -n monitoring svc/prometheus-k8s 9090:9090
```

---

## Access Prometheus Dashboard

Open your browser:

```
http://localhost:9090
```