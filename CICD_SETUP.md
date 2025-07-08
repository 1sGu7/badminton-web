## 🔄 Hướng dẫn khởi động lại website khi máy chủ tắt hoặc khởi động lại

Khi EC2/VPS/server bị tắt hoặc reboot, bạn cần khởi động lại website thủ công như sau:

1. Đăng nhập SSH vào server, cd vào thư mục dự án.
2. Nếu container cũ còn, xóa trước:
   ```bash
   docker rm -f badminton-web
   ```
3. Chạy lại container:
   ```bash
   docker run -d --name badminton-web -p 80:80 -p 5000:5000 --env-file .env badminton-web:latest
   ```
4. Nếu cần build lại image:
   ```bash
   docker build -t badminton-web:latest .
   docker run -d --name badminton-web -p 80:80 -p 5000:5000 --env-file .env badminton-web:latest
   ```
5. Kiểm tra log:
   ```bash
   docker logs badminton-web
   ```
6. Truy cập lại web qua IP hoặc domain.

**Nên dùng các tool như systemd, pm2, hoặc Docker restart policy để tự động khởi động lại container khi máy chủ khởi động lại (xem mục nâng cao bên dưới).**


<h1 align="center">🚀 CI/CD Setup Guide with Jenkins (AWS EC2 Free Tier)</h1>
<p align="center">
  <img src="https://img.shields.io/badge/CI%2FCD-Jenkins-orange" alt="Jenkins"/>
  <img src="https://img.shields.io/badge/Cloud-AWS-yellow" alt="AWS"/>
  <img src="https://img.shields.io/badge/Docker-blue" alt="Docker"/>
</p>

## Yêu cầu hệ thống

1. AWS EC2 instance (Free Tier):
   - Ubuntu Server 22.04 LTS (khuyến nghị, nhẹ, ổn định)
   - 1 vCPU, 1GB RAM, 30GB storage (Free Tier)
   - Security group mở các port:
     - 22 (SSH)
     - 80 (HTTP)
     - 8080 (Jenkins)
     - 5000 (Backend API, nếu cần truy cập trực tiếp)

2. GitHub repository với mã nguồn project

## Các bước cài đặt


### 1. Install Jenkins & Java (latest stable)

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install latest OpenJDK (JDK 21 LTS)
sudo apt install openjdk-21-jdk -y

# Verify Java version
java -version

# Add Jenkins repository
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

# Install Jenkins
sudo apt update
sudo apt install jenkins -y

# Start Jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
```


### 2. Install Docker

```bash
# Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Add both ubuntu and jenkins user vào group docker (fix lỗi permission)
sudo usermod -aG docker ubuntu
sudo usermod -aG docker jenkins

# Đăng xuất SSH và đăng nhập lại để group có hiệu lực (hoặc reboot)
exit
# Sau đó SSH lại vào EC2

# Restart Jenkins để nhận quyền docker
sudo systemctl restart jenkins
```


### 3. Configure Jenkins

1. Truy cập Jenkins tại http://your-ec2-ip:8080
2. Lấy mật khẩu admin lần đầu:
   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```
3. Cài đặt plugin đề xuất
4. Tạo user admin
5. Cài thêm các plugin:
   - Docker Pipeline
   - GitHub Integration
   - Credentials Plugin


### 4. Thêm Jenkins Credentials (biến môi trường bảo mật)

**Hướng dẫn chi tiết:**

1. Truy cập Jenkins Dashboard > Manage Jenkins > Manage Credentials
2. Chọn (hoặc tạo) domain `Global` (nếu chưa có, chọn `(global)` hoặc `Global credentials (unrestricted)`)
3. Nhấn **Add Credentials** (Thêm thông tin xác thực)
4. Ở mục **Kind**, chọn **Secret text**
5. Ở mục **Secret**, nhập giá trị tương ứng với biến môi trường (ví dụ: connection string MongoDB, JWT secret, v.v.)
6. Ở mục **ID**, nhập đúng tên biến môi trường (ví dụ: `MONGODB_URI`, `JWT_SECRET`, ...)
7. Nhấn **OK** để lưu lại

**Lặp lại các bước trên cho từng biến sau:**

| ID (tên biến)           | Giá trị cần nhập (Secret)                  |
|-------------------------|--------------------------------------------|
| MONGODB_URI             | MongoDB Atlas connection string            |
| JWT_SECRET              | JWT secret key                             |
| ENCRYPTION_KEY          | 64 ký tự hex cho AES-256                   |
| CLOUDINARY_CLOUD_NAME   | Cloudinary cloud name                      |
| CLOUDINARY_API_KEY      | Cloudinary API key                         |
| CLOUDINARY_API_SECRET   | Cloudinary API secret                      |
| FRONTEND_URL            | URL frontend (ví dụ: http://localhost:3000)|

> **Lưu ý:**
> - Phải nhập đúng **ID** (không có dấu cách, không thêm ký tự thừa)
> - Không public các giá trị này lên GitHub
> - Sau khi tạo xong, Jenkinsfile sẽ tự động lấy các giá trị này để build `.env` cho ứng dụng

### 5. Configure GitHub Webhook

1. Go to your GitHub repository > Settings > Webhooks
2. Add webhook:
   - Payload URL: http://your-ec2-ip:8080/github-webhook/
   - Content type: application/json
   - Select: Just the push event
   - Active: ✓

### 6. Create Jenkins Pipeline

1. Go to Jenkins Dashboard > New Item
2. Enter name and select "Pipeline"
3. Configure:
   - GitHub project: [Your repository URL]
   - Build Triggers: GitHub hook trigger for GITScm polling
   - Pipeline: Pipeline script from SCM
   - SCM: Git
   - Repository URL: [Your repository URL]
   - Credentials: Add your GitHub credentials
   - Branch Specifier: */main
   - Script Path: Jenkinsfile

## Sử dụng

1. The pipeline will automatically trigger when you push to the main branch
2. You can also trigger manually from Jenkins dashboard
3. Monitor the build process in Jenkins

## Khắc phục sự cố

1. Nếu Jenkins không truy cập được Docker:
   ```bash
   # Đảm bảo cả user ubuntu và jenkins đều thuộc group docker
   sudo usermod -aG docker ubuntu
   sudo usermod -aG docker jenkins
   # Đăng xuất SSH và đăng nhập lại, hoặc reboot EC2
   sudo reboot
   ```

2. Nếu nginx config lỗi:
   ```bash
   docker exec -it [container-id] nginx -t
   ```

3. Xem log container:
   ```bash
   docker logs [container-id]
   ```

## Lưu ý bảo mật

1. Luôn dùng Jenkins credentials cho dữ liệu nhạy cảm
2. Thường xuyên update hệ thống bằng `sudo apt update && sudo apt upgrade -y`
3. Đặt mật khẩu mạnh cho tất cả dịch vụ
4. Có thể dùng AWS Secrets Manager cho production
5. Kiểm tra kỹ security group của EC2, chỉ mở port cần thiết

## Bảo trì

1. Backup Jenkins:
   ```bash
   sudo tar -zcvf jenkins_backup.tar.gz /var/lib/jenkins
   ```

2. Xoá image Docker cũ:
   ```bash
   docker system prune -a
   ```

3. Kiểm tra dung lượng ổ đĩa:
   ```bash
   df -h
   ```
