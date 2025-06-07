# Migration Guide - Infertility Treatment Management System

## Tổng quan

Tài liệu này hướng dẫn chi tiết về migration cho hệ thống **Infertility Treatment Management System** sử dụng Entity Framework Core với Code-First approach.

## Cấu trúc Project

```
InfertilityTreatment.sln
├── InfertilityTreatment.API/          (Startup Project)
├── InfertilityTreatment.Business/
├── InfertilityTreatment.Data/         (Chứa DbContext và Migrations)
└── InfertilityTreatment.Entity/       (Chứa Entity Models)
```

---

## 📋 Trường hợp 1: Clone source code về chưa có database

### Bước 1: Chuẩn bị môi trường

#### 1.1 Kiểm tra Prerequisites

```bash
# Kiểm tra .NET SDK (yêu cầu .NET 8)
dotnet --version

# Kiểm tra EF Core Tools
dotnet tool list -g

# Nếu chưa có EF Core Tools, cài đặt:
dotnet tool install --global dotnet-ef
```

#### 1.2 Clone và chuẩn bị project

```bash
# Clone repository (thay your-repo-url bằng URL thực tế)
git clone <your-repo-url>
cd infertility-treatment-system

# Restore packages cho toàn bộ solution
dotnet restore
```

### Bước 2: Cấu hình Connection String

#### 2.1 Mở file `InfertilityTreatment.API/appsettings.json`

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=.;Database=InfertilityTreatmentDB;Trusted_Connection=true;TrustServerCertificate=true;"
  }
}
```

#### 2.2 Tùy chỉnh connection string theo môi trường của bạn

**SQL Server với Windows Authentication:**

```json
"DefaultConnection": "Server=.;Database=InfertilityTreatmentDB;Trusted_Connection=true;TrustServerCertificate=true;"
```

**SQL Server với SQL Authentication:**

```json
"DefaultConnection": "Server=.;Database=InfertilityTreatmentDB;User Id=sa;Password=YourPassword;TrustServerCertificate=true;"
```

**SQL Server trên máy khác:**

```json
"DefaultConnection": "Server=192.168.1.100;Database=InfertilityTreatmentDB;User Id=sa;Password=YourPassword;TrustServerCertificate=true;"
```

### Bước 3: Kiểm tra Migrations hiện có

```bash
# Di chuyển đến thư mục Data project (chứa DbContext)
cd InfertilityTreatment.Data

# Liệt kê các migrations có sẵn
dotnet ef migrations list --startup-project ../InfertilityTreatment.API
```

**Output mong đợi:**

```
20240601000000_InitialCreate
20240601000001_AddRefreshTokenTable
20240601000002_AddTreatmentServices
20240601000003_AddTreatmentCycles
20240601000004_AddAppointments
20240601000005_AddTestResults
20240601000006_AddMedicationsAndPrescriptions
20240601000007_AddReviews
20240601000008_AddBlogPosts
20240601000009_AddNotifications
```

### Bước 4: Tạo Database và áp dụng Migrations

#### 4.1 Kiểm tra database đã tồn tại chưa

```bash
# Kiểm tra thông tin database
dotnet ef database drop --startup-project ../InfertilityTreatment.API --dry-run

# Nếu database chưa tồn tại, sẽ có thông báo "Database does not exist"
```

#### 4.2 Áp dụng migrations

```bash
# Tạo database và áp dụng tất cả migrations
dotnet ef database update --startup-project ../InfertilityTreatment.API

# Hoặc áp dụng đến một migration cụ thể
dotnet ef database update InitialCreate --startup-project ../InfertilityTreatment.API
```

#### 4.3 Kiểm tra kết quả

```bash
# Xem thông tin database hiện tại
dotnet ef migrations has-pending-model-changes --startup-project ../InfertilityTreatment.API

# Kiểm tra migrations đã áp dụng
dotnet ef migrations list --startup-project ../InfertilityTreatment.API
```

### Bước 5: Seed Data (Nếu có)

#### 5.1 Chạy application để seed data

```bash
# Di chuyển về API project
cd ../InfertilityTreatment.API

