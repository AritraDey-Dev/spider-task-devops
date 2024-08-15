pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
        DOCKER_REPO = 'your-dockerhub-username'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://your-git-repo-url.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    docker.build("${DOCKER_REPO}/user-service:${IMAGE_TAG}", "./user-service")
                    docker.build("${DOCKER_REPO}/order-service:${IMAGE_TAG}", "./order-service")
                    docker.build("${DOCKER_REPO}/product-service:${IMAGE_TAG}", "./product-service")
                }
            }
        }

        stage('Run Unit Tests') {
            steps {
                script {
                    docker.image("${DOCKER_REPO}/user-service:${IMAGE_TAG}").inside {
                        sh 'npm test'
                    }
                    docker.image("${DOCKER_REPO}/order-service:${IMAGE_TAG}").inside {
                        sh 'npm test'
                    }
                    docker.image("${DOCKER_REPO}/product-service:${IMAGE_TAG}").inside {
                        sh 'npm test'
                    }
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        docker.image("${DOCKER_REPO}/user-service:${IMAGE_TAG}").push()
                        docker.image("${DOCKER_REPO}/order-service:${IMAGE_TAG}").push()
                        docker.image("${DOCKER_REPO}/product-service:${IMAGE_TAG}").push()
                    }
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                script {
                    sh 'docker-compose -f docker-compose-staging.yml up -d'
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
