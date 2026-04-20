pipeline {
    agent any

    environment {
        IMAGE = "nandhini0916/portfolio"
        TAG = "v1"
    }

    stages {

        stage('Clone Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Nandhini0916/Portfolio.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE%:%TAG% .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-pass', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    bat 'docker login -u %USER% -p %PASS%'
                    bat 'docker push %IMAGE%:%TAG%'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                bat 'docker push %IMAGE%:%TAG%'
            }
        }

        stage('Stop Old Container (if running)') {
            steps {
                bat '''
                docker stop portfolio-container || exit 0
                docker rm portfolio-container || exit 0
                '''
            }
        }

        stage('Run New Container') {
            steps {
                bat 'docker run -d -p 9090:80 --name portfolio-container %IMAGE%:%TAG%'
            }
        }
    }

    post {
        success {
            echo '✅ Deployment Successful! Access your app at http://localhost:9090'
        }
        failure {
            echo '❌ Pipeline Failed. Check logs.'
        }
    }
}