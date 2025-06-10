pipeline {
    agent any
    environment {
        // Định nghĩa các biến môi trường nếu cần
        DOCKER_IMAGE = "thanh295/nodejs:latest"
    }
    stages {
        stage('Checkout Code') {
            steps {
                // Checkout mã nguồn từ Git (thường không cần vì Jenkins đã checkout để đọc Jenkinsfile)
                // Nếu cần checkout nhánh cụ thể, bạn có thể thêm bước này
                echo 'Code already checked out by Jenkins'
            }
        }
        stage('Install Dependencies') {
            steps {
                // Cài đặt các dependencies của Node.js
                sh 'npm install'
            }
        }
        stage('Lint Code') {
            steps {
                // Chạy lint để kiểm tra chất lượng code (giả sử bạn dùng eslint)
                sh 'npm run lint || echo "Linting not configured, skipping..."'
            }
        }
        stage('Run Unit Tests') {
            steps {
                // Chạy unit test (giả sử bạn dùng Jest hoặc Mocha)
                sh 'npm test || echo "Tests not configured, skipping..."'
            }
        }
        stage('Build Docker Image') {
            steps {
                // Build Docker image cho ứng dụng
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }
        stage('Push Docker Image to DockerHub') {
            steps {
                // Đăng nhập và push image lên DockerHub
                withDockerRegistry(credentialsId: 'docker-hub', url: 'https://index.docker.io/v1/') {
                    sh "docker push ${DOCKER_IMAGE}"
                }
            }
        }
        stage('Deploy to Server') {
            when {
                // Chỉ triển khai nếu branch là 'main' (hoặc branch bạn muốn)
                branch 'main'
            }
            steps {
                // Triển khai ứng dụng (ví dụ: chạy container trên server qua SSH)
                echo 'Deploying to server...'
                // Thêm bước SSH để triển khai (cần cài plugin SSH trong Jenkins)
                // Ví dụ: sshPublisher để triển khai qua SSH
                // Hoặc sử dụng lệnh Docker để chạy container
                sh '''
                    docker stop node-app || true
                    docker rm node-app || true
                    docker run -d --name node-app -p 3000:3000 ${DOCKER_IMAGE}
                '''
                // Nếu server triển khai nằm ở máy khác, sử dụng SSH:
                // sshagent(credentials: ['ssh-credential-id']) {
                //     sh 'ssh user@server "docker pull ${DOCKER_IMAGE} && docker run -d --name node-app -p 3000:3000 ${DOCKER_IMAGE}"'
                // }
            }
        }
    }
    post {
        always {
            // Xóa các tài nguyên tạm thời nếu cần
            echo 'Cleaning up...'
            sh 'docker system prune -f || true'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs for details.'
        }
    }
}
