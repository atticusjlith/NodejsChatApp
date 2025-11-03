pipeline {
    agent any

    environment {
        APP_NAME = "nodejs-chat-app"
        IMAGE_NAME = "nodejs-chat-app-image"
        CONTAINER_PORT = "3000"
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
                    sh """
                    if [ \$(docker ps -q -f name=${APP_NAME}) ]; then
                        docker stop ${APP_NAME}
                        docker rm ${APP_NAME}
                    fi
                    """
                }
            }
        }

        stage('Run Container') {
            steps {
                script {
                    sh "docker run -d --name ${APP_NAME} -p ${CONTAINER_PORT}:${CONTAINER_PORT} ${IMAGE_NAME}"
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    sh "docker ps | grep ${APP_NAME}"
                }
            }
        }
    }

    post {
        success {
            echo "Deployment succeeded!"
        }
        failure {
            echo "Deployment failed. Check logs."
        }
    }
}
