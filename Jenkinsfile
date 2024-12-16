pipeline {
    agent any
    triggers {
        pollSCM('H/5 * * * *') // Poll GitHub for changes every 5 minutes
    }
    environment {
        DOCKER_IMAGE = 'gasiey/cw2-server:1.0'
    }
    stages {
        stage('Checkout') {
            steps {
                echo 'Cloning the repository...'
                git url: 'https://github.com/Gasiey/cw2-project.git', branch: 'main'
            }
        }
        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}")
                }
            }
        }
        stage('Verify Docker Image') {
            steps {
                echo 'Verifying Docker image...'
                sh 'docker images | grep cw2-server'
            }
        }
    }
    post {
        success {
            echo 'Docker build completed successfully.'
        }
        failure {
            echo 'Docker build failed.'
        }
    }
}

