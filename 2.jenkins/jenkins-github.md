# Connect GitHub with Jenkins Using SSH

## Step 1: Switch to the Jenkins User

    sudo su - jenkins

## Step 2: Create the SSH Directory

    mkdir -p ~/.ssh
    chmod 700 ~/.ssh

## Step 3: Generate SSH Key

    ssh-keygen -t ed25519 -C "jenkins-github"

Press Enter to use the default file location.

The keys will be created as:

    /var/lib/jenkins/.ssh/id_ed25519
    /var/lib/jenkins/.ssh/id_ed25519.pub

## Step 4: Display the Jenkins Public Key

    cat ~/.ssh/id_ed25519.pub

Copy the complete output.

## Step 5: Add the Public Key to GitHub

Go to:

    GitHub
    → Settings
    → SSH and GPG keys
    → New SSH key

Title:

    Jenkins-EC2

Key type:

    Authentication Key

Paste the public key from:

    cat ~/.ssh/id_ed25519.pub

Click:

    Add SSH key

## Step 6: Test GitHub SSH Authentication

While still using the Jenkins user:

    ssh -T git@github.com

If GitHub asks:

    Are you sure you want to continue connecting?

Enter:

    yes

## Step 7: Add Jenkins SSH Credential

Open Jenkins:

    Manage Jenkins
    → Credentials
    → System
    → Global credentials
    → Add Credentials

Select:

    Kind: SSH Username with private key

Set:

    Username: git
    ID: github-ssh

Select:

    Enter directly

Get the Jenkins private key:

    sudo cat /var/lib/jenkins/.ssh/id_ed25519

Copy the complete private key into Jenkins.

Click:

    Create

## Step 8: Test Application Repository

The Jenkins credential can access the application repository:

    git@github.com:NithinGowda46/Project1-notesapp-application.git

## Step 9: Test GitOps Repository

The same Jenkins credential can access the GitOps repository:

    git@github.com:NithinGowda46/Project1-notesapp-devops.git

The GitHub account must have access to both repositories.

## Step 10: Jenkinsfile GitHub Configuration

For the application repository:

    git branch: 'main',
        credentialsId: 'github-ssh',
        url: 'git@github.com:NithinGowda46/Project1-notesapp-application.git'

For the GitOps repository:

    git branch: 'main',
        credentialsId: 'github-ssh',
        url: 'git@github.com:NithinGowda46/Project1-notesapp-devops.git'

## Authentication Flow

    Jenkins Server
          ↓
    Jenkins User
          ↓
    SSH Private Key
          ↓
    Jenkins Credential
       github-ssh
          ↓
    GitHub
          ↓
    Application Repository
          +
    GitOps Repository

## Important

Do NOT add the Jenkins private key to GitHub.

GitHub receives only:

    id_ed25519.pub

Jenkins keeps:

    id_ed25519

Never commit the private key to your GitHub repository.
