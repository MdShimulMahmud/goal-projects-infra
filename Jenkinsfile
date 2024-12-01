pipeline {
    agent any
    parameters {
        string(name: 'IMAGE_TAG', defaultValue: '', description: 'Docker image tag for frontend and backend')
    }
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], 
                          userRemoteConfigs: [[url: 'https://github.com/MdShimulMahmud/goal-projects-infra.git',
                                               credentialsId: 'github-credentials']]])
            }
        }
        stage('Ensure on Master Branch') {
            steps {
                script {
                    // Make sure we are on the master branch
                    sh """
                        git checkout master
                        git pull origin master
                    """
                }
            }
        }
        stage('Update Manifests') {
            steps {
                script {
                    // Use the IMAGE_TAG parameter to update both frontend and backend tags
                    def frontendTag = "shimulmahmud/frontend:${params.IMAGE_TAG}"
                    def backendTag = "shimulmahmud/backend:${params.IMAGE_TAG}"
                    
                    sh """
                        sed -i 's|image: .*frontend:.*|image: ${frontendTag}|' k8s/deployment.yaml
                        sed -i 's|image: .*backend:.*|image: ${backendTag}|' k8s/deployment.yaml
                        git config user.name "jenkins-bot"
                        git config user.email "jenkins@localhost"
                        git add k8s/deployment.yaml
                        git commit -m "Update image tags to frontend=${frontendTag}, backend=${backendTag}"
                    """
                }
            }
        }
        stage('Push Changes') {
            steps {
                script {
                    // Push the changes to the master branch
                    sh """
                        git push origin master
                    """
                }
            }
        }
    }
}
