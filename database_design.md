# Database Design - Infertility Treatment Management System

## Tổng quan thiết kế

Database được thiết kế với **14 bảng chính** phù hợp với scope 7 tuần và yêu cầu từ requirement document.

[![ERD]](./erd_diagram.md)

## Cấu trúc Core Entities

### 1. User Management (3 bảng)

- **Users**: Thông tin cơ bản tất cả người dùng
- **Customers**: Thông tin chi tiết bệnh nhân
- **Doctors**: Thông tin chi tiết bác sĩ

### 2. Treatment Services (2 bảng)

- **TreatmentServices**: Danh sách dịch vụ (IUI, IVF)
- **TreatmentPackages**: Các gói điều trị cụ thể với giá

### 3. Treatment Management (4 bảng)

- **TreatmentCycles**: Chu kỳ điều trị của bệnh nhân
- **TreatmentPhases**: Các giai đoạn trong chu kỳ
- **Medications**: Danh sách thuốc
- **Prescriptions**: Đơn thuốc cho từng giai đoạn

### 4. Scheduling & Monitoring (3 bảng)

- **Appointments**: Lịch hẹn khám, xét nghiệm
- **TestResults**: Kết quả xét nghiệm và theo dõi
- **Notifications**: Hệ thống thông báo nhắc nhở

### 5. Content & Feedback (2 bảng)

- **BlogPosts**: Blog chia sẻ kinh nghiệm
- **Reviews**: Đánh giá dịch vụ và bác sĩ

## Key Design Decisions

### 1. Phân quyền đơn giản

- Sử dụng Role trong Users table thay vì role-based complex system
- Phù hợp với scope ngắn hạn

### 2. Flexible Data Storage

- Sử dụng JSON cho MedicalHistory, TestResults
- Dễ mở rộng mà không cần alter schema

### 3. Treatment Flow

```
TreatmentCycles (Chu kỳ điều trị)
├── TreatmentPhases (Giai đoạn)
│ └── Prescriptions (Đơn thuốc)
├── Appointments (Lịch hẹn)
├── TestResults (Kết quả)
└── Reviews (Đánh giá)
```

### 4. Soft Delete Pattern

- Sử dụng IsActive flag thay vì hard delete
- Bảo toàn dữ liệu quan trọng

## Sample Data Flow

### 1. Đăng ký điều trị

```

Customer đăng ký → TreatmentCycle tạo → Doctor phân công

```

### 2. Quá trình điều trị

```

TreatmentCycle → TreatmentPhases → Prescriptions
↓
Appointments ← → TestResults

```

### 3. Theo dõi và đánh giá

```

TestResults → Notifications → Customer
TreatmentCycle complete → Reviews

```

## Entity Relationships Overview

```

Users (1:1) Customers/Doctors
↓
TreatmentCycles (Many cycles per customer)
├── TreatmentPhases (Sequential phases)
├── Appointments (Multiple appointments)
├── TestResults (Regular monitoring)
└── Reviews (Post-treatment feedback)

```

## Danh sách các bảng chính

### 1. Users (Quản lý người dùng)

Lưu trữ thông tin cơ bản của tất cả người dùng trong hệ thống.

**Columns:**

- Id (int, PK, Identity)
- Email (nvarchar(255), Unique, Not Null)
- PasswordHash (nvarchar(500), Not Null)
- FullName (nvarchar(100), Not Null)
- PhoneNumber (nvarchar(20))
- Gender (nvarchar(10)) - Male/Female
- Role (nvarchar(50), Not Null) - Customer/Doctor/Manager/Admin
- IsActive (bit, Default: 1)
- CreatedAt (datetime2, Default: GETDATE())
- UpdatedAt (datetime2)

### 2. Customers (Thông tin chi tiết bệnh nhân)

Mở rộng thông tin cho người dùng có role Customer.

**Columns:**

- Id (int, PK, Identity)
- UserId (int, FK -> Users.Id, Unique)
- Address (nvarchar(500))
- EmergencyContactName (nvarchar(200))
- EmergencyContactPhone (nvarchar(20))
- MedicalHistory (nvarchar(max)) - JSON format
- MaritalStatus (nvarchar(50))
- Occupation (nvarchar(200))
- CreatedAt (datetime2, Default: GETDATE())
- UpdatedAt (datetime2)

### 3. Doctors (Thông tin bác sĩ)

Thông tin chi tiết về bác sĩ trong hệ thống.

**Columns:**