# Chạy application trong Development mode
dotnet run --environment Development
```

#### 5.2 Kiểm tra seed data trong database

Kết nối SQL Server và kiểm tra các bảng:

- `Users` - Nên có admin user mặc định
- `TreatmentServices` - Nên có IUI, IVF services
- `Medications` - Nên có các loại thuốc cơ bản

### Bước 6: Xác minh thiết lập thành công

```bash
# Build toàn bộ solution
dotnet build

# Chạy tests (nếu có)
dotnet test

# Chạy API và kiểm tra Swagger
dotnet run --project InfertilityTreatment.API
```

**Truy cập:** `https://localhost:7000/swagger` để kiểm tra API documentation.

---

## 🆕 Trường hợp 2: Thêm Migration mới

### Bước 1: Thay đổi Entity Models

#### 1.1 Ví dụ: Thêm trường mới vào User entity

```csharp
// InfertilityTreatment.Entity/Entities/User.cs
public class User : BaseEntity
{
    // ... existing properties ...

    // Thêm trường mới
    public string? ProfilePicture { get; set; }
    public DateTime? LastLoginAt { get; set; }
    public bool TwoFactorEnabled { get; set; } = false;
}
```

#### 1.2 Cập nhật Entity Configuration (nếu cần)

```csharp
// InfertilityTreatment.Data/Configurations/UserConfiguration.cs
public void Configure(EntityTypeBuilder<User> builder)
{
    // ... existing configurations ...

    // Cấu hình cho trường mới
    builder.Property(x => x.ProfilePicture)
           .HasMaxLength(500);

    builder.Property(x => x.TwoFactorEnabled)
           .HasDefaultValue(false);
}
```

### Bước 2: Tạo Migration mới

#### 2.1 Tạo migration với tên mô tả rõ ràng

```bash
# Di chuyển đến Data project
cd InfertilityTreatment.Data

# Tạo migration mới
dotnet ef migrations add AddUserProfileEnhancements --startup-project ../InfertilityTreatment.API
```

#### 2.2 Quy tắc đặt tên migration

- **Thêm bảng mới:** `AddTableName` (AddPayments)
- **Thêm trường:** `AddFieldToTable` (AddEmailToCustomers)
- **Xóa trường:** `RemoveFieldFromTable` (RemoveOldFieldFromUsers)
- **Thay đổi trường:** `ModifyFieldInTable` (ModifyEmailLengthInUsers)
- **Thêm index:** `AddIndexToTable` (AddIndexToUserEmail)
- **Thêm relation:** `AddRelationshipBetweenTables` (AddUserCustomerRelationship)

### Bước 3: Kiểm tra Migration được tạo

#### 3.1 Xem file migration được tạo

```bash
# File migration sẽ được tạo tại:
# InfertilityTreatment.Data/Migrations/YYYYMMDDHHmmss_AddUserProfileEnhancements.cs
```

#### 3.2 Nội dung migration mẫu

```csharp
public partial class AddUserProfileEnhancements : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.AddColumn<string>(
            name: "ProfilePicture",
            table: "Users",
            type: "nvarchar(500)",
            maxLength: 500,
            nullable: true);

        migrationBuilder.AddColumn<DateTime>(
            name: "LastLoginAt",
            table: "Users",
            type: "datetime2",
            nullable: true);

        migrationBuilder.AddColumn<bool>(
            name: "TwoFactorEnabled",
            table: "Users",
            type: "bit",
            nullable: false,
            defaultValue: false);
    }

    protected override void Down(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.DropColumn(
            name: "ProfilePicture",
            table: "Users");

        migrationBuilder.DropColumn(
            name: "LastLoginAt",
            table: "Users");

        migrationBuilder.DropColumn(
            name: "TwoFactorEnabled",
            table: "Users");
    }
}
```

### Bước 4: Review và chỉnh sửa Migration (nếu cần)

#### 4.1 Kiểm tra migration code

- Đảm bảo migration logic đúng
- Kiểm tra default values
- Xác nhận nullable/not nullable constraints
- Kiểm tra foreign key constraints

#### 4.2 Thêm custom logic nếu cần

