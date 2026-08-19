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

### Ansible Installation Path

Check:

```bash
which ansible
```

Typical path:

```text
/opt/homebrew/bin/ansible
```

For Intel-based Macs:

```text
/usr/local/bin/ansible
```

### Ansible Configuration Path

Default configuration:

```text
/etc/ansible/ansible.cfg
```

User-level configuration:

```text
~/.ansible.cfg
```

Recommended project structure:

```text
~/ansible/
├── ansible.cfg
├── hosts
├── playbooks/
└── roles/
```

---

# 2. Red Hat Enterprise Linux (RHEL)

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

### Ansible Installation Path

Check:

```bash
which ansible
```

Typical path:

```text
/usr/bin/ansible
```

Check Ansible files:

```bash
rpm -ql ansible-core
```

### Ansible Configuration Path

Default configuration:

```text
/etc/ansible/ansible.cfg
```

Default inventory:

```text
/etc/ansible/hosts
```

User-level configuration:

```text
~/.ansible.cfg
```

Recommended project structure:

```text
/root/ansible/
├── ansible.cfg
├── hosts
├── playbooks/
└── roles/
```

---

# 3. Ubuntu

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

### Ansible Installation Path

Check:

```bash
which ansible
```

Typical path:

```text
/usr/bin/ansible
```

Check installed files:

```bash
dpkg -L ansible
```

### Ansible Configuration Path

Default configuration:

```text
/etc/ansible/ansible.cfg
```

Default inventory:

```text
/etc/ansible/hosts
```

User-level configuration:

```text
~/.ansible.cfg
```

Recommended project structure:

```text
~/ansible/
├── ansible.cfg
├── hosts
├── playbooks/
└── roles/
```