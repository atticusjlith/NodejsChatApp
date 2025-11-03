pipeline {
    agent any

    environment {
        APP_SERVER = "172.236.110.58"
        APP_PATH = "/home/nodejs-chatapp"
    }

    stages {

        stage('Checkout SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/atticusjlith/NodejsChatApp.git'
            }
        }

        stage('Deploy to App Server') {
            steps {
                sshagent(['app-server-ssh']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no root@${APP_SERVER} '
                            mkdir -p ${APP_PATH} &&
                            cd ${APP_PATH} &&
                            git pull || git clone https://github.com/atticusjlith/NodejsChatApp.git . &&
                            npm install &&
                            pm2 stop nodejs-chatapp || true &&
                            pm2 start index.js --name nodejs-chatapp
                        '
                    """
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
