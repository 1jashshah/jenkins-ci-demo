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
                    docker pull $DOCKER_IMAGE:latest &&
                    docker run -d --name demo -p 80:80 $DOCKER_IMAGE:latest
                '
                """
            }
        }
    }
}
