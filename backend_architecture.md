# Backend Architecture - Infertility Treatment Management System

## Tổng quan Kiến trúc

Backend được thiết kế theo **4-Layer Architecture** với các design patterns phù hợp cho scope 7 tuần và team 2 Backend developers.

```
┌─────────────────────────────────────────┐
│           Presentation Layer            │  ← Controllers, Middleware
├─────────────────────────────────────────┤
│            Business Layer               │  ← Services, DTOs
├─────────────────────────────────────────┤
│           Data Access Layer             │  ← Repositories, UoW
├─────────────────────────────────────────┤
│             Database Layer              │  ← SQL Server, EF Core
└─────────────────────────────────────────┘
```

## 1. Presentation Layer (API Controllers)

### 1.1 Controller Organization

Controllers được tổ chức theo domain entities, mỗi controller chịu trách nhiệm một nhóm chức năng cụ thể.

**Core Controllers:**

```
Controllers/
├── AuthController.cs              # Authentication & Authorization
├── UsersController.cs             # User management
├── CustomersController.cs         # Customer profile management
├── DoctorsController.cs           # Doctor management
├── TreatmentServicesController.cs # Treatment services & packages
├── TreatmentCyclesController.cs   # Treatment cycle management
├── AppointmentsController.cs      # Appointment scheduling
├── TestResultsController.cs       # Test results management
├── PrescriptionsController.cs     # Medication prescriptions
├── ReviewsController.cs           # Reviews & ratings
├── BlogPostsController.cs         # Blog content management
├── NotificationsController.cs     # Notification system
└── DashboardController.cs         # Analytics & reporting
```

### 1.2 API Design Principles

**RESTful Convention:**

- Use HTTP verbs correctly (GET, POST, PUT, DELETE)
- Consistent URL patterns: `/api/{resource}/{id}`
- Proper HTTP status codes (200, 201, 400, 401, 404, 500)

**Response Format:**

```json
{
  "success": true,
  "message": "Operation completed successfully",
  "data": {
    /* response data */
  },
  "errors": []
}
```

## 2. Business Layer (Services)

### 2.1 Service Organization

Services chứa business logic và được tổ chức theo domain boundaries.

**Core Services:**

```
Services/
├── IAuthService.cs & AuthService.cs
├── IUserService.cs & UserService.cs
├── ICustomerService.cs & CustomerService.cs
├── IDoctorService.cs & DoctorService.cs
├── ITreatmentService.cs & TreatmentService.cs
├── ICycleService.cs & CycleService.cs
├── IAppointmentService.cs & AppointmentService.cs
├── ITestResultService.cs & TestResultService.cs
├── IPrescriptionService.cs & PrescriptionService.cs
├── INotificationService.cs & NotificationService.cs
├── IReviewService.cs & ReviewService.cs
├── IBlogService.cs & BlogService.cs
└── IDashboardService.cs & DashboardService.cs
```

### 2.2 Service Responsibilities

**ITreatmentService:**

- Business logic cho treatment cycles
- Validation chu kỳ điều trị
- Status transition logic
- Cost calculation
- Success rate tracking

**INotificationService:**

- Email notification logic
- Schedule reminder creation
- Notification template management
- Delivery status tracking

### 2.3 DTOs (Data Transfer Objects)

**Request DTOs:**

```
DTOs/Requests/
├── Auth/
│   ├── LoginRequestDto.cs
│   ├── RegisterRequestDto.cs
│   └── ChangePasswordRequestDto.cs
├── TreatmentCycles/
│   ├── CreateCycleRequestDto.cs
│   ├── UpdateCycleRequestDto.cs
│   └── CreatePhaseRequestDto.cs
└── Appointments/
    ├── CreateAppointmentRequestDto.cs
    └── UpdateAppointmentRequestDto.cs
```

**Response DTOs:**

```
DTOs/Responses/
├── Auth/
│   ├── LoginResponseDto.cs
│   └── UserProfileResponseDto.cs
├── TreatmentCycles/
│   ├── CycleResponseDto.cs
│   ├── CycleListResponseDto.cs
│   └── PhaseResponseDto.cs
└── Dashboard/
    └── DashboardStatsResponseDto.cs
```

## 3. Data Access Layer

### 3.1 Repository Pattern Implementation

**Core Repositories:**

```
Repositories/
├── Interfaces/
│   ├── IBaseRepository.cs
│   ├── IUserRepository.cs
│   ├── ICustomerRepository.cs
│   ├── IDoctorRepository.cs
│   ├── ITreatmentCycleRepository.cs
│   ├── IAppointmentRepository.cs
│   ├── ITestResultRepository.cs
│   └── IUnitOfWork.cs
└── Implementations/
    ├── BaseRepository.cs
    ├── UserRepository.cs
    ├── CustomerRepository.cs
    ├── DoctorRepository.cs
    ├── TreatmentCycleRepository.cs
    ├── AppointmentRepository.cs
    ├── TestResultRepository.cs
    └── UnitOfWork.cs
```

### 3.2 IBaseRepository Interface

```csharp
public interface IBaseRepository<T> where T : class
{
    Task<T> GetByIdAsync(int id);
    Task<IEnumerable<T>> GetAllAsync();
    Task<IEnumerable<T>> FindAsync(Expression<Func<T, bool>> predicate);
    Task<T> AddAsync(T entity);
    Task UpdateAsync(T entity);
    Task DeleteAsync(int id);
    Task<bool> ExistsAsync(int id);
    Task<int> CountAsync();
}
```

