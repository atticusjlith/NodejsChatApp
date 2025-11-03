pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('cybr-3120')
        IMAGE_NAME = "litattj/nodejs-chatapp"
    }

    stages {
        stage('Clone GitHub') {
            steps {
                git 'https://github.com/atticusjlith/NodejsChatApp.git'
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                script {
                    sh 'docker build -t $IMAGE_NAME:latest .'
                    sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                    sh 'docker push $IMAGE_NAME:latest'
                }
            }
        }

        stage('Deploy to App Server') {
            steps {
                sshagent(['app-server-ssh']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no root@172.236.110.58 "
                        docker pull $IMAGE_NAME:latest &&
                        docker stop chatapp || true &&
                        docker rm chatapp || true &&
                        docker run -d -p 3000:3000 --name chatapp $IMAGE_NAME:latest
                    "
                    '''
                }
            }
        }
    }
}
