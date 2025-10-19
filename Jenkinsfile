pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
        DOCKER_IMAGE = "nadil95/geoapp:latest"   // Change to your Docker Hub repo
        EC2_HOST = "13.40.154.215"
        SSH_CREDENTIALS = "ubuntu"
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

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh '''
                    echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                    docker push $DOCKER_IMAGE
                '''
            }
        }

        stage('Deploy from Docker Hub to EC2') {
            steps {
                sshagent([SSH_CREDENTIALS]) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no ubuntu@$EC2_HOST "
                            sudo docker pull $DOCKER_IMAGE &&
                            sudo docker stop geoapp || true &&
                            sudo docker rm geoapp || true &&
                            sudo docker run -d -p 80:80 --name geoapp $DOCKER_IMAGE
                        "
                    '''
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
