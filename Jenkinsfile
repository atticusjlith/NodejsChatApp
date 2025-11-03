pipeline {
    agent any

    environment {
        APP_SERVER = "172.236.110.58"
        APP_PATH = "/home/nodejs-chatapp"
        SSH_CRED = "app-server-ssh" // your Jenkins SSH credential ID
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/atticusjlith/NodejsChatApp.git', branch: 'main'
            }
        }

        stage('Deploy to App Server') {
            steps {
                sshCommand remote: [
                    user: 'root', 
                    host: env.APP_SERVER, 
                    identity: env.SSH_CRED
                ] , command: """
                    mkdir -p ${APP_PATH}
                    cd ${APP_PATH}
                    git pull || git clone https://github.com/atticusjlith/NodejsChatApp.git .
                    npm install
                    pm2 stop nodejs-chatapp || true
                    pm2 start index.js --name nodejs-chatapp
                """
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed. Check logs.'
        }
    }
}
