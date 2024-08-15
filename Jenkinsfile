pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials' 
        IMAGE_TAG = "${env.BUILD_NUMBER}"
        DOCKER_REPO = 'coderaritra'        
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/adey-github/spider-task-devops.git'
            }
        }

        stage('Build Docker Images') {
            parallel {
                stage('Build User Service Image') {
                    steps {
                        script {
                            docker.build("${DOCKER_REPO}/user-service:${IMAGE_TAG}", "./user-service")
                        }
                    }
                }
                stage('Build Order Service Image') {
                    steps {
                        script {
                            docker.build("${DOCKER_REPO}/order-service:${IMAGE_TAG}", "./order-service")
                        }
                    }
                }
                stage('Build Product Service Image') {
                    steps {
                        script {
                            docker.build("${DOCKER_REPO}/product-service:${IMAGE_TAG}", "./product-service")
                        }
                    }
                }
            }
        }

        stage('Push Docker Images') {
            parallel {
                stage('Push User Service Image') {
                    steps {
                        script {
                            docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                                docker.image("${DOCKER_REPO}/user-service:${IMAGE_TAG}").push()
                            }
                        }
                    }
                }
                stage('Push Order Service Image') {
                    steps {
                        script {
                            docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                                docker.image("${DOCKER_REPO}/order-service:${IMAGE_TAG}").push()
                            }
                        }
                    }
                }
                stage('Push Product Service Image') {
                    steps {
                        script {
                            docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                                docker.image("${DOCKER_REPO}/product-service:${IMAGE_TAG}").push()
                            }
                        }
                    }
                }
            }
        }

        stage('Deploy to Staging') {
            steps {
                sh './deploy.sh'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
