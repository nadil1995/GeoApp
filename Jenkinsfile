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
                sh '''
                ssh -o StrictHostKeyChecking=no $DEPLOY_SERVER "mkdir -p $DEPLOY_PATH"
                rsync -avz --delete build/ $DEPLOY_SERVER:$DEPLOY_PATH/
                '''
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
