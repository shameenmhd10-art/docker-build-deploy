pipeline {
    agent any

    environment {
        IMAGE_NAME = "mhdshameen10/jenkins-job"
        IMAGE_TAG = "latest"
        TRIVY = "C:\\Users\\Shameen\\AppData\\Local\\Microsoft\\WinGet\\Packages\\AquaSecurity.Trivy_Microsoft.WingGet.Source_8wekyb3d8bbwe\\trivy.exe"
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

        stage('Check Docker Credential') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker123',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat '''
                    echo Docker Username: %DOCKER_USER%
                    powershell -Command "Write-Host Password Length: $($env:DOCKER_PASS.Length)"
                    '''
                }
            }
        }

        stage('Docker Info') {
            steps {
                bat '''
                where docker
                docker --version
                docker info
                '''
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker123',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat '''
                    docker logout
                    echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                    '''
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
