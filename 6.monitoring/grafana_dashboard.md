# Prometheus Dashboard Access

## Port Forward Prometheus

```bash
kubectl port-forward -n monitoring svc/kube-prometheus-stack-grafana 3000:80
```

---

## Access Prometheus Dashboard

Open your browser:

```
http://localhost:3000
```
