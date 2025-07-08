
# Demo Backup & Restore Đầy Đủ Trên EC2 Ubuntu

> **File này hướng dẫn demo tất cả các cách backup & restore đã trình bày ở GUIDE, bao gồm giao diện AWS và dòng lệnh tự động trên Ubuntu (EC2).**

---


## 1. Backup EC2 Instance (toàn bộ server)

### A. Backup thủ công bằng giao diện AWS Console
1. Vào AWS Console > EC2 > Instances > Chọn instance đang chạy.
2. Ở phần "Storage", chọn volume (EBS) đang gắn với instance.
3. Nhấn "Actions" > "Create snapshot".
4. Đặt tên, mô tả, nhấn "Create snapshot".
5. Snapshot này có thể dùng để restore lại toàn bộ server (OS, Jenkins, Docker, source code, ...).

### B. Backup thủ công bằng dòng lệnh trên Ubuntu
#### Backup Jenkins
```bash
sudo tar -zcvf ~/jenkins_backup_$(date +%F).tar.gz /var/lib/jenkins
```
#### Backup source code
```bash
cd /path/to/your/project
sudo tar -zcvf ~/source_backup_$(date +%F).tar.gz .
```
#### Backup log hệ thống
```bash
sudo tar -zcvf ~/log_backup_$(date +%F).tar.gz /var/log
```

---


## 2. Backup Tự Động Bằng Crontab (Khuyên dùng cho EC2 Ubuntu)

### A. Tạo script backup tự động
Tạo file `backup_all.sh`:
```bash
#!/bin/bash
# Đường dẫn lưu backup
BACKUP_DIR="$HOME/auto_backups/$(date +%F_%H-%M)"
mkdir -p "$BACKUP_DIR"

# Backup Jenkins
sudo tar -zcvf "$BACKUP_DIR/jenkins_backup.tar.gz" /var/lib/jenkins

# Backup source code (sửa lại đường dẫn cho đúng)
sudo tar -zcvf "$BACKUP_DIR/source_backup.tar.gz" /home/ubuntu/badminton-web-ver2

# Backup log hệ thống
sudo tar -zcvf "$BACKUP_DIR/log_backup.tar.gz" /var/log

# Xóa backup cũ hơn 7 ngày
find "$HOME/auto_backups" -maxdepth 1 -type d -mtime +7 -exec rm -rf {} +
```
Cấp quyền thực thi:
```bash
chmod +x ~/backup_all.sh
```

### B. Đặt lịch backup tự động với crontab
Chạy lệnh:
```bash
crontab -e
```
Thêm dòng sau để backup mỗi ngày lúc 2h sáng:
```
0 2 * * * /bin/bash $HOME/backup_all.sh
```

> **Lưu ý:**
> - Đảm bảo script chạy được với quyền truy cập các thư mục cần backup (có thể cần sudo).
> - Có thể chỉnh sửa đường dẫn backup/source/log cho phù hợp.
> - Nên đồng bộ thư mục backup lên S3 hoặc tải về máy cá nhân định kỳ.

---


## 3. Demo Backup MongoDB Atlas (database)

### A. Sử dụng chức năng backup tự động của Atlas
- Vào MongoDB Atlas > Project > Cluster > Backup > Enable Cloud Backup.
- Có thể export snapshot hoặc restore về thời điểm bất kỳ.

### B. Backup thủ công bằng mongodump
```bash
mongodump --uri "<MONGODB_ATLAS_URI>" --out ~/mongodb_backup_$(date +%F)
```

---

## 4. Demo Backup hình ảnh trên Cloudinary

### A. Sử dụng Cloudinary Admin UI
- Vào Cloudinary Dashboard > Media Library > Chọn folder cần backup > Download as ZIP.

### B. Backup qua API (tự động)
- Tham khảo: https://cloudinary.com/documentation/admin_api#get_resources
- Có thể dùng script Python để tải toàn bộ ảnh về máy chủ/S3.

---

## 5. Demo Restore Thủ Công

### A. Restore EC2 từ snapshot
1. Vào AWS Console > EC2 > Snapshots > Chọn snapshot > "Actions" > "Create Volume".
2. Tạo instance mới, attach volume vừa tạo.
3. Khởi động lại Jenkins, Docker, kiểm tra source code.

### B. Restore Jenkins
```bash
sudo systemctl stop jenkins
sudo rm -rf /var/lib/jenkins/*
sudo tar -zxvf /path/to/jenkins_backup.tar.gz -C /
sudo systemctl start jenkins
```

### C. Restore source code
```bash
cd /path/to/restore
sudo tar -zxvf /path/to/source_backup.tar.gz
```

### D. Restore log hệ thống
```bash
sudo tar -zxvf /path/to/log_backup.tar.gz -C /
```

### E. Restore MongoDB Atlas
```bash
mongorestore --uri "<MONGODB_ATLAS_URI>" ./mongodb_backup
```

### F. Restore hình ảnh lên Cloudinary
- Dùng Admin UI upload lại ZIP, hoặc dùng API upload lại ảnh.

---


---

## 6. Kiểm Tra & Lưu Ý
- Kiểm tra lại các service sau khi restore: Jenkins, Docker, backend, frontend.
- Đảm bảo quyền truy cập file/folder đúng sau khi restore.
- Nên test restore định kỳ để đảm bảo backup sử dụng được.
- Nên lưu file backup ra S3 hoặc tải về máy cá nhân.
- Có thể thêm bước tự động upload backup lên S3 bằng AWS CLI.
- Kiểm tra log backup định kỳ để đảm bảo backup thành công.

---

## 7. Tham Khảo
- [Crontab Guru](https://crontab.guru/)
- [Jenkins Backup/Restore](https://www.jenkins.io/doc/book/system-administration/backing-up/)
- [AWS EC2 User Guide](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/)
- [MongoDB mongodump/mongorestore](https://www.mongodb.com/docs/database-tools/mongodump/)
- [Cloudinary Admin API](https://cloudinary.com/documentation/admin_api)
