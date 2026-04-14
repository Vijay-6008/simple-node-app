pipeline {
    agent any
    environment {
        APP_SERVER_IP   = '<98.130.122.234>'   // Change to your App instance public IP
        SSH_CREDENTIAL  = 'app-ssh-key'        // We will create this in next step
        REMOTE_USER     = 'ubuntu'
        REMOTE_PATH     = '/home/ubuntu/node-app'
    }
    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/Vijay-6008/simple-node-app.git'
            }
        }
        stage('Deploy to App Server') {
            steps {
                sshagent(['ubuntu']) { //([env.SSH_CREDENTIAL])
                    sh '''
                        ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${APP_SERVER_IP} "mkdir -p ${REMOTE_PATH}"
                        scp -o StrictHostKeyChecking=no -r * ${REMOTE_USER}@${APP_SERVER_IP}:${REMOTE_PATH}/
                        ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${APP_SERVER_IP} "
                            cd ${REMOTE_PATH} &&
                            npm install &&
                            pm2 start app.js --name simple-app || pm2 restart simple-app
                        "
                    '''
                }
            }
        }
    }
    post {
        success { echo '✅ Deployment Successful!' }
        failure { echo '❌ Deployment Failed - Check logs' }
    }
}
