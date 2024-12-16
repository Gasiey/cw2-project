pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'gasiey/cw2-server:1.0'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Cloning the repository...'
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }
        stage('Verify Docker Image') {
            steps {
                echo 'Verifying Docker image...'
                sh "docker images | grep cw2-server"
            }
        }
        stage('Run and Verify Container') {
            steps {
                echo 'Running Docker container...'
                sh "docker run -d -p 9090:8080 --name my-server ${DOCKER_IMAGE}"
                echo 'Verifying that the container is running...'
                sh "docker ps | grep my-server"
                echo 'Testing the application endpoint...'
                sh "curl http://localhost:9090 || exit 1"
            }
        }
    }
    post {
        success {
            echo 'Docker build and verification successful!'
        }
        failure {
            echo 'Pipeline failed. Check the logs.'
        }
    }
}
