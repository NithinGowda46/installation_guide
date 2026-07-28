# Metrics Server Installation (Kind / Kubernetes)

## Step 1: Install Metrics Server

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

---

## Step 2: Patch Metrics Server (Required for Kind)

```bash
kubectl patch deployment metrics-server -n kube-system \
--type='json' \
-p='[{"op":"add","path":"/spec/template/spec/containers/0/args/-","value":"--kubelet-insecure-tls"}]'
```

---

## Step 3: Verify Installation

```bash
kubectl get pods -n kube-system | grep metrics-server
```

Wait until the pod is in the **Running** state.

---

## Step 4: Verify Metrics Server

```bash
kubectl top nodes
```

```bash
kubectl top pods -A
```

---

## Step 5: Check Deployment

```bash
kubectl get deployment metrics-server -n kube-system
```

---

## Step 6: Check Service

```bash
kubectl get svc -n kube-system
```