pipeline {
    agent any

    environment {
        NODE_ENV = 'production'
        APP_DIR = '/var/jenkins_home/workspace/ChatApp-Pipeline'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/atticusjlith/NodejsChatApp.git', branch: 'main'
            }
        }

        stage('Install Dependencies') {
            steps {
                dir("${APP_DIR}") {
                    sh 'npm install'
                }
            }
        }

        stage('Deploy App') {
            steps {
                dir("${APP_DIR}") {
                    // Stop app if running (using pm2, install if necessary)
                    sh 'pm2 stop nodejs-chatapp || true'
                    // Start app
                    sh 'pm2 start index.js --name nodejs-chatapp'
                    // Save pm2 process list to auto-start on reboot
                    sh 'pm2 save'
                }
            }
        }

        stage('Verify') {
            steps {
                sh 'pm2 status'
            }
        }
    }

    post {
        failure {
            echo 'Deployment failed. Check console logs.'
        }
        success {
            echo 'Deployment successful!'
        }
    }
}
