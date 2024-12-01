pipeline {
    agent any
    parameters {
        string(name: 'IMAGE_TAG', defaultValue: '', description: 'Docker image tag for frontend and backend')
    }
    environment {
        GH_TOKEN = credentials('github-token')  // Secure GitHub token reference
    }
    stages {
        stage('Update and Push Manifests') {
            steps {
                script {
                    def frontendTag = "shimulmahmud/frontend:${params.IMAGE_TAG}"
                    def backendTag = "shimulmahmud/backend:${params.IMAGE_TAG}"

                    // Checkout the repository, update the deployment.yaml, and push the changes
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
