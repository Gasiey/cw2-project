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
	            // Remove any existing container with the same name
	            sh "docker rm -f ${CONTAINER_NAME} || true"

	            // Run the container
	            sh "docker run -d -p 9090:8080 --name ${CONTAINER_NAME} ${DOCKER_IMAGE}"

	            echo 'Verifying that the container is running...'
	            sh "docker ps | grep ${CONTAINER_NAME}"

	            echo 'Waiting for the container to start...'
	            sh "sleep 10" // Add a 10-second delay

	            echo 'Testing the application endpoint...'
	            sh "curl --fail http://localhost:9090 || exit 1"
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

	stage('Deploy to Kubernetes') {
            steps {
                sshagent(['jenkins-ssh']) {
                    echo 'Transferring Kubernetes configuration...'
                    sh 'scp /home/ubuntu/app.yaml ubuntu@54.163.198.33:/tmp/app.yaml'
                    echo 'Deploying application to Kubernetes...'
                    sh 'ssh ubuntu@54.163.198.33 kubectl apply -f /tmp/app.yaml'
                }
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
