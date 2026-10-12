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

        stage('Check Docker') {
            steps {
                bat 'docker --version'
                bat 'docker info'
            }
        }

        stage('Generate Image Tag') {
            steps {
                script {
                    env.IMAGE_TAG = new Date().format('yyyy-MM-dd-HHmmss')
                    env.FULL_IMAGE = "${env.IMAGE_NAME}:${env.IMAGE_TAG}"
                }

                echo "Image to build: ${env.FULL_IMAGE}"
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
                        docker login -u "%DOCKER_USERNAME%" --password-stdin
                    ''' 
                }
            }
        }

        stage('Docker Build') {
            steps {
                bat 'docker build -t "%FULL_IMAGE%" .'
            }
        }

        stage('Docker Push') {
            steps {
                bat 'docker push "%FULL_IMAGE%"'
            }
        }
    }

    post {
        success {
            echo "Successfully pushed ${env.FULL_IMAGE}"
        }

        failure {
            echo 'Pipeline failed. Check the console output.'
        }

        always {
            bat 'docker logout'
        }
    }
}