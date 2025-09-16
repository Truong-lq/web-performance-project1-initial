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
            steps {
                echo "===== DEPLOY TO FIREBASE ====="
                sh 'npx firebase --version'
                
                withCredentials([string(credentialsId: 'LEGACY_TOKEN', variable: 'FIREBASE_TOKEN')]) {
                    sh '''
                        export FIREBASE_TOKEN="$FIREBASE_TOKEN"
                        npm run deploy:legacy -- --project=${PROJECT_NAME}
                    '''
                }
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

            slackSend (
                channel: '#lnd-2025-workshop',
                color: 'good',
                message: ":white_check_mark: *SUCCESS*\n" +
                        // ":small_blue_diamond: *User:* ${GIT_AUTHOR}\n" +
                        // ":small_blue_diamond: *Job:* ${env.JOB_NAME}\n" +
                        // ":small_blue_diamond: *Build:* #${env.BUILD_NUMBER}\n" +
                        // ":small_blue_diamond: *Release:* ${RELEASE_DATE}\n" +
                        ":small_blue_diamond: *Firebase:* https://trule-jenkins-2025.firebaseapp.com\n" +
                        // ":small_blue_diamond: *Remote:* http://${REMOTE_HOST}/jenkins/${WORKSPACE_NAME}/deploy/current/"
            )
        }
        failure {
            echo 'Pipeline gặp lỗi!'
        }
    }
}
