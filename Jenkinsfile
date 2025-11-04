pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/atticusjlith/NodejsChatApp.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t nodejs-chat-app-image .'
            }
        }
        stage('Stop Existing Container') {
            steps {
                sh 'docker stop nodejs-chat-app || true'
                sh 'docker rm nodejs-chat-app || true'
            }
        }
        stage('Run Container') {
            steps {
                sh 'docker run -d -p 3000:3000 --name nodejs-chat-app nodejs-chat-app-image'
            }
        }
        stage('Verify Deployment') {
            steps {
                sh 'curl -f http://localhost:3000 || echo "App not responding yet"'
            }
        }
    }
}
