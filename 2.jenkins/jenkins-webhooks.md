# GitHub Webhook + Jenkins Automatic Build Setup

## Step 1: Make Jenkins Accessible

Jenkins must be reachable from GitHub.

For an EC2 Jenkins server:

    http://YOUR_EC2_PUBLIC_IP:8080

Make sure port `8080` is accessible from the internet for the webhook.

---

## Step 2: Install GitHub Integration Plugin

Go to:

    Jenkins
    → Manage Jenkins
    → Plugins
    → Available plugins

Search:

    GitHub Integration

Install the plugin.

---

## Step 3: Configure Jenkins Job

Open:

    Jenkins
    → Your Pipeline Job
    → Configure

Go to:

    Build Triggers

Enable:

    GitHub hook trigger for GITScm polling

Save the configuration.

---

## Step 4: Configure GitHub Repository in Jenkins

Use the normal HTTPS repository URL:

    https://github.com/NithinGowda46/Project1-notesapp-application.git

Example:

    git branch: 'main',
        url: 'https://github.com/NithinGowda46/Project1-notesapp-application.git'

---

## Step 5: Create GitHub Webhook

Go to:

    GitHub
    → Project1-notesapp-application
    → Settings
    → Webhooks
    → Add webhook

---

## Step 6: Configure Payload URL

Enter:

    http://YOUR_EC2_PUBLIC_IP:8080/github-webhook/

---

## Step 7: Configure Webhook

Set:

    Content type:
    application/json

Select:

    Just the push event

Enable:

    Active

Click:

    Add webhook

---

## Step 8: Test the Webhook

Make a change in the application repository:

    git add .
    git commit -m "Test Jenkins webhook"
    git push origin main

---

## Step 9: Check GitHub Webhook

Go to:

    GitHub
    → Repository
    → Settings
    → Webhooks
    → Your webhook
    → Recent Deliveries

Check that the webhook was delivered successfully.

---

## Step 10: Check Jenkins

Open your Jenkins job.

A new build should automatically start after the GitHub push.

No need to manually click:

    Build Now

---

## Final Flow

    Developer
        ↓
    git push
        ↓
    GitHub
        ↓
    GitHub Webhook
        ↓
    Jenkins
        ↓
    Jenkins Pipeline
        ↓
    Docker Build
        ↓
    Docker Hub
        ↓
    GitOps Repository
        ↓
    Argo CD
        ↓
    Kubernetes
