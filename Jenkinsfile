pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'your_dockerhub_username'
        DOCKER_HUB_PASS = 'your_dockerhub_token'
        BACKEND_IMAGE = 'your_dockerhub_username/backend'
        FRONTEND_IMAGE = 'your_dockerhub_username/frontend'
    }

    stages {
        stage('Build Backend') {
            steps {
                dir('backend') {
                    sh 'docker build -t $BACKEND_IMAGE:latest .'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'docker build -t $FRONTEND_IMAGE:latest .'
                }
            }
        }

        stage('Docker Login') {
            steps {
                sh 'echo $DOCKER_HUB_PASS | docker login -u $DOCKER_HUB_USER --password-stdin'
            }
        }

        stage('Push Images') {
            steps {
                sh 'docker push $BACKEND_IMAGE:latest'
                sh 'docker push $FRONTEND_IMAGE:latest'
            }
        }
    }
}
