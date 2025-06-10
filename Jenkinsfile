pipeline {
    agent any

    stages {
        stage('Clone Source Code') {
            steps {
                git credentialsId: 'github-key', url: 'https://github.com/Nguyenbaothanh/NodeApp.git'
            }
        }

        stage('Login to DockerHub') {
            steps {
                withDockerRegistry(credentialsId: 'docker-hub', url: 'https://index.docker.io/v1/') {
                    // Ví dụ: build và push image
                    sh 'docker build -t thanh295/nodejs:latest .'
                    sh 'docker push thanh295/nodejs:latest'
                }
            }
        }
    }
}
