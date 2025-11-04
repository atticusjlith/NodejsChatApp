pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/atticusjlith/NodejsChatApp.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t litattj/nodejs-chatapp:latest .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh 'docker push litattj/nodejs-chatapp:latest'
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                docker rm -f nodejs-chat || true
                docker pull litattj/nodejs-chatapp:latest
                docker run -d -p 8081:3000 --name nodejs-chat litattj/nodejs-chatapp:latest
                '''
            }
        }
    }
}
