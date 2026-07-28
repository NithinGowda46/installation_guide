# Prometheus Installation (Kubernetes)

## Step 1: Create Namespace

```bash
kubectl create namespace monitoring
```

---

## Step 2: Install Prometheus

```bash
kubectl apply -f https://raw.githubusercontent.com/prometheus-operator/prometheus-operator/main/bundle.yaml
```

---

## Step 3: Install Prometheus Stack

```bash
kubectl apply -f https://raw.githubusercontent.com/prometheus-community/helm-charts/main/charts/kube-prometheus-stack/crds/crd-alertmanagerconfigs.yaml
```

> **Note:** If you're not using Helm, use the kube-prometheus manifests or Prometheus Operator manifests instead of Helm charts.

---

## Step 4: Verify Installation

```bash
kubectl get pods -n monitoring
```

---

## Step 5: Check Services

```bash
kubectl get svc -n monitoring
```