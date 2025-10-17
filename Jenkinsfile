pipeline {
    agent any

    environment {
        DEPLOY_SERVER = 'ubuntu@13.40.154.215'
        DEPLOY_PATH = '/var/www/country-access-app'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/nadil1995/GeoApp.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build React App') {
            steps {
                sh 'npm run build'
            }
        }

     stage('Deploy to Server') {
            steps {
                sshagent(credentials: [env.SSH_CRED]) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@${DEPLOY_SERVER} "mkdir -p ${DEPLOY_DIR}"
                    scp -o StrictHostKeyChecking=no -r dist/* ubuntu@${DEPLOY_SERVER}:${DEPLOY_DIR}/
                    ssh -o StrictHostKeyChecking=no ubuntu@${DEPLOY_SERVER} "sudo systemctl restart nginx || echo 'Nginx not configured yet'"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'React app built and deployed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
