# 🚀 SERVER vanmanhgaming – Premium RDP via GitHub Actions

## 📌 Giới thiệu
**SERVER vanmanhgaming** là một **GitHub Actions Workflow** tự động tạo **máy Windows có Remote Desktop (RDP)** chạy trên GitHub Runner (`windows-latest`).

Workflow này giúp bạn:
- Có ngay **Windows RDP dùng tạm thời**
- Không cần thuê VPS
- Không cần mở port public
- Kết nối an toàn qua **Tailscale**
- Tự động tắt & dọn dẹp khi hết thời gian

Phù hợp cho:
- Test phần mềm Windows
- Chạy tool / script
- Học tập – demo – dev nhanh
- Môi trường tạm thời, không lưu trữ lâu dài

---

## ⚙️ Workflow này làm gì?
Khi chạy, workflow sẽ **tự động thực hiện tuần tự** các bước sau:

1. Khởi động Windows Runner
2. Bật Remote Desktop (RDP)
3. Mở firewall cổng 3389 (nội bộ)
4. Tạo user **Administrator**
5. Sinh hoặc dùng mật khẩu tùy chỉnh
6. Cài đặt & kết nối **Tailscale**
7. Lấy IP riêng (private IP)
8. Kiểm tra kết nối RDP
9. Hiển thị thông tin đăng nhập
10. Duy trì phiên theo thời gian bạn chọn
11. Hết thời gian → tự động dọn dẹp & khóa hệ thống

---

## 🧱 Yêu cầu trước khi sử dụng

### 1️⃣ GitHub Account
- GitHub Free / Pro đều dùng được
- Có quyền chạy **GitHub Actions**

### 2️⃣ Tailscale Account
- Đăng ký tại https://tailscale.com
- Tạo **Auth Key** (Reusable hoặc Ephemeral đều được)

---

## 🔐 Thiết lập Secrets (BẮT BUỘC)

Vào repo GitHub → **Settings → Secrets and variables → Actions → New repository secret**

### 🔑 Secret bắt buộc
| Tên | Mô tả |
|----|------|
| `TAILSCALE_AUTH_KEY` | Auth Key của Tailscale |

### 🔐 Secret tùy chọn
| Tên | Mô tả |
|----|------|
| `CUSTOM_RDP_PASS` | Mật khẩu RDP do bạn tự đặt |

> Nếu **không đặt `CUSTOM_RDP_PASS`**, workflow sẽ **tự sinh mật khẩu an toàn**.

---

## ▶️ Cách chạy Workflow

### Bước 1: Vào Actions
- Mở repo GitHub
- Chọn tab **Actions**
- Chọn workflow: **🚀 SERVER vanmanhgaming**

### Bước 2: Run workflow
- Nhấn **Run workflow**
- Chọn **Thời gian sử dụng**
- Nhấn **Run**

### ⏱️ Các mốc thời gian hỗ trợ
- 30 phút
- 1 giờ
- 1 giờ 30 phút
- 2 → 6 giờ
- Chế độ dài hạn (>= 43200 phút = 24/7)

---

## 🧑‍💻 Thông tin đăng nhập RDP

Sau khi workflow chạy xong, log sẽ hiển thị:

- 🌐 **IP (Tailscale)**
- 👤 **User:** `vanmanhgaming`
- 🔐 **Password**
- 📍 **Port:** `3389`

### 🔑 Ví dụ
```text
IP: 100.xxx.xxx.xxx
User: vanmanhgaming
Password: xA7kP9Qm
Port: 3389
