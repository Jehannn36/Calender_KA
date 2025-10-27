pipeline {
    agent any

    environment {
        IMAGENAME = 'jihannatasya/calender_app'
        REGISTRYCREDENTIALS = 'dockerhub-credentials'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "${REGISTRYCREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        bat '''
                            echo ===== LOGIN TO DOCKER HUB =====
                            docker logout
                            docker login -u %DOCKER_USER% -p %DOCKER_PASS%

                            echo ===== PULL BASE IMAGES =====
                            docker pull node:18-alpine
                            docker pull nginx:alpine

                            echo ===== BUILD DOCKER IMAGE =====
                            docker build -t %IMAGENAME%:%BUILD_NUMBER% .
                        '''
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "${REGISTRYCREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        bat '''
                            echo ===== PUSH DOCKER IMAGE =====
                            docker login -u %DOCKER_USER% -p %DOCKER_PASS%
                            docker push %IMAGENAME%:%BUILD_NUMBER%
                            docker tag %IMAGENAME%:%BUILD_NUMBER% %IMAGENAME%:latest
                            docker push %IMAGENAME%:latest
                        '''
                    }
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished'
        }
    }
}
