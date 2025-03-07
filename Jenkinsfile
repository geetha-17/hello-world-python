pipeline {
    agent any

    environment {
        IMAGE_NAME = 'geetha8500/hello-world-python'
    }

    stages {
        stage('Clone Repository') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github-credentials', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_PASSWORD')]) {
                    sh 'git clone https://$GIT_USER:$GIT_PASSWORD@github.com/geetha8500/hello-world-python.git'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'geetha8500', passwordVariable: 'geetha8500')]) {
                    sh 'echo $geetha8500 | docker login -u $geetha8500 --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push $IMAGE_NAME'
            }
        }
    }

    post {
        success {
            echo 'Build and Push Successful!'
        }
        failure {
            echo 'Build Failed!'
        }
    }
}
