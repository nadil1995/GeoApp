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
                // Copy build folder to your server
                sshagent(['server-ssh-key']) {
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@13.40.154.215 "mkdir -p /var/www/country-access-app"'
                sh 'rsync -avz --delete build/ ubuntu@13.40.154.215:/var/www/country-access-app/'
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
