# GitHub Actions Runner & RDP 24/7 VPS Script

## Tổng quan

File này hướng dẫn triển khai:

1. **GitHub Actions Runner trên VPS Linux**: tự động chạy workflow 24/7.
2. **RDP 24/7 script trên VPS Linux**: giữ dịch vụ xrdp luôn bật, tự động dừng sau 90 ngày.

---

## 1️⃣ Kết nối VPS về repository GitHub

### Triển khai GitHub Actions Runner

```bash
# 1. Tạo folder chứa runner
sudo mkdir -p /opt/actions-runner
cd /opt/actions-runner

# 2. Tải runner phiên bản 2.329.0
sudo curl -O -L https://github.com/actions/runner/releases/download/v2.329.0/actions-runner-linux-x64-2.329.0.tar.gz

# 3. Giải nén
sudo tar xzf actions-runner-linux-x64-2.329.0.tar.gz

# 4. Tạo user runner
sudo useradd -m runner

# 5. Chuyển quyền sở hữu
sudo chown -R runner:runner /opt/actions-runner

# 6. Lấy token từ GitHub và cấu hình runner
# Truy cập repo GitHub -> Settings -> Actions -> Runners -> Add runner
# Chọn Linux, GitHub sẽ hiển thị token dùng 1 lần
# Ví dụ:
# ./config.sh --url https://github.com/<username>/<repo> --token <TOKEN_CỦA_BẠN>
sudo -u runner /opt/actions-runner/config.sh --url https://github.com/vamnhcorder8/VPS --token B2KR5SVNDV67SF7WLVM46N3JE2OAY

# 7. Tạo systemd service chạy 24/7
sudo bash -c 'cat <<EOF >/etc/systemd/system/actions-runner.service
[Unit]
Description=GitHub Actions Runner
After=network.target
[Service]
ExecStart=/opt/actions-runner/run.sh
User=runner
WorkingDirectory=/opt/actions-runner
Restart=always
[Install]
WantedBy=multi-user.target
EOF'

# 8. Reload systemd và kích hoạt service
sudo systemctl daemon-reload
sudo systemctl enable actions-runner
sudo systemctl start actions-runner

# 9. Kiểm tra trạng thái và log realtime
sudo systemctl status actions-runner
sudo journalctl -u actions-runner -f

# 10. Quản lý service
sudo systemctl stop actions-runner
sudo systemctl restart actions-runner
```

---

## 2️⃣ Script Treo RDP 24/7

### Tạo script giữ xrdp luôn bật

```bash
# 1. Tạo thư mục chứa script
sudo mkdir -p /opt/rdp_scripts
cd /opt/rdp_scripts

# 2. Tạo file rdp_24_7.sh
sudo nano /opt/rdp_scripts/rdp_24_7.sh
```

**Nội dung `rdp_24_7.sh`:**

```bash
#!/bin/bash
# Script giữ RDP 24/7

echo "🔄 Job bắt đầu: $(date)"

# Thời gian chạy 90 ngày (phút)
TOTAL_MINUTES=129600
END_TIME=$(date -d "+$TOTAL_MINUTES minutes" +%s)

# Hàm giám sát xrdp
keep_rdp_alive() {
  while true; do
    if ! systemctl is-active --quiet xrdp; then
      echo "🔄 Khởi động lại xrdp..."
      sudo systemctl start xrdp
    fi
    sleep 30
  done
}

keep_rdp_alive &

# Timer hiển thị thời gian còn lại
while true; do
  CURRENT_TIME=$(date +%s)
  TIME_LEFT=$((END_TIME - CURRENT_TIME))
  if [ $TIME_LEFT -le 0 ]; then
    echo -e "\n🎯 ĐÃ HẾT THỜI GIAN - TỰ ĐỘNG DỪNG!"
    pkill -f keep_rdp_alive
    exit 0
  fi
  HOURS=$((TIME_LEFT / 3600))
  MINUTES=$(((TIME_LEFT % 3600) / 60))
  SECONDS=$((TIME_LEFT % 60))
  echo -ne "⏳ Thời gian còn lại: $HOURS giờ $MINUTES phút $SECONDS giây\r"
  sleep 5
done
```

### Cấp quyền chạy script

```bash
sudo chmod +x /opt/rdp_scripts/rdp_24_7.sh
```

### Tạo systemd service cho RDP 24/7

```bash
sudo nano /etc/systemd/system/rdp_24_7.service
```

**Nội dung `rdp_24_7.service`:**

```
[Unit]
Description=RDP 24/7 Auto Job
After=network.target

[Service]
Type=simple
ExecStart=/opt/rdp_scripts/rdp_24_7.sh
User=root
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

### Kích hoạt và quản lý service

```bash
sudo systemctl daemon-reload
sudo systemctl enable rdp_24_7.service
sudo systemctl start rdp_24_7.service

# Kiểm tra trạng thái
sudo systemctl status rdp_24_7.service

# Xem log realtime
journalctl -u rdp_24_7.service -f

# Dừng hoặc restart nếu cần
sudo systemctl stop rdp_24_7.service
sudo systemctl restart rdp_24_7.service
```

---

## 3️⃣ Tóm tắt

* **GitHub Actions Runner**: chạy workflow liên tục trên VPS Linux.
* **RDP 24/7 script**: giữ xrdp luôn bật, hiển thị thời gian còn lại, tự dừng sau 90 ngày.
* **Systemd service**: restart tự động nếu script crash.
* **Hạn chế**: Runner chỉ chạy workflow, script chỉ giữ RDP trên Linux VPS, không tạo GUI Windows VPS.

---

## 4️⃣ Lưu ý

* Kiểm tra log liên tục để đảm bảo service không bị crash.
* Script và runner đều chạy dưới quyền root/user cụ thể.
* Chỉ chạy workflow và giám sát xrdp; không can thiệp GUI trên Linux.

---

## Liên hệ

© 2025 vanmanhgaming. Mọi quyền được bảo lưu.

* Facebook: [https://www.facebook.com/Bong.Toi.11022010/](https://www.facebook.com/Bong.Toi.11022010/)
* YouTube: [https://youtube.com/@vanmanhgaming](https://youtube.com/@vanmanhgaming)
* Discord: [https://discord.com/users/1118923892732477691](https://discord.com/users/1118923892732477691)
