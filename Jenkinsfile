pipeline {
    agent any

    environment {
        DOCKER_HUB_USERNAME = 'krishdockhub'
        DOCKER_IMAGE_NAME = 'krishdockhub/flask-docker-app'
        DOCKER_IMAGE_TAG = 'latest'
        DOCKER_CONTAINER_NAME = 'flask_container'
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
        stage("Build") {
            steps {
                sh """
                    docker build -t ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG} .
                """
            }
        }
        
        stage("Push to DockerHub") {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerhubpwd', variable: 'dockerhubpwd')]) {
                    sh 'docker login -u ${DOCKER_HUB_USERNAME} -p ${dockerhubpwd}'
                    }
                    sh 'docker push ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}'
                }
            }
        }

        stage("Deploy") {
            steps {
                script {
                    sh 'docker rm -f ${DOCKER_CONTAINER_NAME}'
                    sh """
                    docker run -p 5000:5000 --name ${DOCKER_CONTAINER_NAME} -d ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}
                    """
                    echo 'Docker container is running...'
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
