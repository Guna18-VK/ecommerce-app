pipeline {
    agent any

    environment {
        IMAGE_NAME = "guna006/ecommerce-app"
        DOCKER_PATH = "C:\\Program Files\\Docker\\Docker\\resources\\bin\\docker.exe"
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

        // ✅ FIXED Docker Login (clean + reliable)
        stage('Docker Login') {
    steps {
        withCredentials([usernamePassword(
            credentialsId: 'docker-creds',
            usernameVariable: 'DOCKER_USER',
            passwordVariable: 'DOCKER_PASS'
        )]) {
            bat """
            "%DOCKER_PATH%" logout
            echo %DOCKER_PASS% | "%DOCKER_PATH%" login -u %DOCKER_USER% --password-stdin
            """
        }
    }
}

        // ✅ FIXED build (explicit tag)
        stage('Docker Build') {
            steps {
               bat '"%DOCKER_PATH%" build --platform linux/amd64 -t %IMAGE_NAME%:latest .'
            }
        }
        stage('Test Login') {
    steps {
        withCredentials([usernamePassword(
            credentialsId: 'docker-creds',
            usernameVariable: 'DOCKER_USER',
            passwordVariable: 'DOCKER_PASS'
        )]) {
            bat 'echo %DOCKER_PASS%'
        }
    }
}
        // ✅ FIXED push
        stage('Docker Push') {
            steps {
                bat '"%DOCKER_PATH%" push %IMAGE_NAME%:latest'
            }
        }

        // ✅ OPTIONAL (only if kubectl configured)
        stage('Deploy to Kubernetes') {
            steps {
                bat 'kubectl apply -f kubernetes/deployment.yaml'
                bat 'kubectl apply -f kubernetes/service.yaml'
            }
        }
    }
}
