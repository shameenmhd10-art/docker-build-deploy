pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds123')
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
                bat '"%TRIVY%" image --scanners vuln --severity HIGH,CRITICAL %IMAGE_NAME%:%IMAGE_TAG%'
            }
        }

        stage('Verify DockerHub Username') {
            steps {
                bat 'echo Docker Hub Username: %DOCKERHUB_CREDENTIALS_USR%'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                bat '''
                echo %DOCKERHUB_CREDENTIALS_PSW% | docker login -u %DOCKERHUB_CREDENTIALS_USR% --password-stdin
                '''
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
