# NGINX Ingress Controller Installation (Kind)

## Step 1: Install NGINX Ingress Controller

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```

---

## Step 2: Verify Installation

```bash
kubectl get pods -n ingress-nginx
```

Wait until all pods are in the **Running** state.

---

## Step 3: Verify Service

```bash
kubectl get svc -n ingress-nginx
```

---

## Step 4: Verify Deployment

```bash
kubectl get deployment -n ingress-nginx
```