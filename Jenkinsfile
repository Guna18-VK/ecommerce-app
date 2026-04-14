pipeline {
    agent any

    environment {
        IMAGE_NAME = "guna18vk/ecommerce-app:latest"
    }

    stages {
        stage('Build') {
            steps {
                bat 'npm install'
            }
        }

        stage('Test') {
            steps {
                bat 'npm test'
            }
        }

        stage('Docker Build') {
            steps {
                bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe" build -t %IMAGE_NAME% .'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe" login -u %DOCKER_USER% -p %DOCKER_PASS%'
                    bat '"C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe" push %IMAGE_NAME%'
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                bat 'kubectl apply -f kubernetes/deployment.yaml'
                bat 'kubectl apply -f kubernetes/service.yaml'
            }
        }
    }
}
