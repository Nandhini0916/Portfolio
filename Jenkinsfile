pipeline {
    agent any

    environment {
        IMAGE_NAME = "nandhini0916/portfolio:v1"
        CONTAINER_NAME = "portfolio-container"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Nandhini0916/Portfolio.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %IMAGE_NAME% ."
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-pass',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat """
                    echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                    """
                }
            }
        }

        stage('Push Image') {
            steps {
                bat "docker push %IMAGE_NAME%"
            }
        }

        stage('Deploy Container') {
            steps {
                bat """
                docker stop %CONTAINER_NAME% || exit 0
                docker rm %CONTAINER_NAME% || exit 0
                docker run -d -p 9090:80 --name %CONTAINER_NAME% %IMAGE_NAME%
                """
            }
        }
    }

    post {
        success {
            echo "🚀 Auto Deploy Successful! App running at http://localhost:9090"
        }
        failure {
            echo "❌ Deployment Failed - check logs"
        }
    }
}