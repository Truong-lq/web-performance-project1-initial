# Jenkins Pipeline Configuration

## Tổng quan
Pipeline Jenkins này được cấu hình để tự động hóa quá trình build, test và deploy cho dự án web performance.

## Các Stage trong Pipeline

### 1. Checkout
- Tự động checkout code từ repository
- Sử dụng `checkout scm` để lấy source code

### 2. Build
- Chạy `npm install` để cài đặt dependencies
- Chuẩn bị môi trường cho các bước tiếp theo

### 3. Lint & Test
- Chạy `npm run test:ci` để thực hiện:
  - ESLint check với `--max-warnings 0`
  - Jest tests
- Đảm bảo code quality và functionality

### 4. Deploy
- Stage deploy cơ bản (có thể tùy chỉnh theo nhu cầu)
- Hiện tại chỉ hiển thị thông báo deploy thành công

## Cách sử dụng

### 1. Tạo Pipeline Job trong Jenkins
1. Đăng nhập vào Jenkins (http://localhost:8080)
2. Click "New Item"
3. Chọn "Pipeline"
4. Đặt tên cho job (ví dụ: "web-performance-pipeline")
5. Trong phần Pipeline:
   - Definition: Pipeline script from SCM
   - SCM: Git
   - Repository URL: URL của repository chứa code
   - Script Path: Jenkinsfile

### 2. Chạy Pipeline
1. Vào job vừa tạo
2. Click "Build Now"
3. Theo dõi quá trình thực thi trong "Build History"

## Cấu hình bổ sung

### Node.js Setup
Đảm bảo Jenkins có Node.js được cài đặt:
- Cài đặt Node.js plugin trong Jenkins
- Cấu hình Node.js version trong Global Tool Configuration

### Dependencies
Dự án sử dụng:
- ESLint cho code linting
- Jest cho testing
- Jest environment jsdom cho DOM testing

## Tùy chỉnh Pipeline

### Thêm Deploy thực tế
```groovy
stage('Deploy') {
    steps {
        sh 'npm run build'  // Nếu có build script
        sh 'rsync -av dist/ user@server:/var/www/html/'  // Deploy lên server
        // Hoặc deploy lên cloud services như AWS S3, Netlify, etc.
    }
}
```

### Thêm Notifications
```groovy
post {
    success {
        emailext (
            subject: "Build Success: ${env.JOB_NAME} - ${env.BUILD_NUMBER}",
            body: "Pipeline chạy thành công!",
            to: "team@company.com"
        )
    }
    failure {
        emailext (
            subject: "Build Failed: ${env.JOB_NAME} - ${env.BUILD_NUMBER}",
            body: "Pipeline gặp lỗi!",
            to: "team@company.com"
        )
    }
}
```
