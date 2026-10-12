pipeline {
    agent any

    environment {
        IMAGE_NAME = 'harisharan634/hello'
    }

    stages {
        stage('Git Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    env.IMAGE_TAG = new Date().format('yyyy-MM-dd-HHmmss')
                    env.FULL_IMAGE = "${env.IMAGE_NAME}:${env.IMAGE_TAG}"
                }

                bat 'docker build -t %FULL_IMAGE% .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker_cred',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    bat '''
                        @echo off
                        echo %DOCKER_PASSWORD% | docker login -u %DOCKER_USERNAME% --password-stdin
                    '''
                }
            }
        }

        stage('Docker Push') {
            steps {
                bat 'docker push %FULL_IMAGE%'
            }
        }
    }

    post {
        success {
            echo "Successfully pushed ${env.FULL_IMAGE}"
        }

        failure {
            echo 'Pipeline failed. Check the Jenkins console output.'
        }

        always {
            bat 'docker logout'
        }
    }
}