pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'dockerhub'
        DOCKER_IMAGE = "1jashshah/jenkins-demo"
    }

    stages {
        
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/1jashshah/jenkins-ci-demo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:latest .'
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: DOCKERHUB_CREDENTIALS, passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                sh 'docker push $DOCKER_IMAGE:latest'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sh """
                    # Stop container if already running
                    if [ \$(docker ps -q -f name=demo) ]; then
                        echo "Stopping existing container..."
                        docker stop demo
                    fi

                    # Remove container if exists
                    if [ \$(docker ps -aq -f name=demo) ]; then
                        echo "Removing existing container..."
                        docker rm demo
                    fi

                    # Pull latest image
                    docker pull $DOCKER_IMAGE:latest

                    # Run new container
                    docker run -d --name demo -p 80:80 $DOCKER_IMAGE:latest
                """
            }
        }
    }
}