- Id (int, PK, Identity)
- UserId (int, FK -> Users.Id, Unique)
- LicenseNumber (nvarchar(100), Unique)
- Specialization (nvarchar(200))
- YearsOfExperience (int)
- Education (nvarchar(500))
- Biography (nvarchar(max))
- ConsultationFee (decimal(10,2))
- IsAvailable (bit, Default: 1)
- SuccessRate (decimal(5,2)) - Percentage
- CreatedAt (datetime2, Default: GETDATE())
- UpdatedAt (datetime2)

### 4. TreatmentServices (Dịch vụ điều trị)

Danh sách các dịch vụ điều trị hiếm muộn.

**Columns:**

- Id (int, PK, Identity)
- Name (nvarchar(200), Not Null) - IUI, IVF, etc.
- Description (nvarchar(max))
- BasePrice (decimal(12,2))
- EstimatedDuration (int) - Duration in days
- Requirements (nvarchar(max)) - JSON format
- IsActive (bit, Default: 1)
- CreatedAt (datetime2, Default: GETDATE())
- UpdatedAt (datetime2)

### 5. TreatmentPackages (Gói điều trị)

Các gói điều trị cụ thể với giá và nội dung chi tiết.

**Columns:**

- Id (int, PK, Identity)
- ServiceId (int, FK -> TreatmentServices.Id)
- PackageName (nvarchar(200))
- Description (nvarchar(max))
- Price (decimal(12,2))
- IncludedServices (nvarchar(max)) - JSON format
- DurationWeeks (int)
- IsActive (bit, Default: 1)
- CreatedAt (datetime2, Default: GETDATE())
- UpdatedAt (datetime2)

### 6. TreatmentCycles (Chu kỳ điều trị)

Theo dõi từng chu kỳ điều trị của bệnh nhân.

**Columns:**

- Id (int, PK, Identity)
- CustomerId (int, FK -> Customers.Id)
- DoctorId (int, FK -> Doctors.Id)
- PackageId (int, FK -> TreatmentPackages.Id)
- CycleNumber (int) - Chu kỳ thứ mấy của bệnh nhân
- Status (nvarchar(50)) - Registered/InProgress/Completed/Cancelled
- StartDate (datetime2)
- ExpectedEndDate (datetime2)
- ActualEndDate (datetime2)
- TotalCost (decimal(12,2))
- Notes (nvarchar(max))
- IsSuccessful (bit) - Null until completed
- CreatedAt (datetime2, Default: GETDATE())
- UpdatedAt (datetime2)

### 7. TreatmentPhases (Giai đoạn điều trị)

Các giai đoạn trong một chu kỳ điều trị.

**Columns:**

- Id (int, PK, Identity)
- CycleId (int, FK -> TreatmentCycles.Id)
- PhaseName (nvarchar(200)) - Stimulation/Monitoring/Retrieval/Transfer/Waiting
- PhaseOrder (int)
- Status (nvarchar(50)) - Pending/InProgress/Completed
- StartDate (datetime2)
- EndDate (datetime2)
- Instructions (nvarchar(max))
- Notes (nvarchar(max))
- CreatedAt (datetime2, Default: GETDATE())
- UpdatedAt (datetime2)

### 8. Medications (Danh sách thuốc)

Thông tin về các loại thuốc sử dụng trong điều trị.

**Columns:**

- Id (int, PK, Identity)
- Name (nvarchar(200), Not Null)
- ActiveIngredient (nvarchar(200))
- Manufacturer (nvarchar(200))
- Description (nvarchar(max))
- StorageInstructions (nvarchar(500))
- SideEffects (nvarchar(max))
- IsActive (bit, Default: 1)
- CreatedAt (datetime2, Default: GETDATE())

### 9. Prescriptions (Đơn thuốc)

Đơn thuốc được kê cho từng giai đoạn điều trị.

**Columns:**

- Id (int, PK, Identity)
- PhaseId (int, FK -> TreatmentPhases.Id)
- MedicationId (int, FK -> Medications.Id)
- Dosage (nvarchar(100))
- Frequency (nvarchar(100))
- Duration (int) - Days
- Instructions (nvarchar(500))
- StartDate (datetime2)
- EndDate (datetime2)
- IsActive (bit, Default: 1)
- CreatedAt (datetime2, Default: GETDATE())

### 10. Appointments (Lịch hẹn)

Quản lý lịch hẹn khám và theo dõi.

**Columns:**

