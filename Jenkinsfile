pipeline {
    agent any
    
    environment {
        APP_SERVER_IP = '98.130.122.234'
        REMOTE_USER   = 'ubuntu'
        REMOTE_PATH   = '/home/ubuntu/node-app'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', 
                    url: 'https://github.com/Vijay-6008/simple-node-app.git'
            }
        }

        stage('Deploy to App Server') {
            steps {
                sshagent(credentials: ['app-ssh-key']) {
                    sh '''

                       
                        set -e  # Stop on any error

                        echo "=== Creating remote directory ==="
                        ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null ${REMOTE_USER}@${APP_SERVER_IP} "mkdir -p ${REMOTE_PATH}"

                        echo "=== Copying files ==="
                        scp -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -r . ${REMOTE_USER}@${APP_SERVER_IP}:${REMOTE_PATH}/

                        echo "=== Installing dependencies and restarting app ==="
                        ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null ${REMOTE_USER}@${APP_SERVER_IP} "
                            cd ${REMOTE_PATH} &&
                            npm install &&
                            pm2 restart simple-app || pm2 start app.js --name simple-app
                        "

                        echo "=== Deployment completed successfully! ==="
                    '''
                }
            }
        }
    }

    post {
        failure {
            echo "Deployment failed - Check logs above"
        }
        success {
            echo "Deployment successful!"
        }
    }
}