```csharp
protected override void Up(MigrationBuilder migrationBuilder)
{
    // Generated code...

    // Custom SQL nếu cần
    migrationBuilder.Sql(@"
        UPDATE Users
        SET TwoFactorEnabled = 0
        WHERE TwoFactorEnabled IS NULL
    ");
}
```

### Bước 5: Test Migration trước khi áp dụng

#### 5.1 Tạo SQL script để review

```bash
# Tạo SQL script để xem những gì sẽ được thực thi
dotnet ef migrations script --startup-project ../InfertilityTreatment.API --output migration_script.sql

# Tạo script cho migration cụ thể
dotnet ef migrations script AddUserProfileEnhancements --startup-project ../InfertilityTreatment.API
```

#### 5.2 Test trên database copy (khuyên dùng)

```bash
# Backup database trước
# Sau đó test migration trên database copy
dotnet ef database update --startup-project ../InfertilityTreatment.API --connection "Server=.;Database=InfertilityTreatmentDB_Test;..."
```

### Bước 6: Áp dụng Migration

#### 6.1 Áp dụng migration mới

```bash
# Áp dụng migration mới nhất
dotnet ef database update --startup-project ../InfertilityTreatment.API

# Hoặc áp dụng đến migration cụ thể
dotnet ef database update AddUserProfileEnhancements --startup-project ../InfertilityTreatment.API
```

#### 6.2 Verify migration đã áp dụng thành công

```bash
# Kiểm tra migrations đã áp dụng
dotnet ef migrations list --startup-project ../InfertilityTreatment.API

# Kiểm tra model changes
dotnet ef migrations has-pending-model-changes --startup-project ../InfertilityTreatment.API
```

### Bước 7: Test Application

#### 7.1 Build và test

```bash
# Build solution
dotnet build

# Chạy tests
dotnet test

# Chạy application
dotnet run --project ../InfertilityTreatment.API
```

#### 7.2 Test API endpoints có sử dụng trường mới

```bash
# Test qua Swagger hoặc Postman
# Kiểm tra các endpoints có sử dụng User entity
```

---

## 🔧 Migration Commands Reference

### Thông tin Migration

```bash
# Liệt kê tất cả migrations
dotnet ef migrations list --startup-project ../InfertilityTreatment.API

# Kiểm tra pending model changes
dotnet ef migrations has-pending-model-changes --startup-project ../InfertilityTreatment.API

# Xem thông tin database hiện tại
dotnet ef dbcontext info --startup-project ../InfertilityTreatment.API
```

### Tạo Migration

```bash
# Tạo migration mới
dotnet ef migrations add <MigrationName> --startup-project ../InfertilityTreatment.API

# Tạo migration với output directory cụ thể
dotnet ef migrations add <MigrationName> --output-dir Migrations/Custom --startup-project ../InfertilityTreatment.API
```

### Áp dụng Migration

```bash
# Áp dụng tất cả pending migrations
dotnet ef database update --startup-project ../InfertilityTreatment.API

# Áp dụng đến migration cụ thể
dotnet ef database update <MigrationName> --startup-project ../InfertilityTreatment.API

# Rollback về migration trước đó
dotnet ef database update <PreviousMigrationName> --startup-project ../InfertilityTreatment.API
```

### Xóa Migration

```bash
# Xóa migration cuối cùng (chưa áp dụng)
dotnet ef migrations remove --startup-project ../InfertilityTreatment.API

# Force remove migration
dotnet ef migrations remove --force --startup-project ../InfertilityTreatment.API
```

### Tạo SQL Scripts

```bash
# Tạo script cho tất cả migrations
dotnet ef migrations script --startup-project ../InfertilityTreatment.API

# Tạo script từ migration A đến B
dotnet ef migrations script <FromMigration> <ToMigration> --startup-project ../InfertilityTreatment.API

# Tạo script idempotent (có thể chạy nhiều lần)
dotnet ef migrations script --idempotent --startup-project ../InfertilityTreatment.API
```

### Database Operations

```bash
# Tạo database nếu chưa có
dotnet ef database ensure-created --startup-project ../InfertilityTreatment.API

# Xóa database
dotnet ef database drop --startup-project ../InfertilityTreatment.API

# Xóa database mà không confirm
dotnet ef database drop --force --startup-project ../InfertilityTreatment.API
```

