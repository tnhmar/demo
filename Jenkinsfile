pipeline {
    agent any
    tools {
        maven 'maven-3.9.9'
    }

    environment {
        DOCKER_IMAGE = 'tnhmar/demo'
        DOCKER_HOST = 'tcp://docker:2376'
        DOCKER_TLS_VERIFY = 1
        DOCKER_CERT_PATH = '/certs/client'
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials-id' // Jenkins credentials ID
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests=false'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:${env.BUILD_NUMBER}")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKER_CREDENTIALS_ID}") {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
