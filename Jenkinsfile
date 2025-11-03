pipeline {
    agent any

    environment {
        APP_NAME = "NodejsChatApp"
        DOCKER_IMAGE = "nodejs-chatapp:latest"
        APP_PATH = "/home/app"
        APP_SERVER = "172.236.110.58"
        SSH_CREDENTIAL_ID = "app-server-ssh"
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
                    // Build Docker image from the Dockerfile in the repo
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "Skipping Docker push since this is local testing"
                // If you have a DockerHub account, you could add login/push here
                // sh "docker login -u <user> -p <password>"
                // sh "docker tag ${DOCKER_IMAGE} <dockerhub_user>/${DOCKER_IMAGE}"
                // sh "docker push <dockerhub_user>/${DOCKER_IMAGE}"
            }
        }

        stage('Deploy to App Server') {
            steps {
                sshagent([SSH_CREDENTIAL_ID]) {
                    // Ensure the app path exists
                    sh "ssh -o StrictHostKeyChecking=no root@${APP_SERVER} 'mkdir -p ${APP_PATH}'"

                    // Copy application files to the app server
                    sh "scp -o StrictHostKeyChecking=no -r * root@${APP_SERVER}:${APP_PATH}/"

                    // Optionally, restart the app (depends on your setup)
                    sh """
                    ssh -o StrictHostKeyChecking=no root@${APP_SERVER} '
                        cd ${APP_PATH} &&
                        # Stop any existing node process (optional)
                        pkill node || true &&
                        # Install dependencies and start app
                        npm install &&
                        nohup node app.js > app.log 2>&1 &
                    '
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs.'
        }
    }
}
