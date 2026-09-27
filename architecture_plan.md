# Kiến Trúc Hệ Thống RikkeiBank API

Dự án RikkeiBank API được thiết kế theo kiến trúc **Microservices** để thay thế cho hệ thống Monolithic cũ, đảm bảo khả năng mở rộng độc lập, chịu lỗi cao và tách biệt ranh giới nghiệp vụ.

## 1. Danh Sách Các Microservices & Infrastructure

Hệ thống bao gồm các thành phần cốt lõi (Core) và các dịch vụ hạ tầng (Infrastructure):

### Dịch Vụ Hạ Tầng (Infrastructure & Gateway)
1. **Config Server (`config-server`)**: Quản lý cấu hình tập trung cho toàn bộ các microservices.
2. **Service Registry (`eureka-server`)**: Nơi các service tự động đăng ký và khám phá lẫn nhau.
3. **API Gateway (`api-gateway`)**: Định tuyến request, xác thực ban đầu, Rate Limiting và Load Balancing (tích hợp Spring Cloud Gateway).

### Các Microservices Nghiệp Vụ (Core Services)
4. **Identity Service (`identity-service`)**: Quản lý xác thực và phân quyền (JWT). Cấp phát và kiểm tra Token. Phân quyền ADMIN, TELLER, CUSTOMER.
5. **Customer Service (`customer-service`)**: Quản lý hồ sơ khách hàng, nhân viên và danh mục loại tài khoản.
6. **Account Service (`account-service`)**: Quản lý thông tin tài khoản ngân hàng và số dư của khách hàng.
7. **Transaction Service (`transaction-service`)**: Đóng vai trò là trung tâm quản lý giao dịch và điều phối Saga Pattern (Orchestrator) hoặc quản lý trạng thái luồng Choreography. Lưu lịch sử các giao dịch chuyển khoản.
8. **Notification Service (`notification-service`)**: Dịch vụ gửi thông báo biến động số dư cho khách hàng qua Email/SMS/App. (Sử dụng WebFlux & Kafka Consumer).

## 2. Thiết Kế Cơ Tiếp Cơ Sở Dữ Liệu (Database-per-service)

Mỗi service sử dụng một Database riêng biệt (MySQL) để đảm bảo tính độc lập:

- **`identity_db`**: `users`, `roles`
- **`customer_db`**: `customers`, `account_types`
- **`account_db`**: `accounts`
- **`transaction_db`**: `transactions`, `saga_logs`

## 3. Infrastructures khác
- **Kafka**: Message Broker cho Saga pattern và Notification.
- **Redis**: Dùng làm Cache phân tán (Distributed Cache).
- **Zipkin/Sleuth (Micrometer)**: Tracing (tùy chọn).