---

## ⚠️ Best Practices & Lưu ý quan trọng

### Migration Best Practices

#### 1. Naming Conventions

```bash
# ✅ Good names
AddUserProfilePicture
RemoveObsoleteFields
ModifyEmailConstraints
AddPaymentTables

# ❌ Bad names
Update1
FixBug
Changes
NewMigration
```

#### 2. Migration Content Guidelines

**✅ Làm:**

- Review migration code trước khi áp dụng
- Test migration trên database copy trước
- Backup database trước migration quan trọng
- Sử dụng meaningful migration names
- Kiểm tra Up và Down methods

**❌ Không làm:**

- Không edit migration đã áp dụng vào production
- Không xóa migration đã áp dụng
- Không merge conflicts trong migration files
- Không skip testing migrations

#### 3. Production Migration Workflow

```bash
# 1. Tạo migration trên development
dotnet ef migrations add <Name> --startup-project ../InfertilityTreatment.API

# 2. Test migration thoroughly
dotnet ef database update --startup-project ../InfertilityTreatment.API

# 3. Tạo SQL script cho production
dotnet ef migrations script --idempotent --startup-project ../InfertilityTreatment.API

# 4. Review SQL script
# 5. Backup production database
# 6. Apply script to production
# 7. Verify migration success
```

### Troubleshooting Common Issues

#### Issue 1: "No migrations configuration type was found"

```bash
# Solution: Đảm bảo đúng startup project
dotnet ef migrations add <Name> --startup-project ../InfertilityTreatment.API
```

#### Issue 2: "Unable to create an object of type 'ApplicationDbContext'"

```bash
# Solution: Kiểm tra connection string trong appsettings.json
# Đảm bảo startup project có DbContext registration
```

#### Issue 3: "Pending model changes"

```bash
# Check what changed
dotnet ef migrations has-pending-model-changes --startup-project ../InfertilityTreatment.API

# Create migration for changes
dotnet ef migrations add FixPendingChanges --startup-project ../InfertilityTreatment.API
```

#### Issue 4: Migration conflict sau git merge

```bash
# 1. Revert về migration state trước merge
dotnet ef database update <LastGoodMigration> --startup-project ../InfertilityTreatment.API

# 2. Remove conflicted migrations
dotnet ef migrations remove --startup-project ../InfertilityTreatment.API

# 3. Recreate migration
dotnet ef migrations add <NewMigrationName> --startup-project ../InfertilityTreatment.API
```

### Environment-Specific Configurations

#### Development

```json
// appsettings.Development.json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=.;Database=InfertilityTreatmentDB_Dev;Trusted_Connection=true;TrustServerCertificate=true;"
  }
}
```

#### Production

```json
// appsettings.Production.json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=prod-server;Database=InfertilityTreatmentDB;User Id=prod_user;Password=secure_password;TrustServerCertificate=true;"
  }
}
```

---

## 📋 Migration Checklist

### Trước khi tạo migration mới:

- [ ] Entity changes đã được review
- [ ] Entity configurations updated nếu cần
- [ ] Breaking changes đã được identify
- [ ] Backward compatibility đã được consider

### Sau khi tạo migration:

- [ ] Migration name meaningful và descriptive
- [ ] Migration code review đã complete
- [ ] Up và Down methods đều correct
- [ ] Test migration trên local database
- [ ] SQL script generated và reviewed

### Trước khi deploy lên production:

- [ ] Migration tested trên staging environment
- [ ] Database backup completed
- [ ] Rollback plan prepared
- [ ] Team notification sent
- [ ] Maintenance window scheduled (nếu cần)

---

## 🔗 Useful Resources

- [Entity Framework Core Migrations](https://docs.microsoft.com/en-us/ef/core/managing-schemas/migrations/)
- [Code-First Migrations](https://docs.microsoft.com/en-us/ef/core/managing-schemas/migrations/managing)
- [EF Core CLI Reference](https://docs.microsoft.com/en-us/ef/core/cli/dotnet)

---

_Tài liệu này được tạo cho Infertility Treatment Management System. Luôn backup database trước khi áp dụng migrations trong production!_
