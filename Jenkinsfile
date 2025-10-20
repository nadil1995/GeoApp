pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "nadil95/geoapp:latest"   // your Docker Hub repo
        EC2_HOST = "13.40.154.215"
        SSH_CREDENTIALS = "ubuntu"               // Jenkins SSH key ID for EC2
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

        stage('Build & Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                        echo "🔑 Logging in to Docker Hub..."
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin

                        echo "🚀 Building Docker image..."
                        # Disable BuildKit to avoid missing buildx error
                        export DOCKER_BUILDKIT=0
                        docker build -t $DOCKER_IMAGE .

                        echo "📦 Pushing image to Docker Hub..."
                        docker push $DOCKER_IMAGE

                        docker logout
                    '''
                }
            }
        }

        stage('Deploy from Docker Hub to EC2') {
            steps {
                sshagent([SSH_CREDENTIALS]) {
                    sh '''
                        echo "🚀 Deploying container on EC2..."
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
