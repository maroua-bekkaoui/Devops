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
                           def dockerUsername = 'marouabekkaoui20@gmail.com'
                           def dockerPassword = '123456789'
                           def dockerRegistry = 'docker.io'
                           def dockerRepo = 'marouabekkaoui/calculator-app'

                           bat """
                               docker login -u ${dockerUsername} -p ${dockerPassword} ${dockerRegistry}
                           """

                           bat "docker push ${dockerRepo}:${BUILD_NUMBER}"
                       }
                   }
               }

          stage('Deploy to Railway') {
                    steps {
                        script {
                            echo 'Deploying to Railway...'

                            sh '''
                                railway up --docker
                            '''
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
