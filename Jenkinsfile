pipeline {
    agent any

   environment {
    DOCKER_EXE = 'C:\\ACTUAL\\PATH\\TO\\docker.exe'
    IMAGE_NAME = 'harisharan634/hello'
}

    stages {
        stage('Generate Image Tag') {
            steps {
                script {
                    env.IMAGE_TAG = new Date().format('yyyy-MM-dd-HHmmss')
                    env.FULL_IMAGE = "${env.IMAGE_NAME}:${env.IMAGE_TAG}"
                }

                echo "Image: ${env.FULL_IMAGE}"
            }
        }

        stage('Check Docker') {
            steps {
                bat '"%DOCKER_EXE%" --version'
                bat '"%DOCKER_EXE%" info'
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
                        echo %DOCKER_PASSWORD% | "%DOCKER_EXE%" login -u "%DOCKER_USERNAME%" --password-stdin
                    '''
                }
            }
        }

        stage('Docker Build') {
            steps {
                bat '"%DOCKER_EXE%" build -t "%FULL_IMAGE%" .'
            }
        }

        stage('Docker Push') {
            steps {
                bat '"%DOCKER_EXE%" push "%FULL_IMAGE%"'
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
    }
}