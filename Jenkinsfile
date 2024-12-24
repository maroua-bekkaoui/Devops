pipeline {
    agent any

    environment {
        IMAGE_NAME = 'calculator-app'
        DOCKER_REGISTRY = 'docker.io'
        DOCKER_REPO = 'marouabekkaoui/calculator-app'
//         REMOTE_SERVER = 'remote-server-ip'
//         SSH_CREDENTIALS = 'ssh-credentials-id'
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
                                bat '''
                                    echo "DOCKER_USERNAME: %DOCKER_USERNAME%"
                                    echo "DOCKER_PASSWORD: %DOCKER_PASSWORD%"
                                    powershell -Command "
                                        echo $env:DOCKER_PASSWORD | docker login -u $env:DOCKER_USERNAME --password-stdin $env:DOCKER_REGISTRY
                                    "
                                '''
                            }

                            bat "docker push %DOCKER_REPO%:%BUILD_NUMBER%"
                        }
                    }
                }


//         stage('Deploy to Remote Server') {
//             steps {
//                 script {
//                     sshagent([SSH_CREDENTIALS]) {
//                         bat """
//                         ssh $REMOTE_SERVER 'docker pull %DOCKER_REPO%:%BUILD_NUMBER%'
//                         ssh $REMOTE_SERVER 'docker stop %IMAGE_NAME% || true && docker rm %IMAGE_NAME% || true'
//                         ssh $REMOTE_SERVER 'docker run -d --name %IMAGE_NAME% -p 8080:8080 %DOCKER_REPO%:%BUILD_NUMBER%'
//                         """
//                     }
//                 }
//             }
//         }
    }

    post {
        success {
            echo 'Build and deploy were successful.'
        }
        failure {
            echo 'Build or deployment failed.'
        }
    }
}
