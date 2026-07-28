# Docker Installation on RHEL / Rocky Linux / AlmaLinux / CentOS (Production)

This guide explains how to install Docker Engine on RHEL-based Linux distributions.

## Prerequisites

- RHEL 9 / Rocky Linux / AlmaLinux / CentOS Stream
- User with sudo privileges
- Internet connection

---

## Step 1: Update the System

```bash
sudo dnf update -y
```

---

## Step 2: Remove Old Docker Versions

```bash
sudo dnf remove docker \
docker-client \
docker-client-latest \
docker-common \
docker-latest \
docker-latest-logrotate \
docker-logrotate \
docker-engine \
podman \
runc -y
```

---

## Step 3: Install Required Packages

```bash
sudo dnf install -y dnf-plugins-core
```

---

## Step 4: Add Docker Repository

```bash
sudo dnf config-manager --add-repo https://download.docker.com/linux/rhel/docker-ce.repo
```

> For Rocky Linux or AlmaLinux, you can use the CentOS repository:

```bash
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

---

## Step 5: Install Docker

```bash
sudo dnf install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

---

## Step 6: Enable Docker

```bash
sudo systemctl enable docker
```

---

## Step 7: Start Docker

```bash
sudo systemctl start docker
```

---

## Step 8: Verify Installation

```bash
sudo systemctl status docker
```

```bash
docker --version
```

---

## Step 9: Test Docker

```bash
sudo docker run hello-world
```