# Grafana Dashboard Access

## Port Forward Grafana

```bash
kubectl port-forward -n monitoring svc/kube-prometheus-stack-grafana 3000:80
```

---

## Access Grafana Dashboard

Open your browser:

```
http://localhost:3000
```
