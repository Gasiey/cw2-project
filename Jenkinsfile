pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                // Pull the repository's content
                git branch: 'main', url: 'https://github.com/Gasiey/cw2-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build Docker image with your tag
                sh 'docker build -t gasiey/cw2-server:1.0 .'
            }
        }

        stage('Push Docker Image') {
            steps {
                // Login to Docker Hub using Jenkins credentials
                withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push gasiey/cw2-server:1.0
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                // Apply Kubernetes YAML for deployment
                sh 'kubectl apply -f deployment.yaml'
            }
        }
    }
}
