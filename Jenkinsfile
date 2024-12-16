pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'gasiey/cw2-server:1.0'
        CONTAINER_NAME = 'my-server'
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
                script {
                    sh """
                    # Remove any existing container with the same name
                    docker rm -f ${CONTAINER_NAME} || true
                    
                    # Run the container
                    docker run -d -p 9090:8080 --name ${CONTAINER_NAME} ${DOCKER_IMAGE}
                    
                    echo 'Verifying that the container is running...'
                    docker ps | grep ${CONTAINER_NAME}
                    
                    echo 'Testing the application endpoint...'
                    curl --fail http://localhost:9090 || exit 1
                    """
                }
            }
        }
	
	stage('Push to DockerHub') {
	    steps {
		echo 'Pushing Docker image to DockerHub...'
		sh "docker login -u gasiey -p 1Q2w3e4r5t"
	        sh "docker tag ${DOCKER_IMAGE} gasiey/cw2-server:1.0"
	        sh "docker push gasiey/cw2-server:1.0"
	    }
	}
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for errors.'
        }
    }
}
