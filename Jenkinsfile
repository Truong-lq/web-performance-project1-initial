pipeline {
    agent any
    
    tools {
        nodejs "Node24"
    }

    environment {
        PROJECT_NAME = "trule-jenkins-2025"
        GIT_AUTHOR = sh(script: 'git log -1 --pretty=format:"%an"', returnStdout: true).trim()
        RELEASE_TIME = sh(script: 'TZ="Asia/Bangkok" date +"%H:%M %d-%m-%Y"', returnStdout: true).trim()
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
                color: 'good',
                message: ":ninja:  *Thủ phạm*   ${GIT_AUTHOR}\n\n" +
                        ":date:  *Thời gian*    ${RELEASE_TIME}\n\n" +
                        ":confirmed:  <https://trule-jenkins-2025.firebaseapp.com|Firebase>  "
            )
        }
        failure {
            echo 'Pipeline gặp lỗi!'
        }
    }
}
