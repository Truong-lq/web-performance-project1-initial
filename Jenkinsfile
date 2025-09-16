pipeline {
    agent any
    
    tools {
        nodejs "Node24"
    }

    environment {
        PROJECT_NAME = "trule-jenkins-2025"
    }
    
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
            stages('Deploy to Firebase') {
                    steps {
                        echo "===== DEPLOY TO FIREBASE ====="
                        // withCredentials([file(credentialsId: 'firebase_adc', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                        //     sh '''
                        //         export GOOGLE_APPLICATION_CREDENTIALS=$GOOGLE_APPLICATION_CREDENTIALS
                        //         firebase deploy --only hosting --project=${PROJECT_NAME}
                        //     '''
                        // }
                        withCredentials([string(credentialsId: 'LEGACY_TOKEN', variable: 'FIREBASE_TOKEN')]) {
                            sh '''
                                firebase deploy --token "$FIREBASE_TOKEN" --only hosting --project=${PROJECT_NAME}
                            '''
                        }
                    }
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
