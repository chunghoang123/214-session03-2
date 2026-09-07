# Bài 2 — Tổ chức Centralized Configuration cho hệ thống FoodX

## Cấu trúc thư mục Git Repository

```
config-repo/
├── restaurant-service.yml
├── order-service.yml
└── delivery-service.yml
```

## Giải thích các lỗi đã khắc phục

### Lỗi 1: Tên file không đúng quy ước

**Vấn đề:** File cấu hình ban đầu đặt tên là `config.yml`, không khớp với `spring.application.name = "restaurant-service"`.

**Hậu quả:** Config Server hoạt động theo nguyên tắc: khi service gọi `GET /{application-name}/{profile}`, Config Server sẽ tìm file có tên `{application-name}.yml` trong Git repository. Nếu tên file không khớp, Config Server trả về lỗi 404 và service không thể khởi tạo cấu hình.

**Sửa:** Đổi tên file thành `restaurant-service.yml`, `order-service.yml`, `delivery-service.yml` — đúng bằng giá trị `spring.application.name` tương ứng.

### Lỗi 2: Mật khẩu lưu dạng plaintext

**Vấn đề:** Mật khẩu `RestaurantPass123` được lưu trực tiếp dưới dạng chữ thường trong file YAML.

**Rủi ro:**
- Bất kỳ ai có quyền truy cập Git repository đều xem được mật khẩu.
- Vi phạm nguyên tắc bảo mật cơ bản: không bao giờ lưu secret ở dạng plaintext.
- Nếu Git repository bị leak, toàn bộ hệ thống cơ sở dữ liệu bị compromit.

**Sửa:** Sử dụng cú pháp mã hóa `{cipher}`:
```yaml
password: "{cipher}AQIC2j8QtNDUj3Fk5rF8gK3mVx7p9wY2bN4zR6tH1jL"
```

## Cấu hình từng service

| Service | File cấu hình | Port |
|---------|--------------|------|
| restaurant-service | `restaurant-service.yml` | 8085 |
| order-service | `order-service.yml` | 8086 |
| delivery-service | `delivery-service.yml` | 8087 |

## Mô tả cấu trúc kho cấu hình tập trung

Git repository chứa toàn bộ cấu hình cho 3 service của FoodX. Mỗi service có một file YAML đặt tên đúng theo `spring.application.name`. Config Server sẽ phục vụ cấu hình từ repository này. Mật khẩu được mã hóa bằng cơ chế `{cipher}` của Spring Cloud Config, đảm bảo bảo mật khi lưu trữ tập trung.
