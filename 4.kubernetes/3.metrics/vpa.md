# Kubernetes VPA Installation

## Step 1: Clone the Autoscaler Repository

    git clone https://github.com/kubernetes/autoscaler.git

## Step 2: Go to VPA Directory

    cd autoscaler/vertical-pod-autoscaler

## Step 3: Install VPA

    ./hack/vpa-up.sh

## Step 4: Verify VPA

    kubectl get pods -n kube-system | grep vpa

## Step 5: Verify VPA CRDs

    kubectl get crd | grep verticalpodautoscaler

## Step 6: Check VPA Resources

    kubectl get vpa -A
