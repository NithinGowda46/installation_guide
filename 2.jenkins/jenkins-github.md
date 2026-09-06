# GitHub – Jenkins SSH Connection

SSH authentication is used for Jenkins to access and push changes to the GitHub CD repository.

## 1. Create SSH Directory for Jenkins

```bash
sudo mkdir -p /var/lib/jenkins/.ssh
sudo chown jenkins:jenkins /var/lib/jenkins/.ssh
sudo chmod 700 /var/lib/jenkins/.ssh
```

## 2. Generate SSH Key for Jenkins

```bash
sudo -u jenkins ssh-keygen -t ed25519 -C "jenkins-github"
```

Press `Enter` for the default file location and leave the passphrase empty.

The keys are created at:

```text
/var/lib/jenkins/.ssh/id_ed25519
/var/lib/jenkins/.ssh/id_ed25519.pub
```

## 3. Add Public Key to GitHub

Display the public key:

```bash
sudo -u jenkins cat /var/lib/jenkins/.ssh/id_ed25519.pub
```

Copy the complete output and add it to:

**GitHub → Settings → SSH and GPG keys → New SSH key**

Example title:

```text
Jenkins CD Server
```

## 4. Add GitHub Host Key

```bash
sudo -u jenkins sh -c 'ssh-keyscan github.com >> /var/lib/jenkins/.ssh/known_hosts'
```

Set the correct permissions:

```bash
sudo chown -R jenkins:jenkins /var/lib/jenkins/.ssh
sudo chmod 700 /var/lib/jenkins/.ssh
sudo chmod 644 /var/lib/jenkins/.ssh/known_hosts
```

## 5. Create Jenkins Credential

Go to:

**Jenkins → Manage Jenkins → Credentials → Global → Add Credentials**

Configure:

```text
Kind: SSH Username with private key
Username: git
ID: github-ssh
Private Key: Enter directly
```

Get the private key:

```bash
sudo cat /var/lib/jenkins/.ssh/id_ed25519
```

Copy the complete private key and paste it into Jenkins.

> Never share the private key or commit it to GitHub.

## 6. Use SSH URL in Jenkins Pipeline

For the CD repository:

```groovy
git branch: 'main',
    credentialsId: 'github-ssh',
    url: 'git@github.com:NithinGowda46/Project2-chatapp-CD.git'
```

## GitHub → Jenkins Flow

```text
Jenkins
   ↓
SSH Private Key
   ↓
GitHub SSH Authentication
   ↓
Project2-chatapp-CD
```
