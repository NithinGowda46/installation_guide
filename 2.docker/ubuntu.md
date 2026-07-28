# Docker Installation on Ubuntu (Production)

This guide explains how to install Docker Engine on Ubuntu using the official Docker repository.

## Prerequisites

- Ubuntu 22.04 / 24.04
- User with sudo privileges
- Internet connection

---

## Step 1: Update the System

```bash
sudo apt update
sudo apt upgrade -y
```

---

## Step 2: Remove Old Docker Versions

```bash
sudo apt remove docker docker-engine docker.io containerd runc -y
```

---

## Step 3: Install Required Packages

```bash
sudo apt install ca-certificates curl gnupg lsb-release -y
```

---

## Step 4: Create Docker GPG Key Directory

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

---

## Step 5: Add Docker GPG Key

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

```bash
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

---

## Step 6: Add Docker Repository

```bash
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

---

## Step 7: Update Package Index

```bash
sudo apt update
```

---

## Step 8: Install Docker

```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

---

## Step 9: Enable Docker

```bash
sudo systemctl enable docker
```

---

## Step 10: Start Docker

```bash
sudo systemctl start docker
```

---

## Step 11: Verify Installation

```bash
sudo systemctl status docker
```

```bash
docker --version
```

---

## Step 12: Test Docker

```bash
sudo docker run hello-world
```