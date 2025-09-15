# Khắc phục lỗi "npm: not found" trong Jenkins

## 🔍 Nguyên nhân lỗi
Lỗi `npm: not found` xảy ra khi Jenkins không tìm thấy Node.js và npm trong PATH của container/agent.

## 🛠️ Các giải pháp

### Giải pháp 1: Sử dụng Node.js Tool (Khuyến nghị)

#### Bước 1: Cài đặt Node.js Plugin
1. Vào Jenkins Dashboard
2. Manage Jenkins → Manage Plugins
3. Available → Tìm "NodeJS Plugin"
4. Install và restart Jenkins

#### Bước 2: Cấu hình Node.js Tool
1. Manage Jenkins → Global Tool Configuration
2. Tìm phần "NodeJS"
3. Add NodeJS:
   - Name: `NodeJS-18`
   - Install automatically: ✅
   - Version: `18.19.0` (hoặc version mới nhất)
4. Save

#### Bước 3: Sử dụng Jenkinsfile đã cập nhật
```groovy
pipeline {
    agent any
    
    tools {
        nodejs 'NodeJS-18' // Tên tool đã cấu hình
    }
    
    stages {
        // ... các stages khác
    }
}
```

### Giải pháp 2: Cài đặt Node.js trực tiếp trong Pipeline

Sử dụng `Jenkinsfile.standalone` - file này sẽ tự động cài đặt Node.js trong quá trình chạy pipeline.

### Giải pháp 3: Cài đặt Node.js trong Docker Container

Nếu sử dụng Docker agent, cập nhật Dockerfile:

```dockerfile
FROM jenkins/jenkins:lts

# Cài đặt Node.js
USER root
RUN curl -fsSL https://deb.nodesource.com/setup_18.x | bash - \
    && apt-get install -y nodejs

USER jenkins
```

## 🔧 Kiểm tra và Debug

### Kiểm tra Node.js có sẵn không:
```bash
# Trong Jenkins console hoặc SSH vào container
which node
which npm
node --version
npm --version
```

### Kiểm tra PATH:
```bash
echo $PATH
```

### Kiểm tra Jenkins Tools:
1. Manage Jenkins → Global Tool Configuration
2. Xem danh sách NodeJS tools đã cấu hình

## 📝 Các file Jenkinsfile có sẵn

1. **`Jenkinsfile`** - Sử dụng Node.js tool (cần cấu hình trước)
2. **`Jenkinsfile.standalone`** - Tự động cài đặt Node.js
3. **`Jenkinsfile.advanced`** - Phiên bản nâng cao (đã bị xóa)

## ⚡ Khuyến nghị

1. **Sử dụng Giải pháp 1** nếu bạn có quyền cấu hình Jenkins
2. **Sử dụng Giải pháp 2** nếu không thể cấu hình global tools
3. **Kiểm tra logs** trong Jenkins để xem chi tiết lỗi

## 🚨 Lưu ý quan trọng

- Đảm bảo Jenkins có quyền truy cập internet để download Node.js
- Kiểm tra firewall và proxy settings
- Restart Jenkins sau khi cài đặt plugin
- Kiểm tra disk space đủ cho việc cài đặt Node.js

## 📞 Hỗ trợ thêm

Nếu vẫn gặp lỗi, hãy kiểm tra:
1. Jenkins logs: `/var/jenkins_home/logs/`
2. Console output của build
3. System information trong Jenkins
