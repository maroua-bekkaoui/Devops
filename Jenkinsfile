pipeline {
    agent any

    environment {
        IMAGE_NAME = 'calculator-app'
        DOCKER_REGISTRY = 'docker.io'
        DOCKER_REPO = 'marouabekkaoui/calculator-app'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    checkout scm
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    bat 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Unit Tests') {
            steps {
                script {
                    bat 'mvn test'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    bat 'docker build -t %DOCKER_REPO%:%BUILD_NUMBER% .'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: '2024', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        echo "DOCKER_USERNAME: %DOCKER_USERNAME%"
                        echo "DOCKER_PASSWORD: %DOCKER_PASSWORD%"

                        bat """
                            powershell -Command \"
                                \$env:DOCKER_USERNAME='%DOCKER_USERNAME%'
                                \$env:DOCKER_PASSWORD='%DOCKER_PASSWORD%'
                                \$env:DOCKER_REGISTRY='%DOCKER_REGISTRY%'
                                echo \$env:DOCKER_PASSWORD | docker login -u \$env:DOCKER_USERNAME --password-stdin \$env:DOCKER_REGISTRY
                            \"
                        """

                        bat "docker push %DOCKER_REPO%:%BUILD_NUMBER%"
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Build and push to Docker registry were successful.'
        }
        failure {
            echo 'Build or push failed.'
        }
    }
}
