pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds123')
        IMAGE_NAME = "bhuvanraj123/jenkins-job"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                bat """
                docker build -t %IMAGE_NAME%:%IMAGE_TAG% .
                """
            }
        }

        stage('Trivy Image Scan') {
            steps {
                bat """
                trivy image --scanners vuln --severity HIGH,CRITICAL %IMAGE_NAME%:%IMAGE_TAG%
                """
            }
        }

        stage('Login to Docker Hub') {
            steps {
                bat """
                echo %DOCKERHUB_CREDENTIALS_PSW% | docker login -u %DOCKERHUB_CREDENTIALS_USR% --password-stdin
                """
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                bat """
                docker push %IMAGE_NAME%:%IMAGE_TAG%
                """
            }
        }

    }

    post {
        always {
            bat 'docker logout'
        }
    }
}
