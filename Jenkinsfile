pipeline {
    agent any

    environment {
        IMAGE_NAME = "nodejs-chat-app-image"
        CONTAINER_NAME = "nodejs-chat-app-container"
        APP_PORT = "3000"
        HOST_PORT = "8081"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/atticusjlith/NodejsChatApp.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME} ."
                }
            }
        }

        stage('Stop Existing Container') {
            steps {
                script {
                    // Stop and remove the container if it exists
                    sh """
                    if [ \$(docker ps -aq -f name=${CONTAINER_NAME}) ]; then
                        docker stop ${CONTAINER_NAME} || true
                        docker rm ${CONTAINER_NAME} || true
                    fi
                    """
                }
            }
        }

        stage('Run Container') {
            steps {
                script {
                    sh "docker run -d --name ${CONTAINER_NAME} -p ${HOST_PORT}:${APP_PORT} ${IMAGE_NAME}"
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                echo "Your app should now be accessible at http://<JENKINS_HOST>:${HOST_PORT}"
            }
        }
    }

    post {
        failure {
            echo "Deployment failed. Check logs above."
        }
    }
}
