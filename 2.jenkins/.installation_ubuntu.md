# Jenkins Installation on Ubuntu (Production - Without Docker)

This guide explains how to install Jenkins on Ubuntu 22.04/24.04 without Docker.

## Prerequisites

- Ubuntu 22.04 or later
- User with sudo privileges
- Internet connection

---

## Step 1: Update the System

```bash
sudo apt update
sudo apt upgrade -y
```

---

## Step 2: Install Java

Check Java version:

```bash
java -version
```

Install OpenJDK 21:

```bash
sudo apt install openjdk-21-jdk -y
```

Verify:

```bash
java -version
```

---

## Step 3: Install Required Packages

```bash
sudo apt install curl gnupg -y
```

---

## Step 4: Add Jenkins Repository Key

```bash
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
```

---

## Step 5: Add Jenkins Repository

```bash
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
```

---

## Step 6: Update Package List

```bash
sudo apt update
```

---

## Step 7: Install Jenkins

```bash
sudo apt install jenkins -y
```

---

## Step 8: Enable Jenkins

```bash
sudo systemctl enable jenkins
```

---

## Step 9: Start Jenkins

```bash
sudo systemctl start jenkins
```

---

## Step 10: Verify Status

```bash
sudo systemctl status jenkins
```

Expected:

```text
Active: active (running)
```

---

## Step 11: Configure Firewall

```bash
sudo ufw allow 8080
sudo ufw reload
```

---

## Step 12: Access Jenkins

```
http://<SERVER-IP>:8080
```

Example:

```
http://192.168.1.100:8080
```

---

## Step 13: Get Initial Password

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Paste this password into the Jenkins web interface.

---

## Step 14: Complete Setup

- Install Suggested Plugins
- Create Admin User
- Save and Finish

---

# Useful Commands

Start Jenkins

```bash
sudo systemctl start jenkins
```

Stop Jenkins

```bash
sudo systemctl stop jenkins
```

Restart Jenkins

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

# Verify Installation

```bash
java -version
sudo systemctl status jenkins
sudo ss -tulnp | grep 8080
```