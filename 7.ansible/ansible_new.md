# Ansible Installation Guide

## 1. macOS

### Install Homebrew

If Homebrew is not installed:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### Install Ansible

```bash
brew install ansible
```

### Verify Installation

```bash
ansible --version
```

---

## 2. Red Hat Enterprise Linux (RHEL)

### Update Packages

```bash
sudo dnf update -y
```

### Install Ansible

```bash
sudo dnf install ansible-core -y
```

### Verify Installation

```bash
ansible --version
```

---

## 3. Ubuntu

### Update Packages

```bash
sudo apt update
```

### Install Ansible

```bash
sudo apt install ansible -y
```

### Verify Installation

```bash
ansible --version
```

---

## 4. Check Ansible Installation

Run on any of the systems:

```bash
ansible --version
```

Example output:

```text
ansible [core ...]
  python version = ...
  jinja version = ...
  libyaml = True
```

---

## 5. Check Ansible Playbook

```bash
ansible-playbook --version
```

Example:

```text
ansible-playbook [core ...]
```

---

## 6. Basic Ansible Directory Structure

```text
ansible/
├── ansible.cfg
├── hosts
├── playbooks/
│   └── app_install.yml
└── roles/
```

Create the directories:

```bash
mkdir -p ~/ansible/playbooks
mkdir -p ~/ansible/roles
cd ~/ansible
```

---

## 7. Test Ansible

Check the installed version:

```bash
ansible --version
```

Check the playbook command:

```bash
ansible-playbook --version
```

Ansible is now installed and ready to use.