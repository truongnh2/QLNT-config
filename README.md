# QLNT-config

Repository chứa cấu hình dùng chung cho hệ thống QLNT (Quản Lý Nhà Trọ).

## Mục đích

QLNT-config là nguồn dữ liệu cấu hình tập trung, được sử dụng bởi:
- **Client** (Flutter mobile app) — đọc URL server và các cấu hình khác
- **Server** (Spring Boot) — tham chiếu cấu hình môi trường

## Cấu trúc

```
QLNT-config/
├── server_config.json    # Cấu hình URL server cho từng môi trường
└── README.md             # Tài liệu này
```

## `server_config.json`

File JSON chứa URL của QLNT Server cho 3 môi trường:

| Môi trường   | URL mặc định                                 | Ghi chú                                     |
|-------------|---------------------------------------------|--------------------------------------------|
| development | `http://10.0.2.2:8081/api/v1`               | Android emulator → host machine            |
| staging     | `https://staging.qlnt.example.com/api/v1`    | Máy chủ staging                            |
| production  | `https://api.qlnt.example.com/api/v1`        | Máy chủ production                         |

### Cách sử dụng

#### Trong Flutter Client

1. **Build-time config**: Truyền URL qua `--dart-define`
   ```bash
   # Development
   flutter run --dart-define=API_BASE_URL=http://10.0.2.2:8081/api/v1
   
   # Production
   flutter build apk --release --dart-define=API_BASE_URL=https://api.qlnt.example.com/api/v1
   ```

2. **Runtime override**: Người dùng có thể thay đổi URL trong màn hình Settings của app.
   URL runtime được lưu trong SharedPreferences và có độ ưu tiên cao nhất.

#### Trong Spring Boot Server

Server sử dụng `application.properties` với environment variables. Port và context-path cần khớp với cấu hình trong `server_config.json`:

```properties
# application.properties
server.port=${SERVER_PORT:8081}
# Context path được định nghĩa qua @RequestMapping("/api/v1") trong controllers
```

## Cập nhật cấu hình

Khi URL server thay đổi:
1. Cập nhật `server_config.json` với URL mới
2. Commit và push lên git
3. Client sẽ tự động nhận cấu hình mới khi build lại (qua `--dart-define`)
4. Hoặc người dùng có thể nhập URL mới trực tiếp trong app

## Lưu ý bảo mật

- **KHÔNG** commit các secret keys, token, hay mật khẩu vào repo này
- Sử dụng biến môi trường hoặc CI/CD variables cho các giá trị nhạy cảm
- File `server_config.json` chỉ chứa các URL công khai
