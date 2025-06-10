pipeline {
    agent {
        docker {
            image 'node:18' // Image có Git và Node.js
            args '-e DOCKER_HOST=tcp://host.docker.internal:2375'
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
        stage('Setup Docker CLI') {
            steps {
                sh '''
                    apt-get update
                    apt-get install -y docker.io
                '''
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
        stage('Install Trivy') {
            steps {
                sh '''
                    apt-get update
                    apt-get install -y wget
                    wget https://github.com/aquasecurity/trivy/releases/download/v0.45.1/trivy_0.45.1_Linux-64bit.deb
                    dpkg -i trivy_0.45.1_Linux-64bit.deb
                '''
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
