pipeline {
    agent any
    stages {
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
