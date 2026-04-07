Markdown
# Guide: Kết hợp Ollama và OpenClaw chạy ổn định 24/7

Hướng dẫn cấu hình **Ollama** kết hợp với **OpenClaw** để chạy Local LLM ổn định, tối ưu hóa RAM và giữ model luôn trong trạng thái sẵn sàng (Keep-Alive).

---

## 📋 Mục lục
1. [Cấu hình Ollama Keep-Alive](#1-cấu-hình-ollama-keep-alive)
2. [Cấu hình OpenClaw Config](#2-cấu-hình-openclaw-config)
3. [Kiểm tra và Xác minh](#3-kiểm-tra-và-xác-minh)

---

## 1. Cấu hình Ollama Keep-Alive
Mặc định, Ollama sẽ giải phóng model khỏi RAM sau một khoảng thời gian không hoạt động. Để tránh delay khi gọi tools hoặc bắt đầu chat, chúng ta sẽ ép Ollama giữ model trên RAM vĩnh viễn.

### Bước 1: Tạo thư mục và file override
Chạy lệnh sau để tạo thư mục cấu hình systemd (nếu chưa có) và mở trình soạn thảo:

```bash
sudo mkdir -p /etc/systemd/system/ollama.service.d
sudo nano /etc/systemd/system/ollama.service.d/override.conf
Bước 2: Thêm nội dung cấu hình
Xóa trắng file (nếu có nội dung cũ) và dán nội dung sau vào.
Lưu ý: Phải có dòng [Service] ở trên cùng để Systemd nhận diện đúng biến môi trường.

Ini, TOML
[Service]
Environment="OLLAMA_KEEP_ALIVE=-1"
Bước 3: Áp dụng thay đổi
Cập nhật lại daemon và khởi động lại dịch vụ Ollama để các thiết lập có hiệu lực:

Bash
sudo systemctl daemon-reload
sudo systemctl restart ollama
2. Cấu hình OpenClaw Config
Cấu hình này được tối ưu cho model Gemma 4 (26b), thiết lập các công cụ mặc định và giới hạn một số quyền truy cập hệ thống để đảm bảo an toàn.

Chỉnh sửa file cấu hình tại: ~/.openclaw/config.json

JSON
{
  "agents": {
    "defaults": {
      "workspace": "/home/user/.openclaw/workspace",
      "model": {
        "primary": "ollama/gemma4:26b-a4b-it-q4_K_M"
      },
      "models": {
        "ollama/gemma4:26b-a4b-it-q4_K_M": {}
      },
      "timeFormat": "24",
      "timeoutSeconds": 600,
      "llm": {
        "idleTimeoutSeconds": 600
      },
      "typingIntervalSeconds": 72800,
      "typingMode": "instant"
    },
    "list": [
      {
        "id": "main",
        "model": "ollama/gemma4:26b-a4b-it-q4_K_M",
        "tools": {
          "profile": "full",
          "alsoAllow": [
            "browser",
            "canvas",
            "message"
          ]
        }
      }
    ]
  },
  "gateway": {
    "mode": "local",
    "port": 18789,
    "bind": "loopback",
    "nodes": {
      "denyCommands": [
        "camera.snap",
        "camera.clip",
        "screen.record",
        "contacts.add",
        "calendar.add",
        "reminders.add",
        "sms.send",
        "sms.search"
      ]
    }
  },
  "models": {
    "mode": "merge",
    "providers": {
      "ollama": {
        "baseUrl": "[http://127.0.0.1:11434](http://127.0.0.1:11434)",
        "api": "ollama",
        "models": [
          {
            "id": "gemma4:26b-a4b-it-q4_K_M",
            "name": "gemma4:26b-a4b-it-q4_K_M",
            "contextWindow": 160000,
            "maxTokens": 819200
          }
        ]
      }
    }
  }
}
3. Kiểm tra và Xác minh
Kiểm tra biến môi trường Ollama
Chạy lệnh sau để xác nhận cấu hình Keep-Alive đã được hệ thống ghi nhận:

Bash
systemctl show ollama.service | grep "Environment"
Kết quả chuẩn: Phải xuất hiện chuỗi OLLAMA_KEEP_ALIVE=-1.

Kiểm tra kết nối Gateway
Mở trình duyệt và truy cập vào cổng cấu hình của OpenClaw để bắt đầu sử dụng:
http://127.0.0.1:18789

Chúc bro setup thành công!
