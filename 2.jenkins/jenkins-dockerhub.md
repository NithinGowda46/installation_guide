# Connect Jenkins with Docker Hub

## Step 1: Create Docker Hub Access Token

Login to Docker Hub.

Go to:

    Account Settings
    → Personal access tokens
    → Generate new token

Create an access token with permission to push images.

Copy the token.

> Save the token securely. You may not be able to view it again.

## Step 2: Open Jenkins Credentials

Open Jenkins:

    Manage Jenkins
    → Credentials
    → System
    → Global credentials
    → Add Credentials

## Step 3: Add Docker Hub Credential

Select:

    Kind: Username with password

Enter:

    Username: Your Docker Hub username

    Password: Your Docker Hub Access Token

    ID: dockerid

Then click:

    Create

## Step 4: Verify the Credential

The Jenkins credential should appear as:

    ID: dockerid

    Type: Username with password

## Step 5: Login to Docker Hub from Jenkins Pipeline

Use:

    withCredentials([
        usernamePassword(
            credentialsId: 'dockerid',
            usernameVariable: 'DOCKER_USER',
            passwordVariable: 'DOCKER_PASS'
        )
    ]) {
        sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
    }

## Step 6: Docker Login Flow

    Jenkins
       ↓
    Jenkins Credentials
       ↓
    dockerid
       ↓
    Docker Hub Username
       +
    Docker Hub Access Token
       ↓
    docker login
       ↓
    Docker Hub
       ↓
    Successfully Authenticated

## Step 7: Push Docker Image

After successful login, Jenkins can push the image:

    docker push nithingowda46/notes-app:${BUILD_NUMBER}-back

For example:

    docker push nithingowda46/notes-app:10-back

## Important

Do NOT put your Docker Hub password or access token directly in the Jenkinsfile.

Do NOT use:

    docker login -u username -p password

Use Jenkins Credentials Manager instead:

    credentialsId: 'dockerid'

Your Jenkinsfile should retrieve the credentials securely using:

    withCredentials(...)
