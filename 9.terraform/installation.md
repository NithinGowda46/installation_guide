# Terraform Installation on Linux

This guide installs the Terraform CLI from HashiCorp's official package repository on Ubuntu/Debian, RHEL/CentOS, or Amazon Linux.

## Prerequisites

- Ubuntu/Debian, RHEL/CentOS, or Amazon Linux
- A user with `sudo` privileges
- Internet connection

---

## Ubuntu / Debian

### Step 1: Update the system and install prerequisites

    sudo apt-get update
    sudo apt-get install -y wget gpg

### Step 2: Add HashiCorp's signing key

    wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null

### Step 3: Add the official HashiCorp repository

    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

### Step 4: Install Terraform

    sudo apt-get update
    sudo apt-get install -y terraform

---

## RHEL / CentOS

### Step 1: Install repository management tools

    sudo yum install -y yum-utils

### Step 2: Add the official HashiCorp repository

    sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo

### Step 3: Install Terraform

    sudo yum install -y terraform

---

## Amazon Linux

### Step 1: Install repository management tools

    sudo yum install -y yum-utils shadow-utils

### Step 2: Add the official HashiCorp repository

    sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo

### Step 3: Install Terraform

    sudo yum install -y terraform

---

## Verify the Installation

Check that Terraform is available and print its installed version:

    terraform -version

Terraform is installed and ready to use. In a directory containing Terraform configuration files, initialize the working directory with:

    terraform init

For other operating systems or installation methods, see the [official Terraform installation guide](https://developer.hashicorp.com/terraform/install).
