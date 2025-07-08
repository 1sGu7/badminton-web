# Hướng dẫn Backup & Restore toàn bộ hệ thống Badminton Shop trên EC2

> **Áp dụng cho hệ thống triển khai CI/CD với Jenkins, Docker, backend/frontend trên EC2 Ubuntu, database MongoDB Atlas, lưu trữ ảnh Cloudinary.**

---

## 1. Backup EC2 Instance (toàn bộ server)

### A. Backup thủ công bằng snapshot (AWS Console)
1. Vào AWS Console > EC2 > Instances > Chọn instance đang chạy.
2. Ở phần "Storage", chọn volume (EBS) đang gắn với instance.
3. Nhấn "Actions" > "Create snapshot".
4. Đặt tên, mô tả, nhấn "Create snapshot".
5. Snapshot này có thể dùng để restore lại toàn bộ server (OS, Jenkins, Docker, source code, ...).

### B. Backup Jenkins cấu hình và dữ liệu
```bash
sudo tar -zcvf jenkins_backup_$(date +%F).tar.gz /var/lib/jenkins
```
- Lưu file backup này ra S3 hoặc tải về máy cá nhân.


### C. Backup tự động bằng crontab trên Ubuntu (khuyên dùng cho EC2 Ubuntu)
#### 1. Tạo script backup (ví dụ: `backup_system.sh`)
```bash
#!/bin/bash
# Đường dẫn lưu backup
BACKUP_DIR="$HOME/backup_$(date +%F_%H-%M)"
mkdir -p "$BACKUP_DIR"

# Backup Jenkins
sudo tar -zcvf "$BACKUP_DIR/jenkins_backup.tar.gz" /var/lib/jenkins

# Backup source code (nếu cần)
# cp -r /path/to/source/code "$BACKUP_DIR/source_code"

# Backup MongoDB Atlas (nếu dùng mongodump)
mongodump --uri "<MONGODB_ATLAS_URI>" --out "$BACKUP_DIR/mongodb_backup"

# Nén toàn bộ backup
cd "$HOME" && tar -zcvf "backup_full_$(date +%F_%H-%M).tar.gz" "$(basename $BACKUP_DIR)"

# Xóa backup tạm
rm -rf "$BACKUP_DIR"
```
#### 2. Cấp quyền thực thi cho script
```bash
chmod +x ~/backup_system.sh
```
#### 3. Thiết lập crontab để tự động backup mỗi ngày lúc 2h sáng
```bash
crontab -e
# Thêm dòng sau vào cuối file:
0 2 * * * /bin/bash $HOME/backup_system.sh
```

### D. Backup source code (nếu có thay đổi ngoài Git)
- Đảm bảo mọi thay đổi đã được commit và push lên GitHub.

---

## 2. Backup MongoDB Atlas (database)

### A. Sử dụng chức năng backup tự động của Atlas
- Vào MongoDB Atlas > Project > Cluster > Backup > Enable Cloud Backup.
- Có thể export snapshot hoặc restore về thời điểm bất kỳ.

### B. Backup thủ công bằng mongodump
```bash
mongodump --uri "<MONGODB_ATLAS_URI>" --out ~/mongodb_backup_$(date +%F)
```
- Tải folder backup này về máy hoặc lưu lên S3.

---

## 3. Backup hình ảnh trên Cloudinary

### A. Sử dụng Cloudinary Admin UI
- Vào Cloudinary Dashboard > Media Library > Chọn folder cần backup > Download as ZIP.

### B. Backup qua API (tự động)
- Tham khảo: https://cloudinary.com/documentation/admin_api#get_resources
- Có thể dùng script Python để tải toàn bộ ảnh về máy chủ/S3.

---

## 4. Restore toàn bộ hệ thống

### A. Restore EC2 từ snapshot
1. Vào AWS Console > EC2 > Snapshots > Chọn snapshot > "Actions" > "Create Volume".
2. Tạo instance mới, attach volume vừa tạo.
3. Khởi động lại Jenkins, Docker, kiểm tra source code.

### B. Restore Jenkins
```bash
sudo systemctl stop jenkins
sudo rm -rf /var/lib/jenkins/*
sudo tar -zxvf jenkins_backup_xxx.tar.gz -C /
sudo systemctl start jenkins
```

### C. Restore MongoDB Atlas
- Vào Atlas > Backup > Restore snapshot về cluster.
- Hoặc dùng mongorestore:
```bash
mongorestore --uri "<MONGODB_ATLAS_URI>" ~/mongodb_backup_xxx
```

### D. Restore hình ảnh lên Cloudinary
- Dùng Admin UI upload lại ZIP, hoặc dùng API upload lại ảnh.

---

## 5. Lưu ý bảo mật & kiểm tra
- Luôn kiểm tra lại các biến môi trường, credentials Jenkins, file .env.
- Đảm bảo các service (Jenkins, Docker, backend, frontend) đều chạy bình thường.
- Kiểm tra lại domain, SSL, webhook GitHub nếu có.

---

## 6. Tài liệu tham khảo
- [AWS EC2 Snapshots](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-creating-snapshot.html)
- [MongoDB Atlas Backup](https://www.mongodb.com/docs/atlas/backup/)
- [Cloudinary Admin API](https://cloudinary.com/documentation/admin_api)
- [Jenkins Backup/Restore](https://www.jenkins.io/doc/book/system-administration/backing-up/)

---

> **Khuyến nghị:**
> - Đặt lịch backup định kỳ cho Jenkins, MongoDB Atlas, Cloudinary.
> - Lưu backup ra nhiều nơi (S3, máy cá nhân, ...).
> - Test restore định kỳ để đảm bảo backup thực sự sử dụng được khi cần.
