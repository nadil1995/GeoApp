pipeline {
    agent any

    environment {
        AWS_REGION = 'eu-west-2'
        ECR_REPO = '058264525621.dkr.ecr.eu-west-2.amazonaws.com/country-access-app'
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/<your-repo>.git'
            }
        }

        stage('Login to ECR') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'aws-credentials', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh '''
                        aws ecr get-login-password --region $AWS_REGION | \
                        docker login --username AWS --password-stdin $ECR_REPO
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t country-access-app:$IMAGE_TAG .'
                sh 'docker tag country-access-app:$IMAGE_TAG $ECR_REPO:$IMAGE_TAG'
            }
        }

        stage('Push to ECR') {
            steps {
                sh 'docker push $ECR_REPO:$IMAGE_TAG'
            }
        }
    }

    post {
        success {
            echo 'Docker image successfully built and pushed to ECR!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
