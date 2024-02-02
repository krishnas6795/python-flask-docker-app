pipeline {
    agent any

    environment {
        // Define your Docker Hub credentials
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials-id')
        // Define your Docker image name and tag
        DOCKER_IMAGE_NAME = 'krishdockhub/flask-docker-app'
        DOCKER_IMAGE_TAG = 'latest'
    }
    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from the GitHub repository
                script {
                    checkout scm
                }
            }
        }
        
        stage('Build and Push Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    dockerImage = docker.build("${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}")

                    // Log in to Docker Hub
                    docker.withRegistry('https://registry.hub.docker.com', DOCKER_HUB_CREDENTIALS) {
                        // Push the Docker image to Docker Hub
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Run the Docker container
                    docker.image("${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}").withRun('-p 5000:5000') {
                        // Add any additional steps needed after running the container
                        echo 'Docker container is running...'
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Build and deployment successful!'
        }

        failure {
            echo 'Build or deployment failed!'
        }
    }
}