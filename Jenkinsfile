pipeline {
    agent any
    stages {
        stage('Debug') {
            steps {
                sh 'whoami'  // Xác định user chạy Jenkins
                sh 'groups'  // Kiểm tra group của user
                sh 'ls -l /var/run/docker.sock'  // Kiểm tra quyền của Docker socket
                sh 'docker --version || echo "Docker not installed"'  // Kiểm tra Docker
            }
        }
        stage('Build and Push to DockerHub') {
            steps {
                withDockerRegistry(credentialsId: 'docker-hub', url: 'https://index.docker.io/v1/') {
                    sh 'docker build -t thanh295/nodejs:latest .'
                    sh 'docker push thanh295/nodejs:latest'
                }
            }
        }
    }
}
