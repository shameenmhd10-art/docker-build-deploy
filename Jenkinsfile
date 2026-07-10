pipeline {
    agent any

    environment {
        IMAGE_NAME = "mhdshameen10/jenkins-job"
        IMAGE_TAG = "latest"
        TRIVY = "C:\\Users\\Shameen\\AppData\\Local\\Microsoft\\WinGet\\Packages\\AquaSecurity.Trivy_Microsoft.Winget.Source_8wekyb3d8bbwe\\trivy.exe"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME%:%IMAGE_TAG% .'
            }
        }

        stage('Trivy Image Scan') {
            steps {
                bat '"%TRIVY%" --version'
                bat '"%TRIVY%" image --scanners vuln --severity HIGH,CRITICAL %IMAGE_NAME%:%IMAGE_TAG%'
            }
        }

        stage('Docker Info') {
            steps {
                bat '''
                docker --version
                docker info
                '''
            }
        }

        stage('Login to Docker Hub') {
    steps {
        withCredentials([usernamePassword(
            credentialsId: 'abcd',
            usernameVariable: 'DOCKER_USER',
            passwordVariable: 'DOCKER_PASS'
        )]) {
            // Notice there is NO space between %DOCKER_PASS% and |
            bat 'echo %DOCKER_PASS%| docker login -u %DOCKER_USER% --password-stdin'
        }
    }
}

        stage('Push Image to Docker Hub') {
            steps {
                bat 'docker push %IMAGE_NAME%:%IMAGE_TAG%'
            }
        }
    }

    post {
        always {
            bat 'docker logout'
        }
    }
}
