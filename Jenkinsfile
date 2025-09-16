pipeline {
    agent any
    
    tools {
        nodejs "Node24"
    }

    environment {
        // Common
        GIT_AUTHOR = sh(script: 'git log -1 --pretty=format:"%an"', returnStdout: true).trim()
        FORMATTED_TIME = sh(script: 'TZ="Asia/Bangkok" date +"%H:%M %d-%m-%Y"', returnStdout: true).trim()
        RELEASE_DATE = sh(script: 'date +%Y%m%d', returnStdout: true).trim()

        // Firebase
        PROJECT_NAME = "trule-jenkins-2025"
        FIREBASE_PRODUCT_URL = "https://trule-jenkins-2025.firebaseapp.com"

        // Remote host
        REMOTE_HOST = '118.69.34.46'
        REMOTE_PORT = '3334'
        REMOTE_USER = 'newbie'
        REMOTE_PATH = '/usr/share/nginx/html/jenkins'
        WORKSPACE_NAME = 'truongle-jenkins'
        MAIN_FOLDER = 'web-performance-project1-initial'
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
            parallel {
                // stage('Deploy to Firebase') {
                //     steps {
                //         echo "Bắt đầu deploy lên Firebase"
                //         sh 'npx firebase --version'

                //         withCredentials([string(credentialsId: 'LEGACY_TOKEN', variable: 'FIREBASE_TOKEN')]) {
                //             sh '''
                //                 export FIREBASE_TOKEN="$FIREBASE_TOKEN"
                //                 npm run deploy:legacy -- --project=${PROJECT_NAME}
                //             '''
                //         }
                //         echo 'Deploy lên Firebase hoàn thành!'
                //     }
                // }

                stage('Deploy to Remote Host') {
                    steps {
                        echo "Bắt đầu deploy lên Remote Server"
                        script {
                            def releaseDir = "${REMOTE_PATH}/${WORKSPACE_NAME}/deploy/${RELEASE_DATE}"
                            def mainDir = "${REMOTE_PATH}/${WORKSPACE_NAME}/${MAIN_FOLDER}"

                            withCredentials([file(credentialsId: 'SSH_KEY', variable: 'SSH_KEY')]) {
                                sh """
                                    ssh -o StrictHostKeyChecking=no -i \$SSH_KEY -p \${REMOTE_PORT} \${REMOTE_USER}@\${REMOTE_HOST} '
                                        if [ ! -d "${REMOTE_PATH}/${WORKSPACE_NAME}" ]; then
                                            cp -r ${REMOTE_PATH}/template2 ${REMOTE_PATH}/${WORKSPACE_NAME}
                                        fi
                                    '
                                    ssh -o StrictHostKeyChecking=no -i \$SSH_KEY -p \${REMOTE_PORT} \${REMOTE_USER}@\${REMOTE_HOST} "mkdir -p ${releaseDir}"
                                    scp -o StrictHostKeyChecking=no -i \$SSH_KEY -P \${REMOTE_PORT} index.html \${REMOTE_USER}@\${REMOTE_HOST}:${mainDir}/
                                    scp -o StrictHostKeyChecking=no -i \$SSH_KEY -P \${REMOTE_PORT} 404.html \${REMOTE_USER}@\${REMOTE_HOST}:${mainDir}/
                                    scp -o StrictHostKeyChecking=no -i \$SSH_KEY -P \${REMOTE_PORT} -r css \${REMOTE_USER}@\${REMOTE_HOST}:${mainDir}/
                                    scp -o StrictHostKeyChecking=no -i \$SSH_KEY -P \${REMOTE_PORT} -r js \${REMOTE_USER}@\${REMOTE_HOST}:${mainDir}/
                                    scp -o StrictHostKeyChecking=no -i \$SSH_KEY -P \${REMOTE_PORT} -r images \${REMOTE_USER}@\${REMOTE_HOST}:${mainDir}/
                                    ssh -o StrictHostKeyChecking=no -i \$SSH_KEY -p \${REMOTE_PORT} \${REMOTE_USER}@\${REMOTE_HOST} "cp -r ${mainDir}/index.html ${releaseDir}/"
                                    ssh -o StrictHostKeyChecking=no -i \$SSH_KEY -p \${REMOTE_PORT} \${REMOTE_USER}@\${REMOTE_HOST} "cp -r ${mainDir}/404.html ${releaseDir}/"
                                    ssh -o StrictHostKeyChecking=no -i \$SSH_KEY -p \${REMOTE_PORT} \${REMOTE_USER}@\${REMOTE_HOST} "cp -r ${mainDir}/css ${releaseDir}/"
                                    ssh -o StrictHostKeyChecking=no -i \$SSH_KEY -p \${REMOTE_PORT} \${REMOTE_USER}@\${REMOTE_HOST} "cp -r ${mainDir}/js ${releaseDir}/"
                                    ssh -o StrictHostKeyChecking=no -i \$SSH_KEY -p \${REMOTE_PORT} \${REMOTE_USER}@\${REMOTE_HOST} "cp -r ${mainDir}/images ${releaseDir}/"
                                    ssh -o StrictHostKeyChecking=no -i \$SSH_KEY -p \${REMOTE_PORT} \${REMOTE_USER}@\${REMOTE_HOST} "
                                        cd \${REMOTE_PATH}/\${WORKSPACE_NAME}/deploy
                                        rm -f current
                                        ln -s "\${RELEASE_DATE}" current
                                        ls -1t | grep -E '^[0-9]{8}\$' | tail -n +6 | xargs -r rm -rf
                                    "
                                """
                            }
                            echo 'Deploy lên Remote Server hoàn thành!'
                        }
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

            slackSend (
                color: 'good',
                message: ":ninja:  *Thủ phạm*   ${GIT_AUTHOR}\n\n" +
                        ":date:  *Thời gian*    ${FORMATTED_TIME}\n\n" +
                        ":confirmed:  <${FIREBASE_PRODUCT_URL}|Firebase>  |  <http://${REMOTE_HOST}/jenkins/${WORKSPACE_NAME}/deploy/current/|Remote Server>"
            )
        }
        failure {
            echo 'Pipeline gặp lỗi!'
        }
    }
}
