# Grafana Installation (Kubernetes)

## Step 1: Create Namespace

```bash
kubectl create namespace monitoring
```

---

## Step 2: Install Grafana

```bash
kubectl apply -f https://raw.githubusercontent.com/grafana/grafana/main/devenv/k8s/custom.yaml
```

---

## Step 3: Verify Installation

```bash
kubectl get pods -n monitoring
```

---

## Step 4: Check Services

```bash
kubectl get svc -n monitoring
```