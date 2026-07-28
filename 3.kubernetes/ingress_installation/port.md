# NGINX Ingress Controller Access

## Port Forward NGINX Ingress Controller

```bash
kubectl port-forward -n ingress-nginx svc/ingress-nginx-controller 8081:80
```

---

## Access Applications Through Ingress

Open your browser:

```
http://localhost:8081
```