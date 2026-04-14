pipeline {
    agent any

    environment {
        IMAGE_NAME = "guna006/ecommerce-app:latest"
        DOCKER_PATH = "\"C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe\""
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
                // ✅ Added platform fix (IMPORTANT)
                bat '%DOCKER_PATH% build --platform linux/amd64 -t %IMAGE_NAME% .'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    // ✅ More secure login
                    bat 'echo %DOCKER_PASS% | %DOCKER_PATH% login -u %DOCKER_USER% --password-stdin'

                    // ✅ Push with tag
                    bat '%DOCKER_PATH% push %IMAGE_NAME%'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                // ✅ Ensure kubectl works
                bat 'kubectl version --client'

                // ✅ Apply deployment & service
                bat 'kubectl apply -f kubernetes/deployment.yaml'
                bat 'kubectl apply -f kubernetes/service.yaml'
            }
        }
    }
}
