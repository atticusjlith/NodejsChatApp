pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('cybr-3120')
        IMAGE_NAME = "litattj/nodejs-chatapp"
        SSH_KEY_PATH = "/var/jenkins_home/.ssh/app-server-key"  // path to your private key
        APP_SERVER = "root@172.236.110.58"
    }

    stages {
        stage('Clone GitHub') {
            steps {
                git 'https://github.com/atticusjlith/NodejsChatApp.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push $IMAGE_NAME:latest'
            }
        }

        stage('Deploy to App Server') {
            steps {
                sh '''
                ssh -i $SSH_KEY_PATH -o StrictHostKeyChecking=no $APP_SERVER "
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
