pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "geetha8500/hello-world-python"
        DOCKER_CREDENTIALS_ID = "41380629-0631-44da-ba4d-add2b73c5926"
        GITHUB_REPO = "https://github.com/your-username/k8s-manifests.git"
        GIT_BRANCH = "main"
        ARGOCD_SERVER = "localhost:8080"
        ARGOCD_APP_NAME = "hello-world-python"
        ARGOCD_CREDENTIALS_ID = "argocd-auth"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: "${GIT_BRANCH}", url: "${GITHUB_REPO}"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE}:latest ."
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: DOCKER_CREDENTIALS_ID, url: ""]) {
                    sh "docker push ${DOCKER_IMAGE}:latest"
                }
            }
        }

        stage('Update Kubernetes Manifests') {
            steps {
                script {
                    sh '''
                    sed -i 's|image: .*|image: '${DOCKER_IMAGE}':latest|' deployment.yaml
                    git config user.email "jenkins@example.com"
                    git config user.name "Jenkins"
                    git add deployment.yaml
                    git commit -m "Update deployment image to latest"
                    git push origin ${GIT_BRANCH}
                    '''
                }
            }
        }

        stage('Trigger ArgoCD Deployment') {
            steps {
                script {
                    withCredentials([string(credentialsId: ARGOCD_CREDENTIALS_ID, variable: 'ARGOCD_PASSWORD')]) {
                        sh '''
                        argocd login ${ARGOCD_SERVER} --username admin --password ${ARGOCD_PASSWORD} --insecure
                        argocd app sync ${ARGOCD_APP_NAME}
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Deployment completed successfully!"
        }
        failure {
            echo "Deployment failed."
        }
    }
}
