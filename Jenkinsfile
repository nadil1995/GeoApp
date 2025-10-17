pipeline {
    agent any

    environment {
        DEPLOY_SERVER = "13.40.154.215"
        DEPLOY_DIR = "/var/www/country-access-app"
        SSH_CRED = "geo-ssh"
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

      stage('Deploy to EC2') {
    steps {
        sshagent([env.SSH_CRED]) {
            sh """
                if [ ! -d "dist" ]; then
                    echo "❌ Build directory 'dist' not found!"
                    exit 1
                fi
                ssh -o StrictHostKeyChecking=no ubuntu@${DEPLOY_SERVER} "sudo mkdir -p ${DEPLOY_DIR}"
                scp -o StrictHostKeyChecking=no -r dist/* ubuntu@${DEPLOY_SERVER}:${DEPLOY_DIR}/
                ssh -o StrictHostKeyChecking=no ubuntu@${DEPLOY_SERVER} "sudo systemctl restart nginx || echo 'Nginx not configured yet'"
            """
        }
    }
}

    }

    post {
        success {
            echo "✅ Deployment completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
    }
}
