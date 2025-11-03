pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('cybr-3120')
        IMAGE_NAME = "litattj/nodejs-chatapp"
        APP_SERVER = "172.236.110.58"
    }

    stages {
        stage('Clone GitHub') {
            steps {
                git branch: 'main', url: 'https://github.com/atticusjlith/NodejsChatApp.git'
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                sshagent(['app-server-ssh']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no root@$APP_SERVER '
                        cd /root && \
                        git clone -b main https://github.com/atticusjlith/NodejsChatApp.git || \
                        cd NodejsChatApp && git pull && \
                        docker build -t $IMAGE_NAME:latest . && \
                        echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin && \
                        docker push $IMAGE_NAME:latest
                    '
                    """
                }
            }
        }

        stage('Deploy to App Server') {
            steps {
                sshagent(['app-server-ssh']) {
                    sh """
                    ssh -o StrictHostKeyChecking=no root@$APP_SERVER '
                        docker stop chatapp || true && \
                        docker rm chatapp || true && \
                        docker run -d -p 3000:3000 --name chatapp $IMAGE_NAME:latest
                    '
                    """
                }
            }
        }
    }
}
