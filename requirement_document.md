# Infertility Treatment Management and Monitoring System

## Tài liệu Yêu cầu Hệ thống

## 1. Tổng quan Dự án

### 1.1 Mục đích

Xây dựng hệ thống quản lý và theo dõi toàn diện quá trình điều trị hiếm muộn cho cơ sở y tế, hỗ trợ từ việc đăng ký dịch vụ đến theo dõi kết quả điều trị.

### 1.2 Phạm vi

Hệ thống bao gồm:

- Website giới thiệu cơ sở y tế và dịch vụ
- Hệ thống quản lý điều trị hiếm muộn
- Hệ thống theo dõi tiến trình điều trị
- Dashboard báo cáo và thống kê

## 2. Stakeholders

### 2.1 Primary Actors

1. **Guest** - Khách truy cập website
2. **Customer** - Bệnh nhân đăng ký điều trị
3. **Doctor** - Bác sĩ điều trị
4. **Manager** - Quản lý cơ sở y tế
5. **Admin** - Quản trị hệ thống

## 3. Yêu cầu Chức năng

### 3.1 Module Trang chủ và Giới thiệu

**FR-001: Trang chủ thông tin**

- Hiển thị thông tin giới thiệu cơ sở y tế
- Danh sách các dịch vụ điều trị hiếm muộn
- Blog chia sẻ kinh nghiệm và thông tin y khoa
- Trang liên hệ và thông tin địa chỉ

### 3.2 Module Quản lý Người dùng

**FR-002: Đăng ký và Xác thực**

- Đăng ký tài khoản cho khách hàng
- Đăng nhập/đăng xuất với JWT Authentication
- Quản lý thông tin cá nhân
- Phân quyền người dùng theo vai trò

**FR-003: Quản lý Hồ sơ Bệnh nhân**

- Lưu trữ thông tin cá nhân chi tiết
- Tiền sử bệnh lý và điều trị
- Lịch sử đặt dịch vụ điều trị
- Quản lý thông tin liên hệ khẩn cấp

### 3.3 Module Dịch vụ Điều trị

**FR-004: Đăng ký Dịch vụ**

- Danh sách các phương pháp điều trị: IUI, IVF.
- Đăng ký dịch vụ điều trị hiếm muộn
- Lựa chọn bác sĩ điều trị
- Đặt lịch hẹn khám ban đầu

**FR-005: Quản lý Thông tin Dịch vụ**

- Khai báo các gói dịch vụ điều trị
- Quản lý bảng giá theo từng phương pháp
- Mô tả chi tiết quy trình điều trị
- Điều kiện và yêu cầu cho từng phương pháp

### 3.4 Module Quản lý Quá trình Điều trị

**FR-006: Theo dõi Chu trình Điều trị**

- Tạo lịch trình điều trị theo phương pháp (IUI/IVF)
- Quản lý giai đoạn kích thích buồng trứng
- Theo dõi lịch tiêm thuốc hàng ngày
- Ghi nhận ngày thụ tinh/chuyển phôi
- Theo dõi thời kỳ chờ kết quả

**FR-007: Quản lý Thuốc và Liều lượng**

- Danh sách thuốc điều trị hiếm muộn
- Kê đơn thuốc theo từng giai đoạn
- Hướng dẫn cách sử dụng và bảo quản
- Theo dõi phản ứng phụ

**FR-008: Theo dõi Kết quả**

- Ghi nhận kết quả xét nghiệm định kỳ
- Theo dõi sự phát triển của nang trứng
- Đánh giá chất lượng phôi (với IVF)
- Xác nhận kết quả thụ thai

### 3.5 Module Lịch hẹn và Nhắc nhở

**FR-009: Quản lý Lịch hẹn**

- Đặt lịch khám định kỳ
- Lịch xét nghiệm máu, siêu âm
- Lịch tiêm thuốc tại nhà/bệnh viện
- Xác nhận và điều chỉnh lịch hẹn

**FR-010: Hệ thống Nhắc nhở**

- Thông báo qua email
- Cảnh báo lịch khám quan trọng
- Thông báo kết quả xét nghiệm

### 3.6 Module Quản lý Bác sĩ

**FR-011: Hồ sơ Bác sĩ**

- Thông tin cá nhân và liên hệ
- Bằng cấp và chứng chỉ chuyên môn
- Kinh nghiệm và chuyên môn
- Tỷ lệ thành công điều trị

**FR-012: Lịch làm việc**

- Quản lý ca trực và lịch khám
- Phân công bệnh nhân
- Theo dõi khối lượng công việc
- Báo cáo hiệu suất điều trị

### 3.7 Module Đánh giá và Phản hồi

**FR-013: Hệ thống Rating**

- Đánh giá chất lượng dịch vụ
- Rating bác sĩ và cơ sở y tế
- Bình luận và phản hồi từ bệnh nhân
- Quản lý khiếu nại

### 3.8 Module Báo cáo và Thống kê

**FR-014: Dashboard Quản lý**

- Tổng quan tình hình điều trị
- Thống kê số lượng bệnh nhân theo phương pháp
- Tỷ lệ thành công của từng bác sĩ
- Doanh thu theo thời gian

**FR-015: Báo cáo Chi tiết**

- Báo cáo tiến độ điều trị cá nhân
- Báo cáo tài chính theo dịch vụ
- Báo cáo hiệu quả điều trị
- Export báo cáo ra Excel/PDF

## 4. Yêu cầu Phi chức năng

### 4.1 Hiệu suất

- Thời gian phản hồi < 2 giây cho các thao tác thông thường
- Hỗ trợ đồng thời tối thiểu 100 người dùng
- Khả năng mở rộng theo nhu cầu

### 4.2 Bảo mật

- Mã hóa dữ liệu nhạy cảm
- JWT Authentication cho API
- Phân quyền truy cập theo vai trò
- Tuân thủ quy định bảo mật y tế

### 4.3 Khả năng sử dụng

- Giao diện thân thiện, dễ sử dụng
- Responsive design cho mobile

## 5. Yêu cầu Kỹ thuật

### 5.1 Kiến trúc Hệ thống

- **Architecture**: 4-layer architecture (Presentation, Business, Data Access, Database)
- **Pattern**: Repository Pattern, Unit of Work Pattern
- **Communication**: RESTful API

### 5.2 Backend Technology Stack

- **Framework**: ASP.NET Core Web API (.NET 8)
- **Authentication**: JWT (JSON Web Token)
- **Database**: SQL Server 2022
- **ORM**: Entity Framework Core
- **Documentation**: Swagger/OpenAPI

### 5.3 Frontend Technology Stack

- **Framework**: React 18 với TypeScript
- **Build Tool**: Vite
- **State Management**: Zustand
- **Data Fetching**: TanStack Query
- **UI Library**: Shadcn UI
- **Styling**: Tailwind CSS
- **Form Handling**: React Hook Form
- **HTTP Client**: Axios

### 5.4 Infrastructure

- **Hosting**: Cloud-based (DiigitalOcean)
- **Database**: SQL Server trên cloud
- **File Storage**: Cloud storage cho hình ảnh, tài liệu (AWS S3)

## 6. Integration Requirements

### 6.1 Third-party Services

- **Email Service**: Gửi email tự động
- **Payment Gateway**: Thanh toán online (VNPay, Momo)

### 6.2 API Documentation

- Swagger UI cho API documentation
- Postman collection cho testing
- API versioning strategy
