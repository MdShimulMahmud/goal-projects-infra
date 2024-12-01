pipeline {
    agent any
    environment {
        GH_TOKEN = credentials('github-token')  // GitHub token
    }
    parameters {
        string(name: 'FRONTEND_IMAGE_TAG', defaultValue: '', description: 'Frontend Docker image tag')
        string(name: 'BACKEND_IMAGE_TAG', defaultValue: '', description: 'Backend Docker image tag')
    }
    stages {
        stage('Clone Repository') {
            steps {
                script {
                    sh """
                        git clone https://github.com/MdShimulMahmud/goal-projects-infra.git
                        cd goal-projects-infra
                        git config user.name "jenkins-bot"
                        git config user.email "jenkins@localhost"
                    """
                }
            }
        }
        stage('Update Image Tags') {
            steps {
                script {
                    sh """
                        cd goal-projects-infra

                        # Update image tags in k8s/deployment.yaml
                        sed -i "s|shimulmahmud/frontend:.*|${params.FRONTEND_IMAGE_TAG}|" k8s/deployment.yaml
                        sed -i "s|shimulmahmud/backend:.*|${params.BACKEND_IMAGE_TAG}|" k8s/deployment.yaml

                        # Commit and push changes
                        git add k8s/deployment.yaml
                        git commit -m "Update image tags to ${params.FRONTEND_IMAGE_TAG} and ${params.BACKEND_IMAGE_TAG}"
                        git push https://${GH_TOKEN}@github.com/MdShimulMahmud/goal-projects-infra.git
                    """
                }
            }
        }
    }
    post {
        always {
            echo "Update manifest pipeline completed."
        }
        failure {
            echo "Update manifest pipeline failed."
        }
    }
}