### 3.3 Unit of Work Pattern

```csharp
public interface IUnitOfWork : IDisposable
{
    IUserRepository Users { get; }
    ICustomerRepository Customers { get; }
    IDoctorRepository Doctors { get; }
    ITreatmentCycleRepository TreatmentCycles { get; }
    IAppointmentRepository Appointments { get; }
    // ... other repositories

    Task<int> SaveChangesAsync();
    Task BeginTransactionAsync();
    Task CommitTransactionAsync();
    Task RollbackTransactionAsync();
}
```

## 4. Database Layer

### 4.1 Entity Framework Core Configuration

**DbContext Configuration:**

```
Data/
├── ApplicationDbContext.cs
├── Configurations/
│   ├── UserConfiguration.cs
│   ├── CustomerConfiguration.cs
│   ├── DoctorConfiguration.cs
│   ├── TreatmentCycleConfiguration.cs
│   └── AppointmentConfiguration.cs
└── Migrations/
    └── [EF Generated Migrations]
```

### 4.2 Entity Models

```
Models/
├── Entities/
│   ├── User.cs
│   ├── Customer.cs
│   ├── Doctor.cs
│   ├── TreatmentService.cs
│   ├── TreatmentPackage.cs
│   ├── TreatmentCycle.cs
│   ├── TreatmentPhase.cs
│   ├── Medication.cs
│   ├── Prescription.cs
│   ├── Appointment.cs
│   ├── TestResult.cs
│   ├── Review.cs
│   ├── BlogPost.cs
│   └── Notification.cs
└── Enums/
    ├── UserRole.cs
    ├── CycleStatus.cs
    ├── AppointmentStatus.cs
    ├── AppointmentType.cs
    └── NotificationType.cs
```

## 5. Cross-Cutting Concerns

### 5.1 Middleware Pipeline

```
Middleware/
├── ExceptionHandlingMiddleware.cs  # Global error handling
├── JwtMiddleware.cs               # JWT token validation
├── LoggingMiddleware.cs           # Request/response logging
└── CorsMiddleware.cs              # CORS configuration
```

### 5.2 Authentication & Authorization

**JWT Configuration:**

- JWT Bearer token authentication
- Role-based authorization
- Token expiration: 1 hour (Access), 7 days (Refresh)
- Secure token storage patterns

**Authorization Policies:**

```csharp
// Policy-based authorization
"CustomerOnly" // Only customers can access
"DoctorOnly"   // Only doctors can access
"AdminOnly"    // Only admin/manager can access
"DoctorOrAdmin" // Doctor or admin access
```

### 5.3 Validation & Error Handling

**FluentValidation Integration:**

```
Validators/
├── Auth/
│   ├── LoginRequestValidator.cs
│   └── RegisterRequestValidator.cs
├── TreatmentCycles/
│   ├── CreateCycleRequestValidator.cs
│   └── UpdateCycleRequestValidator.cs
└── Appointments/
    └── CreateAppointmentRequestValidator.cs
```

**Global Exception Handling:**

- Custom exception types
- Structured error responses
- Logging integration with Serilog
- User-friendly error messages

### 5.4 Logging Strategy

**Serilog Configuration:**

- Structured logging with JSON format
- Multiple sinks: File, Console, Database
- Log levels: Debug, Information, Warning, Error, Fatal
- Request/Response logging for API calls

## 6. Configuration Management

### 6.1 Configuration Structure

```
appsettings.json
├── ConnectionStrings
├── JwtSettings
├── EmailSettings
├── CloudStorageSettings
├── CorsSettings
└── LoggingSettings
```

### 6.2 Environment-Specific Configs

- `appsettings.Development.json`
- `appsettings.Staging.json`
- `appsettings.Production.json`

## 7. Dependency Injection Configuration

### 7.1 Service Registration

```csharp
// Repository registration
services.AddScoped<IUnitOfWork, UnitOfWork>();
services.AddScoped(typeof(IBaseRepository<>), typeof(BaseRepository<>));

// Service registration
services.AddScoped<IAuthService, AuthService>();
services.AddScoped<ITreatmentService, TreatmentService>();
services.AddScoped<INotificationService, NotificationService>();

// External services
services.AddScoped<IEmailService, EmailService>();
services.AddScoped<IFileStorageService, CloudStorageService>();
```

## 8. API Documentation & Testing

### 8.1 Swagger Configuration

- Swagger UI for API documentation
- XML comments for detailed descriptions
- JWT authentication integration
- Request/Response examples

### 8.2 API Versioning Strategy

```
/api/v1/treatment-cycles
/api/v2/treatment-cycles
```

## 9. Performance Considerations

### 9.1 Caching Strategy

- In-memory caching cho reference data
- Redis caching cho user sessions
- Database query optimization với EF Core

### 9.2 Database Optimization

- Proper indexing strategy
- Query optimization với Include() statements
- Pagination cho large datasets
- Connection pooling configuration

## 10. Security Implementation

### 10.1 Data Protection

- Password hashing với BCrypt
- Sensitive data encryption
- HTTPS enforcement
- CORS configuration

### 10.2 API Security

- Rate limiting
- Input validation
- SQL injection prevention

Kiến trúc này được thiết kế để đảm bảo:

- **Scalability**: Dễ mở rộng khi cần
- **Maintainability**: Code dễ maintain và debug
- **Testability**: Dễ viết unit test và integration test
- **Security**: Tuân thủ best practices bảo mật
- **Performance**: Tối ưu hóa cho hiệu suất cao
