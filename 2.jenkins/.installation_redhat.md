# Jenkins Installation on RHEL / Rocky Linux / AlmaLinux / CentOS (Production - Without Docker)

This guide explains how to install Jenkins on RHEL-based Linux distributions.

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

## Step 2: Install Java

Check Java:

```bash
java -version
```

Install OpenJDK 21:

```bash
sudo dnf install java-21-openjdk -y
```

Verify:

```bash
java -version
```

---

## Step 3: Add Jenkins Repository

```bash
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
```

---

## Step 4: Import Jenkins GPG Key

```bash
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
```

---

## Step 5: Install Jenkins

```bash
sudo dnf install jenkins -y
```

---

## Step 6: Enable Jenkins

```bash
sudo systemctl enable jenkins
```

---

## Step 7: Start Jenkins

```bash
sudo systemctl start jenkins
```

---

## Step 8: Verify Status

```bash
sudo systemctl status jenkins
```

Expected:

```text
Active: active (running)
```

---

## Step 9: Open Firewall

```bash
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload
```

Verify:

```bash
sudo firewall-cmd --list-ports
```

---

## Step 10: Access Jenkins

```
http://<SERVER-IP>:8080
```

---

## Step 11: Get Initial Password

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

---

## Step 12: Complete Setup

- Install Suggested Plugins
- Create Admin User
- Save and Finish

---

# Useful Commands

Start

```bash
sudo systemctl start jenkins
```

Stop

```bash
sudo systemctl stop jenkins
```

Restart

```bash
sudo systemctl restart jenkins
```

Status

```bash
sudo systemctl status jenkins
```

Logs

```bash
sudo journalctl -u jenkins -f
```

---

# Important Locations

| Item | Location |
|------|----------|
| Jenkins Home | `/var/lib/jenkins` |
| Jobs | `/var/lib/jenkins/jobs` |
| Plugins | `/var/lib/jenkins/plugins` |
| Workspace | `/var/lib/jenkins/workspace` |
| Initial Password | `/var/lib/jenkins/secrets/initialAdminPassword` |

---

# Firewall Commands

Open Port

```bash
sudo firewall-cmd --permanent --add-port=8080/tcp
```

Reload

```bash
sudo firewall-cmd --reload
```

Verify

```bash
sudo firewall-cmd --list-ports
```

---

# Verify Installation

```bash
java -version
sudo systemctl status jenkins
sudo ss -tulnp | grep 8080
```