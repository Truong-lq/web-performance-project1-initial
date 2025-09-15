pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo 'Code đã được checkout thành công'
            }
        }
        
        stage('Build') {
            steps {
                echo 'Bắt đầu quá trình build...'
                sh 'npm install'
                echo 'Build hoàn thành!'
            }
        }
        
        stage('Lint & Test') {
            steps {
                echo 'Bắt đầu quá trình lint và test...'
                sh 'npm run test:ci'
                echo 'Lint và test hoàn thành!'
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Bắt đầu quá trình deploy...'
                // Có thể thêm các bước deploy tùy theo môi trường
                // Ví dụ: copy files, restart services, etc.
                echo 'Deploy hoàn thành!'
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline đã hoàn thành!'
        }
        success {
            echo 'Pipeline chạy thành công!'
        }
        failure {
            echo 'Pipeline gặp lỗi!'
        }
    }
}