- Id (int, PK, Identity)
- CycleId (int, FK -> TreatmentCycles.Id)
- DoctorId (int, FK -> Doctors.Id)
- AppointmentType (nvarchar(100)) - Consultation/Ultrasound/BloodTest/Procedure
- ScheduledDateTime (datetime2)
- Status (nvarchar(50)) - Scheduled/Confirmed/Completed/Cancelled/NoShow
- Duration (int) - Minutes
- Notes (nvarchar(max))
- Results (nvarchar(max))
- CreatedAt (datetime2, Default: GETDATE())
- UpdatedAt (datetime2)

### 11. TestResults (Kết quả xét nghiệm)

Lưu trữ kết quả xét nghiệm và theo dõi.

**Columns:**

- Id (int, PK, Identity)
- CycleId (int, FK -> TreatmentCycles.Id)
- TestType (nvarchar(100)) - BloodTest/Ultrasound/HormoneLevel
- TestDate (datetime2)
- Results (nvarchar(max)) - JSON format for structured data
- ReferenceRange (nvarchar(200))
- Status (nvarchar(50)) - Normal/Abnormal/RequiresAttention
- DoctorNotes (nvarchar(max))
- CreatedAt (datetime2, Default: GETDATE())

### 12. Reviews (Đánh giá dịch vụ)

Đánh giá của bệnh nhân về dịch vụ và bác sĩ.

**Columns:**

- Id (int, PK, Identity)
- CustomerId (int, FK -> Customers.Id)
- DoctorId (int, FK -> Doctors.Id, Nullable)
- CycleId (int, FK -> TreatmentCycles.Id, Nullable)
- Rating (int) - 1-5 stars
- ReviewType (nvarchar(50)) - Service/Doctor/Overall
- Comment (nvarchar(max))
- IsApproved (bit, Default: 0)
- CreatedAt (datetime2, Default: GETDATE())

### 13. BlogPosts (Blog chia sẻ)

Bài viết chia sẻ kinh nghiệm và thông tin y khoa.

**Columns:**

- Id (int, PK, Identity)
- AuthorId (int, FK -> Users.Id)
- Title (nvarchar(300))
- Content (nvarchar(max))
- Summary (nvarchar(500))
- Category (nvarchar(100))
- Tags (nvarchar(500)) - Comma separated
- Status (nvarchar(50)) - Draft/Published/Archived
- ViewCount (int, Default: 0)
- PublishedAt (datetime2)
- CreatedAt (datetime2, Default: GETDATE())
- UpdatedAt (datetime2)

### 14. Notifications (Thông báo hệ thống)

Hệ thống thông báo và nhắc nhở.

**Columns:**

- Id (int, PK, Identity)
- UserId (int, FK -> Users.Id)
- Title (nvarchar(200))
- Message (nvarchar(max))
- Type (nvarchar(50)) - Reminder/Appointment/Result/General
- IsRead (bit, Default: 0)
- RelatedEntityType (nvarchar(100)) - Appointment/Cycle/Test
- RelatedEntityId (int)
- ScheduledAt (datetime2)
- SentAt (datetime2)
- CreatedAt (datetime2, Default: GETDATE())

## Relationships Summary

### Primary Relationships:

1. **Users** → **Customers** (1:1)
2. **Users** → **Doctors** (1:1)
3. **TreatmentServices** → **TreatmentPackages** (1:n)
4. **Customers** → **TreatmentCycles** (1:n)
5. **Doctors** → **TreatmentCycles** (1:n)
6. **TreatmentPackages** → **TreatmentCycles** (1:n)
7. **TreatmentCycles** → **TreatmentPhases** (1:n)
8. **TreatmentPhases** → **Prescriptions** (1:n)
9. **Medications** → **Prescriptions** (1:n)
10. **TreatmentCycles** → **Appointments** (1:n)
11. **TreatmentCycles** → **TestResults** (1:n)
12. **Customers** → **Reviews** (1:n)
13. **Users** → **Notifications** (1:n)

## Indexes đề xuất

### Performance Indexes:

- Users: Email, Role
- TreatmentCycles: CustomerId, DoctorId, Status, StartDate
- Appointments: DoctorId, ScheduledDateTime, Status
- TestResults: CycleId, TestDate, TestType
- Notifications: UserId, IsRead, ScheduledAt
- Reviews: DoctorId, Rating, IsApproved

## Notes

- Sử dụng JSON fields cho dữ liệu flexible (MedicalHistory, TestResults)
- Soft delete pattern với IsActive flags
- Timestamps cho audit trail
- Foreign key constraints để đảm bảo data integrity
- Designed for SQL Server với Entity Framework Core
