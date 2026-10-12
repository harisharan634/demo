pipeline {
    agent any

    environment {
        DOCKER_EXE = 'C:\\Users\\hari\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'
        IMAGE_NAME = 'harisharan634/hello'
    }

    stages {
        stage('Generate Image Tag') {
            steps {
                script {
                    env.IMAGE_TAG = new Date().format('yyyy-MM-dd-HHmmss')
                    env.FULL_IMAGE = "${env.IMAGE_NAME}:${env.IMAGE_TAG}"
                }

                echo "Docker image: ${env.FULL_IMAGE}"
            }
        }

        stage('Diagnose Docker') {
    steps {
        bat '''
            @echo off
            echo ===== Jenkins account =====
            whoami

            echo ===== Docker executable =====
            if exist "C:\\Users\\hari\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe" (
                echo Docker executable exists
            ) else (
                echo Docker executable NOT FOUND
            )

            echo ===== Docker version =====
            "C:\\Users\\hari\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe" --version

            echo ===== Docker engine =====
            "C:\\Users\\hari\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe" info
        '''
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
            echo "SUCCESS: Image pushed: ${env.FULL_IMAGE}"
        }

        failure {
            echo 'FAILED: Check the Jenkins console output.'
        }
    }
}