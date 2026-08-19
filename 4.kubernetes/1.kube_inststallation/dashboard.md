# Kubernetes Dashboard Installation (Kind / Kubernetes)

## Step 1: Deploy Kubernetes Dashboard

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

---

## Step 2: Create Admin User

Create a file named **dashboard-admin-user.yaml**

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

Apply it:

```bash
kubectl apply -f dashboard-admin-user.yaml
```

---

## Step 3: Get Login Token

```bash
kubectl -n kubernetes-dashboard create token admin-user
```

Copy the generated token.

---

## Step 4: Start Port Forwarding

```bash
kubectl port-forward -n kubernetes-dashboard service/kubernetes-dashboard 8443:443
```

---

## Step 5: Access Kubernetes Dashboard

Open your browser:

```
https://localhost:8443
```

Ignore the browser security warning (self-signed certificate).

---

## Step 6: Login

Select:

```
Token
```

Paste the token generated in **Step 3**.

Click:

```
Sign In
```

---

# Verify Installation

Check Dashboard Pods

```bash
kubectl get pods -n kubernetes-dashboard
```

Check Services

```bash
kubectl get svc -n kubernetes-dashboard
```

Check Service Account

```bash
kubectl get sa -n kubernetes-dashboard
```

Check Cluster Role Binding

```bash
kubectl get clusterrolebinding admin-user
```

---

# Useful Commands

View Dashboard Namespace

```bash
kubectl get ns
```

Delete Dashboard

```bash
kubectl delete -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

Delete Admin User

```bash
kubectl delete -f dashboard-admin-user.yaml
```