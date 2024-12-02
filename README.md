# Kubernetes Deployment Pipeline

This Jenkins pipeline automates the process of updating Kubernetes manifests with new Docker image tags and pushing the changes to a GitHub repository.

This pipeline is triggered by [this repository's Jenkinsfile](https://github.com/MdShimulMahmud/goal-projects.git)

## Overview

The pipeline performs the following tasks:
1. Accepts a Docker image tag as a parameter.
2. Updates the `deployment.yaml` file with the provided image tags for the frontend and backend.
3. Commits the changes and pushes them to the GitHub repository.

## Pipeline Configuration

```groovy
pipeline {
    agent any
    parameters {
        string(name: 'IMAGE_TAG', defaultValue: '', description: 'Docker image tag for frontend and backend')
    }
    environment {
        GH_TOKEN = credentials('github-token')  
    }
    stages {
        stage('Update and Push Manifests') {
            steps {
                script {
                    def frontendTag = "shimulmahmud/frontend:${params.IMAGE_TAG}"
                    def backendTag = "shimulmahmud/backend:${params.IMAGE_TAG}"
                    sh """
                        # Checkout the repository
                        git checkout master

                        # Update image tags in deployment.yaml
                        sed -i 's|image: .*frontend:.*|image: ${frontendTag}|' k8s/deployment.yaml
                        sed -i 's|image: .*backend:.*|image: ${backendTag}|' k8s/deployment.yaml

                        # Configure Git user details and commit the changes
                        git config user.name "jenkins-bot"
                        git config user.email "jenkins@localhost"
                        git add k8s/deployment.yaml
                        git commit -m "Update image tags to frontend=${frontendTag}, backend=${backendTag}"

                        # Pull latest changes and push the commit
                        git pull --rebase origin master
                        git push https://\$GH_TOKEN@github.com/MdShimulMahmud/goal-projects-infra.git master
                    """
                }
            }
        }
    }
}

```

## How It Works

### Input Parameter

- **IMAGE_TAG**: A parameter for specifying the Docker image tag for both frontend and backend services.

### Steps in the Pipeline

1. **Check Out the Repository**:  
   The pipeline checks out the `master` branch of the repository.

2. **Update Image Tags**:  
   The `k8s/deployment.yaml` file is updated to include the specified `IMAGE_TAG`:
   - Replace the frontend image tag with `shimulmahmud/frontend:<IMAGE_TAG>`.
   - Replace the backend image tag with `shimulmahmud/backend:<IMAGE_TAG>`.

3. **Configure Git**:  
   Configure Git user details for committing changes.

4. **Push Changes**:  
   Push the updated manifests back to the `master` branch of the repository.

---

### Repository Structure

The repository should include:
- **Kubernetes Manifest File**: A file named `k8s/deployment.yaml`.
- **Image References**: The `deployment.yaml` file should include image definitions for:
  - Frontend service
  - Backend service

---