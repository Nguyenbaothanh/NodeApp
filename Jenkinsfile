pipeline {
    agent {
        docker {
            image 'node:18' // Image có Git và Node.js
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    tools {
        nodejs 'NodeJS'
    }
    environment {
        DOCKER_HUB_CREDENTIALS_ID = 'jen-dockerhub'
        DOCKER_HUB_REPO = 'thanh295/my-app'
    }
    stages {
        stage('Checkout Github') {
            steps {
                cleanWs() // Xóa sạch workspace
                git branch: 'main', credentialsId: 'jen-docker-github', url: 'https://github.com/Nguyenbaothanh/NodeApp.git'
            }
        }        
        stage('Install node dependencies') {
            steps {
                sh 'npm install'
            }
        }
        stage('Test Code') {
            steps {
                sh 'npm test'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_HUB_REPO}:latest")
                }
            }
        }
        stage('Trivy Scan') {
            steps {
                sh 'trivy --severity HIGH,CRITICAL --no-progress image --format table -o trivy-scan-report.txt ${DOCKER_HUB_REPO}:latest'
            }
        }
        stage('Push Image to DockerHub') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', "${DOCKER_HUB_CREDENTIALS_ID}") {
                        dockerImage.push('latest')
                    }
                }
            }
        }
    }
    post {
        success {
            echo 'Build & Deploy hoàn tất thành công!'
        }
        failure {
            echo 'Build & Deploy thất bại. Kiểm tra log.'
        }
    }
}
