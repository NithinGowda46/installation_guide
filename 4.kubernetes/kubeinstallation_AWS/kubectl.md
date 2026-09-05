# 🔴 kubectl Installation

## Amazon Linux 2023

### Download kubectl

```bash
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.33.0/2025-05-01/bin/linux/amd64/kubectl
```

### Make kubectl Executable

```bash
chmod +x kubectl
```

### Move kubectl to PATH

```bash
sudo mv kubectl /usr/local/bin/
```

### Verify Installation

```bash
kubectl version --client
```