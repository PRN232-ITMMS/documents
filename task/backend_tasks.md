# Backend Development Tasks - Infertility Treatment Management System

# Week 1: Project Foundation & 4-Layer Setup (✅ COMPLETED)

## 🎯 Sprint Goals

- 4-layer project structure setup
- Code-first database foundation
- Basic authentication system

### Issue #BE001: 4-Layer Project Structure Setup

**Assignee**: BE1  
**Priority**: High  
**Story Points**: 6

**Description:**
Set up clean 4-layer architecture với separate projects và configure dependencies.

**Acceptance Criteria:**

- [ ] Create solution với 4 projects:
  ```
  InfertilityTreatment.sln
  ├── InfertilityTreatment.API/
  ├── InfertilityTreatment.Business/
  ├── InfertilityTreatment.Data/
  └── InfertilityTreatment.Entity/
  ```
- [ ] Configure project dependencies đúng thứ tự:
  - API → Business + Entity
  - Business → Data + Entity
  - Data → Entity
- [ ] Install required NuGet packages cho từng project
- [ ] Set up appsettings.json với connection strings và JWT settings
- [ ] Configure Program.cs với service registration
- [ ] Add Swagger documentation với JWT authentication

**Definition of Done:**

- Solution builds thành công
- All project dependencies configured đúng
- Swagger UI accessible với JWT auth support

---

### Issue #BE002: Entity Layer Foundation (Code-First)

**Assignee**: BE2  
**Priority**: High  
**Story Points**: 8

**Description:**
Tạo Entity layer với all domain models, DTOs, enums theo code-first approach.

**Acceptance Criteria:**

- [ ] Create base entities:
  ```csharp
  BaseEntity.cs - Id, CreatedAt, UpdatedAt, IsActive
  IAuditableEntity.cs - interface cho audit fields
  ```
- [ ] Create core entities:
  - User (Id, Email, PasswordHash, FullName, PhoneNumber, Gender, Role)
  - Customer (UserId FK, Address, EmergencyContact, MedicalHistory)
  - Doctor (UserId FK, LicenseNumber, Specialization, Experience)
  - RefreshToken (UserId FK, Token, ExpiresAt, IsRevoked)
- [ ] Create treatment entities:
  - TreatmentService, TreatmentPackage, TreatmentCycle, TreatmentPhase
- [ ] Create enums:
  - UserRole (Customer=1, Doctor=2, Manager=3, Admin=4)
  - Gender, CycleStatus, AppointmentStatus, etc.
- [ ] Create DTOs cho authentication:
  - LoginRequestDto, RegisterRequestDto, LoginResponseDto
- [ ] Create common DTOs:
  - ApiResponseDto<T>, PaginatedResultDto<T>

**Definition of Done:**

- All entities defined với proper relationships
- DTOs separated từ entities
- Enums using proper types (byte for UserRole)
- No compilation errors

---

### Issue #BE003: Data Layer with Code-First Configuration

**Assignee**: BE1  
**Priority**: High  
**Story Points**: 9

**Description:**
Implement Data layer với ApplicationDbContext, entity configurations, và repository pattern.

**Acceptance Criteria:**

- [ ] Create ApplicationDbContext:
  ```csharp
  - DbSets cho all entities
  - OnModelCreating với configuration assembly loading
  - SaveChangesAsync override cho audit fields
  ```
- [ ] Create entity configurations using IEntityTypeConfiguration:
  - UserConfiguration - relationships, indexes, constraints
  - CustomerConfiguration, DoctorConfiguration
  - RefreshTokenConfiguration
  - TreatmentCycleConfiguration, AppointmentConfiguration
- [ ] Implement Repository Pattern:
  ```csharp
  IBaseRepository<T> - CRUD operations
  IUserRepository - custom user operations
  ICustomerRepository, ITreatmentCycleRepository
  ```
- [ ] Implement Unit of Work pattern:
  ```csharp
  IUnitOfWork - aggregate repositories, transaction support
  UnitOfWork implementation
  ```
- [ ] Create and run initial migration:
  ```bash
  dotnet ef migrations add InitialCreate --startup-project API
  dotnet ef database update --startup-project API
  ```
- [ ] Create data seeders cho test data

**Definition of Done:**

- Database created với all tables và relationships
- Repository pattern working correctly
- Initial migration applied successfully
- Seed data available

---

# Week 2: Authentication System & Business Logic (✅ COMPLETED)

## 🎯 Sprint Goals

- Complete JWT authentication với refresh tokens
- Business layer implementation
- User management APIs

### Issue #BE004: JWT Authentication System Implementation

**Assignee**: BE2  
**Priority**: High  
**Story Points**: 9

**Description:**
Implement comprehensive JWT authentication system với refresh token stored in database.

**Acceptance Criteria:**

- [ ] Create Business layer structure:
  ```
  InfertilityTreatment.Business/
  ├── Interfaces/IAuthService.cs
  ├── Services/AuthService.cs
  ├── Helpers/JwtHelper.cs, PasswordHelper.cs
  ├── Validators/LoginRequestValidator.cs
  └── Mappings/AutoMapperProfile.cs
  ```
- [ ] Implement IAuthService với methods:
  - LoginAsync(LoginRequestDto) → LoginResponseDto
  - RegisterAsync(RegisterRequestDto) → RegisterResponseDto
  - RefreshTokenAsync(RefreshTokenRequestDto) → LoginResponseDto
  - LogoutAsync(int userId) → revoke refresh tokens
- [ ] Create JwtHelper:
  - GenerateAccessToken(User) → JWT string
  - GenerateRefreshTokenAsync(int userId) → RefreshToken entity
  - ValidateRefreshToken(string token) → bool
- [ ] Implement password hashing với BCrypt
- [ ] Create FluentValidation validators:
  - LoginRequestValidator, RegisterRequestValidator
- [ ] Add AutoMapper profiles cho entity ↔ DTO mapping

**Definition of Done:**

- Authentication system hoàn chỉnh
- Refresh tokens stored securely in database
- Password hashing implemented correctly
- Validation working properly

---

### Issue #BE005: API Layer & Service Registration

**Assignee**: BE1  
**Priority**: High  
**Story Points**: 7

**Description:**
Create API controllers và configure dependency injection cho all layers.

**Acceptance Criteria:**

- [ ] Create AuthController:
  ```csharp
  POST /api/auth/login
  POST /api/auth/register
  POST /api/auth/refresh
  POST /api/auth/logout
  ```
- [ ] Create ServiceCollectionExtensions:
  ```csharp
  AddBusinessServices() - register repositories, services
  AddJwtAuthentication() - JWT + authorization policies
  ```
- [ ] Configure Program.cs:
  - Database context registration
  - Business services registration
  - JWT authentication setup
  - CORS policy cho React app
  - Swagger với JWT support
- [ ] Create middleware:
  - ExceptionHandlingMiddleware - global error handling
  - LoggingMiddleware - request/response logging
- [ ] Add authorization policies:
  - \"CustomerOnly\", \"DoctorOnly\", \"AdminOnly\"
- [ ] Implement API response format:
  ```csharp
  ApiResponseDto<T> { Success, Data, Message, Errors }
  ```

**Definition of Done:**

- Authentication APIs working correctly
- Dependency injection configured properly
- Error handling implemented
- Swagger documentation complete

---

### Issue #BE006: User Management APIs

**Assignee**: BE2  
**Priority**: Medium  
**Story Points**: 6

**Description:**
Implement user management business logic và APIs.

**Acceptance Criteria:**

- [ ] Create IUserService và UserService:
  - GetProfileAsync(int userId) → UserProfileDto
  - UpdateProfileAsync(int userId, UpdateProfileDto)
  - ChangePasswordAsync(int userId, ChangePasswordDto)
- [ ] Create ICustomerService và CustomerService:
  - GetCustomerProfileAsync(int customerId)
  - UpdateCustomerProfileAsync(int customerId, CustomerProfileDto)
  - UpdateMedicalHistoryAsync(int customerId, string medicalHistory)
- [ ] Create API controllers:

  ```csharp
  UsersController:
  - GET /api/users/profile
  - PUT /api/users/profile
  - PUT /api/users/change-password

  CustomersController:
  - GET /api/customers/{id}
  - PUT /api/customers/{id}
  - PUT /api/customers/{id}/medical-history
  ```

- [ ] Add proper authorization:
  - Users can only access their own data
  - Admins can access all user data
- [ ] Create validators cho update operations
- [ ] Add audit logging cho profile changes

**Definition of Done:**

- User profile management working
- Customer-specific data management implemented
- Authorization working correctly
- Validation preventing invalid updates

---

# Week 3: Doctor Management & Treatment Services (✅ COMPLETED)

## 🎯 Sprint Goals

- Doctor management system
- Treatment services foundation
- Enhanced repository implementations

### Issue #BE007: Doctor Management System

**Assignee**: BE1  
**Priority**: High  
**Story Points**: 8

**Description:**
Implement comprehensive doctor management system với search và filtering.

**Acceptance Criteria:**

- [ ] Create IDoctorService và DoctorService:
  - GetAllDoctorsAsync(DoctorFilterDto) → PaginatedResultDto<DoctorResponseDto>
  - GetDoctorByIdAsync(int doctorId) → DoctorDetailDto
  - UpdateDoctorProfileAsync(int doctorId, UpdateDoctorDto)
  - UpdateAvailabilityAsync(int doctorId, bool isAvailable)
  - SearchDoctorsAsync(DoctorSearchDto) → List<DoctorResponseDto>
- [ ] Implement doctor search và filtering:
  - By specialization
  - By availability status
  - By rating/success rate
  - By years of experience
- [ ] Create DoctorsController:
  ```csharp
  GET /api/doctors - list với pagination và filtering
  GET /api/doctors/{id} - doctor details
  PUT /api/doctors/{id} - update profile (doctor/admin only)
  PUT /api/doctors/{id}/availability - toggle availability
  GET /api/doctors/search?specialization={spec}&available={bool}
  ```
- [ ] Add doctor-specific DTOs:
  - DoctorResponseDto, DoctorDetailDto, DoctorFilterDto
  - UpdateDoctorDto, DoctorSearchDto
- [ ] Implement doctor rating calculation system
- [ ] Add pagination support trong repository layer

**Definition of Done:**

- Doctor listing với search/filter working
- Doctor profiles can be updated
- Availability management functional
- Pagination working correctly

---

### Issue #BE008: Treatment Services Management

**Assignee**: BE2  
**Priority**: Medium  
**Story Points**: 6

**Description:**
Create treatment services và packages management system.

**Acceptance Criteria:**

- [ ] Create additional entities:
  ```csharp
  TreatmentService - Id, Name, Description, BasePrice, EstimatedDuration
  TreatmentPackage - ServiceId FK, PackageName, Price, IncludedServices
  ```
- [ ] Add entity configurations cho new entities
- [ ] Create migration for treatment entities:
  ```bash
  dotnet ef migrations add AddTreatmentServices --startup-project API
  ```
- [ ] Create ITreatmentService và implementation:
  - GetAllServicesAsync() → List<TreatmentServiceDto>
  - GetServiceByIdAsync(int serviceId) → TreatmentServiceDetailDto
  - GetPackagesByServiceAsync(int serviceId) → List<TreatmentPackageDto>
  - CreateServiceAsync(CreateServiceDto) - admin only
  - UpdateServiceAsync(int serviceId, UpdateServiceDto) - admin only
- [ ] Create TreatmentServicesController:
  ```csharp
  GET /api/treatment-services
  GET /api/treatment-services/{id}
  POST /api/treatment-services (Admin only)
  PUT /api/treatment-services/{id} (Admin only)
  GET /api/treatment-services/{id}/packages
  ```
- [ ] Add admin-only authorization cho management endpoints
- [ ] Create seed data cho common treatment services (IUI, IVF)

**Definition of Done:**

- Treatment services CRUD working
- Package management implemented
- Admin-only restrictions enforced
- Seed data includes common treatments

---

### Issue #BE009: Enhanced Repository Layer

**Assignee**: BE1  
**Priority**: Medium  
**Story Points**: 5

**Description:**
Enhance repository layer với advanced querying capabilities.

**Acceptance Criteria:**

- [ ] Enhance IBaseRepository:
  ```csharp
  Task<IEnumerable<T>> GetPagedAsync(int pageNumber, int pageSize);
  Task<IEnumerable<T>> FindWithIncludeAsync(Expression<Func<T, bool>> predicate, params Expression<Func<T, object>>[] includes);
  Task<T> GetByIdWithIncludeAsync(int id, params Expression<Func<T, object>>[] includes);
  ```
- [ ] Create specialized repository interfaces:

  ```csharp
  IUserRepository:
  - Task<User> FindByEmailAsync(string email);
  - Task<bool> EmailExistsAsync(string email);

  IDoctorRepository:
  - Task<IEnumerable<Doctor>> SearchAsync(DoctorSearchCriteria criteria);
  - Task<IEnumerable<Doctor>> GetAvailableDoctorsAsync();

  ICustomerRepository:
  - Task<Customer> GetWithMedicalHistoryAsync(int customerId);
  ```

- [ ] Implement advanced querying trong repositories
- [ ] Add caching considerations cho frequently accessed data
- [ ] Create database indexes cho performance:
  - User.Email (unique)
  - Doctor.Specialization, Doctor.IsAvailable
  - TreatmentCycle.CustomerId, TreatmentCycle.DoctorId

**Definition of Done:**

- Advanced repository methods implemented
- Specialized repositories working
- Database indexes created
- Performance considerations addressed

---

# Week 4: Treatment Cycle Management & Appointments (✅ COMPLETED)

## 🎯 Sprint Goals

- Treatment cycle entities và business logic
- Appointment scheduling system
- Advanced data relationships

### Issue #BE010: Treatment Cycle System

**Assignee**: BE2  
**Priority**: High  
**Story Points**: 10

**Description:**
Implement comprehensive treatment cycle management system.

**Acceptance Criteria:**

- [ ] Create TreatmentCycle entities migration:
  ```bash
  dotnet ef migrations add AddTreatmentCycles --startup-project API
  ```
- [ ] Implement ICycleService:
  - CreateCycleAsync(CreateCycleDto) → CycleResponseDto
  - GetCyclesByCustomerAsync(int customerId) → List<CycleResponseDto>
  - GetCycleByIdAsync(int cycleId) → CycleDetailDto
  - UpdateCycleStatusAsync(int cycleId, CycleStatus status)
  - AddPhaseAsync(int cycleId, CreatePhaseDto) → PhaseResponseDto
  - UpdatePhaseAsync(int phaseId, UpdatePhaseDto)
- [ ] Create TreatmentCyclesController:
  ```csharp
  POST /api/treatment-cycles
  GET /api/treatment-cycles (filtered by customer/doctor)
  GET /api/treatment-cycles/{id}
  PUT /api/treatment-cycles/{id}/status
  GET /api/treatment-cycles/{id}/phases
  POST /api/treatment-cycles/{id}/phases
  ```
- [ ] Implement cycle status transitions:
  - Registered → InProgress → Completed/Cancelled
- [ ] Add cost calculation logic
- [ ] Create cycle assignment logic (assign doctor)

**Definition of Done:**

- Treatment cycles can be created và managed
- Status transitions working với validation
- Phases can be added và tracked
- Cost calculations accurate

---

### Issue #BE011: Appointment Scheduling System

**Assignee**: BE1  
**Priority**: High  
**Story Points**: 8

**Description:**
Create appointment scheduling system với conflict detection.

**Acceptance Criteria:**

- [ ] Create Appointment entity migration:
  ```bash
  dotnet ef migrations add AddAppointments --startup-project API
  ```
- [ ] Implement IAppointmentService:
  - CreateAppointmentAsync(CreateAppointmentDto) → AppointmentResponseDto
  - GetAppointmentsByCustomerAsync(int customerId)
  - GetAppointmentsByDoctorAsync(int doctorId, DateTime date)
  - RescheduleAppointmentAsync(int appointmentId, DateTime newDateTime)
  - CancelAppointmentAsync(int appointmentId)
  - GetDoctorAvailabilityAsync(int doctorId, DateTime date)
- [ ] Create AppointmentsController:
  ```csharp
  POST /api/appointments
  GET /api/appointments (filtered by user role)
  GET /api/appointments/{id}
  PUT /api/appointments/{id}/reschedule
  PUT /api/appointments/{id}/cancel
  GET /api/doctors/{id}/availability
  ```
- [ ] Implement conflict detection:
  - Prevent double booking same doctor
  - Check doctor working hours
  - Validate appointment duration
- [ ] Add appointment status management:
  - Scheduled → Confirmed → Completed/Cancelled/NoShow

**Definition of Done:**

- Appointments can be scheduled successfully
- Conflict detection prevents double booking
- Doctor availability checking works
- Status transitions validated

---

### Issue #BE012: Basic Notification System

**Assignee**: BE2  
**Priority**: Medium  
**Story Points**: 6

**Description:**
Implement basic notification system cho appointments và treatment updates.

**Acceptance Criteria:**

- [ ] Create Notification entity migration:
  ```bash
  dotnet ef migrations add AddNotifications --startup-project API
  ```
- [ ] Create INotificationService:
  - CreateNotificationAsync(CreateNotificationDto)
  - GetUserNotificationsAsync(int userId) → List<NotificationDto>
  - MarkAsReadAsync(int notificationId)
  - DeleteNotificationAsync(int notificationId)
- [ ] Create NotificationsController:
  ```csharp
  GET /api/notifications
  PUT /api/notifications/{id}/mark-read
  DELETE /api/notifications/{id}
  ```
- [ ] Implement notification creation cho:
  - Appointment confirmations
  - Appointment reminders (24h before)
  - Treatment phase transitions
- [ ] Add basic email notification foundation:
  - IEmailService interface
  - Email template structure
  - SMTP configuration

**Definition of Done:**

- Notifications created automatically for events
- Users can view và manage notifications
- Email foundation ready for implementation

---

# Week 5: Test Results & Medication Management (✅ COMPLETED)

## 🎯 Sprint Goals

- Complete missing entities từ Week 4 (Appointments, TreatmentPhases)
- Implement core medical tracking (Test Results)
- Enhanced notification system với email integration
- Prepare foundation cho advanced features

### Issue #BE013: Complete Appointment System Implementation (✅ COMPLETED)

**Assignee**: BE1  
**Priority**: CRITICAL  
**Story Points**: 6

**Description:**
Complete the missing Appointment entity implementation và full integration với treatment cycles.

**Acceptance Criteria:**

- [ ] Uncomment và complete Appointment DbSet trong ApplicationDbContext:
  ```csharp
  public DbSet<Appointment> Appointments { get; set; }
  ```
- [ ] Create Appointment entity migration:
  ```bash
  dotnet ef migrations add AddAppointments --startup-project API
  ```
- [ ] Implement missing repositories:
  - IAppointmentRepository & AppointmentRepository
  - Add to UnitOfWork interface và implementation
- [ ] Complete IAppointmentService implementation:
  - CreateAppointmentAsync(CreateAppointmentDto) → AppointmentResponseDto
  - GetAppointmentsByCustomerAsync(int customerId)
  - GetAppointmentsByDoctorAsync(int doctorId, DateTime date)
  - GetDoctorAvailabilityAsync(int doctorId, DateTime date)
- [ ] Create AppointmentsController:
  ```csharp
  POST /api/appointments
  GET /api/appointments (filtered by user role)
  GET /api/appointments/{id}
  PUT /api/appointments/{id}/reschedule
  PUT /api/appointments/{id}/cancel
  ```
- [ ] Implement appointment-cycle integration
- [ ] Add conflict detection cho appointment scheduling

**Definition of Done:**

- Appointment entity fully implemented và working
- Full CRUD operations functional
- Integration với treatment cycles complete
- Conflict detection prevents double booking
- All missing repositories added to UnitOfWork

---

### Issue #BE014: Treatment Phases & Test Results Management (✅ COMPLETED)

**Assignee**: BE2  
**Priority**: HIGH  
**Story Points**: 7

**Description:**
Implement TreatmentPhase entity và core Test Results management system.

**Acceptance Criteria:**

- [ ] Uncomment và implement TreatmentPhase DbSet:
  ```csharp
  public DbSet<TreatmentPhase> TreatmentPhases { get; set; }
  ```
- [ ] Create TreatmentPhase migration:
  ```bash
  dotnet ef migrations add AddTreatmentPhases --startup-project API
  ```
- [ ] Implement TestResult entity và migration:
  ```bash
  dotnet ef migrations add AddTestResults --startup-project API
  ```
- [ ] Create missing repositories:
  - ITreatmentPhaseRepository & TreatmentPhaseRepository
  - ITestResultRepository & TestResultRepository
- [ ] Create ITestResultService:
  - CreateTestResultAsync(CreateTestResultDto) → TestResultDto
  - GetTestResultsByCycleAsync(int cycleId) → List<TestResultDto>
  - GetTestResultByIdAsync(int testResultId) → TestResultDetailDto
  - UpdateTestResultAsync(int testResultId, UpdateTestResultDto)
- [ ] Create TestResultsController:
  ```csharp
  POST /api/test-results (Doctor only)
  GET /api/test-results (filtered by cycle/type/date)
  GET /api/test-results/{id}
  PUT /api/test-results/{id} (Doctor only)
  GET /api/treatment-cycles/{id}/test-results
  ```
- [ ] Support basic test types:
  - BloodTest, Ultrasound, HormoneLevel, PregnancyTest
- [ ] Implement result interpretation với reference ranges:
  - Normal/Abnormal/RequiresAttention based on reference values
  - Automatic flagging of critical results
- [ ] Add TestResult-Appointment relationship:
  - Link test results to specific appointments
  - Track which appointment generated the test
- [ ] Implement validation logic:
  - Reference range validation
  - Test date validation (not future dates)
  - Required fields validation

**Definition of Done:**

- TreatmentPhase entity implemented
- Test results can be recorded và linked to cycles và appointments
- Basic test types supported với reference ranges
- Result interpretation working với proper validation
- Phase-cycle relationship functional
- Critical results flagged automatically

---

---

---

### Issue #BE016: System Integration & Foundation Completion (✅ COMPLETED)

**Assignee**: BE2  
**Priority**: MEDIUM  
**Story Points**: 4

**Description:**
Complete system integration và prepare foundation cho Week 6 advanced features.

**Acceptance Criteria:**

- [ ] Create Medication và Prescription entities (prepare for implementation):
  ```csharp
  // Có thể comment DbSet tạm thời
  public DbSet<Medication> Medications { get; set; }
  public DbSet<Prescription> Prescriptions { get; set; }
  ```
- [ ] Create entity configurations cho Medication & Prescription
- [ ] Complete UnitOfWork interface với ALL missing repositories:
  ```csharp
  IAppointmentRepository Appointments { get; }
  ITreatmentPhaseRepository TreatmentPhases { get; }
  ITestResultRepository TestResults { get; }
  INotificationRepository Notifications { get; }
  // Prepare for Week 6:
  // IMedicationRepository Medications { get; }
  // IPrescriptionRepository Prescriptions { get; }
  ```
- [ ] Complete service registrations trong DI container:
  - All missing services added to ServiceCollectionExtensions
  - Proper lifetime management (Scoped/Singleton)
  - Interface-implementation mapping complete
- [ ] Add critical database indexes cho performance:
  - Appointment.DoctorId, Appointment.ScheduledDateTime
  - TestResult.CycleId, TestResult.TestDate, TestResult.TestType
  - TreatmentPhase.CycleId, TreatmentPhase.PhaseOrder
  - Notification.UserId, Notification.IsRead
  - TreatmentCycle.Status, TreatmentCycle.StartDate
- [ ] Complete missing entity integrations:
  - TreatmentCycle ↔ TreatmentPhase proper relationship
  - Appointment ↔ TestResult linking
  - Notification auto-creation for major events
- [ ] Add comprehensive validation:
  - Cross-entity validation rules
  - Business rule enforcement
  - Data consistency checks
- [ ] Create missing DTOs:
  - Summary DTOs cho dashboard integration
  - Detail DTOs với full navigation properties
  - Filter DTOs cho advanced querying

**Definition of Done:**

- ALL missing repositories added to UnitOfWork
- Critical database indexes created
- ALL services properly registered
- Entity relationships và integrations complete
- Foundation fully ready cho Week 6 implementation
- Comprehensive validation in place
- System integration tests passing

---

### Issue #BE018: Review & Rating System (✅ COMPLETED)

**Assignee**: BE1  
**Priority**: MEDIUM  
**Story Points**: 6

**Description:**
Implement review và rating system cho doctors và services.

**Acceptance Criteria:**

- [ ] Create Review entity migration:
  ```bash
  dotnet ef migrations add AddReviews --startup-project API
  ```
- [ ] Create IReviewService:
  - CreateReviewAsync(CreateReviewDto) → ReviewDto
  - GetReviewsByDoctorAsync(int doctorId) → List<ReviewDto>
  - GetReviewStatisticsAsync(int doctorId) → ReviewStatisticsDto
  - ApproveReviewAsync(int reviewId) - admin only
- [ ] Create ReviewsController:
  ```csharp
  POST /api/reviews (Customer only - after completed cycle)
  GET /api/reviews (với filtering)
  PUT /api/reviews/{id}/approve (Admin only)
  GET /api/doctors/{id}/reviews
  GET /api/doctors/{id}/statistics
  ```
- [ ] Implement rating aggregation:
  - Average rating calculation
  - Update doctor success rate
  - Rating distribution analysis
- [ ] Add review validation:
  - Only customers with completed cycles can review
  - One review per cycle
- [ ] Basic moderation system:
  - Auto-approval for trusted customers
  - Admin moderation queue

**Definition of Done:**

- Customers can submit reviews after treatment
- Doctor ratings calculated automatically
- Admin can moderate reviews
- Review statistics displayed correctly

---

# Week 6 Foundation Fixes - Critical Issues

## 🚨 **URGENT: Pre-Week 6 Foundation Completion**

**Phát hiện:** Dự án BE chưa đủ điều kiện triển khai Week 6 do thiếu sót trong foundation layer.
**Timeline:** 1-2 ngày để fix trước khi start Week 6 tasks
**Priority:** CRITICAL - Blocking Week 6 implementation

---

## Issue #BE-FIX001: Complete UnitOfWork Repository Integration

**Assignee**: BE1  
**Priority**: CRITICAL  
**Story Points**: 4  
**Timeline**: 1 day

### **Description:**

UnitOfWork interface và implementation thiếu 5 repositories quan trọng, gây ra dependency injection failures và không thể access các entities key.

### **Current Problem:**

```csharp
// IUnitOfWork.cs - Missing repositories:
ITreatmentServiceRepository TreatmentServices { get; }  ❌
ITreatmentPackageRepository TreatmentPackages { get; }  ❌
ITreatmentPhaseRepository TreatmentPhases { get; }      ❌
ITestResultRepository TestResults { get; }             ❌
INotificationRepository Notifications { get; }         ❌
```

### **Acceptance Criteria:**

#### **1. Update IUnitOfWork Interface**

```csharp
// File: InfertilityTreatment.Data/Repositories/Interfaces/IUnitOfWork.cs
public interface IUnitOfWork : IDisposable
{
    // Existing repositories...
    IUserRepository Users { get; }
    ICustomerRepository Customers { get; }
    ITreatmentCycleRepository TreatmentCycles { get; }
    IRefreshTokenRepository RefreshTokens { get; }
    IAppointmentRepository Appointments { get; }
    IDoctorRepository Doctors { get; }
    IDoctorScheduleRepository DoctorSchedules { get; }
    IMedicationRepository Medications { get; }
    IPrescriptionRepository Prescriptions { get; }
    IReviewRepository Reviews { get; }

    // ADD MISSING REPOSITORIES:
    ITreatmentServiceRepository TreatmentServices { get; }
    ITreatmentPackageRepository TreatmentPackages { get; }
    ITreatmentPhaseRepository TreatmentPhases { get; }
    ITestResultRepository TestResults { get; }
    INotificationRepository Notifications { get; }

    // Transaction Methods
    Task<int> SaveChangesAsync();
    Task BeginTransactionAsync();
    Task CommitTransactionAsync();
    Task RollbackTransactionAsync();
}
```

#### **2. Update UnitOfWork Implementation**

```csharp
// File: InfertilityTreatment.Data/Repositories/Implementations/UnitOfWork.cs
public class UnitOfWork : IUnitOfWork
{
    // ADD missing private fields:
    private ITreatmentServiceRepository? _treatmentServices;
    private ITreatmentPackageRepository? _treatmentPackages;
    private ITreatmentPhaseRepository? _treatmentPhases;
    private ITestResultRepository? _testResults;
    private INotificationRepository? _notifications;

    // ADD missing lazy loading properties:
    public ITreatmentServiceRepository TreatmentServices =>
        _treatmentServices ??= new TreatmentServiceRepository(_context);

    public ITreatmentPackageRepository TreatmentPackages =>
        _treatmentPackages ??= new TreatmentPackageRepository(_context);

    public ITreatmentPhaseRepository TreatmentPhases =>
        _treatmentPhases ??= new TreatmentPhaseRepository(_context);

    public ITestResultRepository TestResults =>
        _testResults ??= new TestResultRepository(_context);

    public INotificationRepository Notifications =>
        _notifications ??= new NotificationRepository(_context);
}
```

#### **3. Verify All Repository Implementations Exist**

- [ ] Confirm TreatmentServiceRepository.cs exists và implements ITreatmentServiceRepository
- [ ] Confirm TreatmentPackageRepository.cs exists và implements ITreatmentPackageRepository
- [ ] Confirm TreatmentPhaseRepository.cs exists và implements ITreatmentPhaseRepository
- [ ] Confirm TestResultRepository.cs exists và implements ITestResultRepository
- [ ] Confirm NotificationRepository.cs exists và implements INotificationRepository

### **Definition of Done:**

- [ ] All missing repositories added to IUnitOfWork interface
- [ ] UnitOfWork implementation updated với lazy loading
- [ ] All repository implementations verified
- [ ] Build successful without errors
- [ ] Existing APIs still functional after changes

### **Testing:**

```bash
# Verify build success
dotnet build InfertilityTreatment.sln

# Test existing controllers still work
dotnet run --project InfertilityTreatment.API
# Access: https://localhost:7000/swagger
# Test: GET /api/test/protected endpoint
```

---

## Issue #BE-FIX002: Complete Service Registration & Dependency Injection

**Assignee**: BE2  
**Priority**: CRITICAL  
**Story Points**: 3  
**Timeline**: 1 day

### **Description:**

ServiceCollectionExtensions.cs thiếu service registrations cho several key services, gây ra runtime dependency injection errors.

### **Current Problem:**

```csharp
// Missing service registrations:
ITestResultService, TestResultService                   ❌
ITreatmentPhaseService, TreatmentPhaseService           ❌
INotificationService, NotificationService               ❌
```

### **Acceptance Criteria:**

#### **1. Update ServiceCollectionExtensions.cs**

```csharp
// File: InfertilityTreatment.API/Extensions/ServiceCollectionExtensions.cs
public static IServiceCollection AddBusinessServices(this IServiceCollection services)
{
    // ... existing registrations ...

    // ADD MISSING SERVICE REGISTRATIONS:
    services.AddScoped<ITestResultService, TestResultService>();
    services.AddScoped<ITreatmentPhaseService, TreatmentPhaseService>();
    services.AddScoped<INotificationService, NotificationService>();

    // Verify existing critical services are registered:
    services.AddScoped<IEmailService, EmailService>();
    services.AddScoped<ICycleService, CycleService>();

    return services;
}
```

#### **2. Create Missing Service Interfaces (if needed)**

```csharp
// Verify these interfaces exist:
// - ITestResultService ✅ (exists)
// - ITreatmentPhaseService ❌ (may need creation)
// - INotificationService ✅ (exists)
```

#### **3. Create ITreatmentPhaseService if missing**

```csharp
// File: InfertilityTreatment.Business/Interfaces/ITreatmentPhaseService.cs
public interface ITreatmentPhaseService
{
    Task<TreatmentPhaseDto> CreatePhaseAsync(CreateTreatmentPhaseDto dto);
    Task<List<TreatmentPhaseDto>> GetPhasesByCycleAsync(int cycleId);
    Task<TreatmentPhaseDto> GetPhaseByIdAsync(int phaseId);
    Task<TreatmentPhaseDto> UpdatePhaseAsync(int phaseId, UpdateTreatmentPhaseDto dto);
    Task<bool> DeletePhaseAsync(int phaseId);
}

// File: InfertilityTreatment.Business/Services/TreatmentPhaseService.cs
public class TreatmentPhaseService : ITreatmentPhaseService
{
    private readonly IUnitOfWork _unitOfWork;

    public TreatmentPhaseService(IUnitOfWork unitOfWork)
    {
        _unitOfWork = unitOfWork;
    }

    // Implement basic CRUD methods
}
```

#### **4. Verify All Services Have Proper Dependencies**

- [ ] Check all services can resolve their dependencies
- [ ] Verify circular dependency issues resolved
- [ ] Confirm service lifetimes appropriate (Scoped for business services)

### **Definition of Done:**

- [ ] All missing services registered in DI container
- [ ] ITreatmentPhaseService created if needed
- [ ] No dependency injection runtime errors
- [ ] All controllers can resolve their service dependencies
- [ ] Application starts successfully

### **Testing:**

```bash
# Test DI resolution
dotnet run --project InfertilityTreatment.API

# Verify no DI errors in startup logs
# Test controllers that use missing services:
# - TestResultController
# - TreatmentCyclesController (for phases)
# - NotificationsController
```

---

## Issue #BE-FIX003: Create Week 6 Foundation Services Structure

**Assignee**: BE1  
**Priority**: HIGH  
**Story Points**: 5  
**Timeline**: 1 day

### **Description:**

Tạo foundation structure cho Week 6 services để prepare cho implementation. Không cần full implementation, chỉ cần interfaces và basic structure.

### **Acceptance Criteria:**

#### **1. Create Analytics Service Foundation**

```csharp
// File: InfertilityTreatment.Business/Interfaces/IAnalyticsService.cs
public interface IAnalyticsService
{
    // Prepare for BE019: Analytics & Dashboard Foundation
    Task<DashboardStatsDto> GetDashboardStatsAsync(UserRole role, int userId, DateRangeDto dateRange);
    Task<List<TreatmentSuccessRateDto>> GetTreatmentSuccessRatesAsync();
    Task<RevenueReportDto> GetRevenueReportAsync(RevenueFilterDto filter);
    Task<DoctorPerformanceDto> GetDoctorPerformanceAsync(int doctorId);
}

// File: InfertilityTreatment.Business/Services/AnalyticsService.cs
public class AnalyticsService : IAnalyticsService
{
    private readonly IUnitOfWork _unitOfWork;

    public AnalyticsService(IUnitOfWork unitOfWork)
    {
        _unitOfWork = unitOfWork;
    }

    // Basic stub implementations
    public async Task<DashboardStatsDto> GetDashboardStatsAsync(UserRole role, int userId, DateRangeDto dateRange)
    {
        // TODO: Implement in BE019
        return new DashboardStatsDto();
    }

    // ... other stub methods
}
```

#### **2. Create Payment Service Foundation**

```csharp
// File: InfertilityTreatment.Business/Interfaces/IPaymentService.cs
public interface IPaymentService
{
    // Prepare for BE021: Payment Gateway Integration
    Task<PaymentResponseDto> CreateVNPayPaymentAsync(CreatePaymentDto dto);
    Task<PaymentResponseDto> CreateMomoPaymentAsync(CreatePaymentDto dto);
    Task<bool> HandleVNPayCallbackAsync(VNPayCallbackDto dto);
    Task<bool> HandleMomoCallbackAsync(MomoCallbackDto dto);
    Task<List<PaymentDto>> GetPaymentHistoryAsync(int customerId);
}

// File: InfertilityTreatment.Business/Services/PaymentService.cs
public class PaymentService : IPaymentService
{
    private readonly IUnitOfWork _unitOfWork;

    public PaymentService(IUnitOfWork unitOfWork)
    {
        _unitOfWork = unitOfWork;
    }

    // Stub implementations
    public async Task<PaymentResponseDto> CreateVNPayPaymentAsync(CreatePaymentDto dto)
    {
        // TODO: Implement in BE021
        throw new NotImplementedException("Will be implemented in BE021");
    }

    // ... other stub methods
}
```

#### **3. Create Booking Service Foundation**

```csharp
// File: InfertilityTreatment.Business/Interfaces/IBookingService.cs
public interface IBookingService
{
    // Prepare for BE023: Enhanced Booking System
    Task<BookingResponseDto> BookTreatmentCycleAsync(BookTreatmentDto dto);
    Task<List<TimeSlotDto>> GetAvailableSlotsAsync(AvailabilityFilterDto filter);
    Task<AppointmentDto> BookDoctorAppointmentAsync(BookAppointmentDto dto);
    Task<bool> RescheduleBookingAsync(int bookingId, RescheduleDto dto);
    Task<bool> CancelBookingAsync(int bookingId, CancellationDto dto);
}

// File: InfertilityTreatment.Business/Services/BookingService.cs
public class BookingService : IBookingService
{
    private readonly IUnitOfWork _unitOfWork;

    public BookingService(IUnitOfWork unitOfWork)
    {
        _unitOfWork = unitOfWork;
    }

    // Stub implementations for Week 6
}
```

#### **4. Create Basic DTOs Structure**

```csharp
// File: InfertilityTreatment.Entity/DTOs/Analytics/
// - DashboardStatsDto.cs
// - TreatmentSuccessRateDto.cs
// - RevenueReportDto.cs
// - DoctorPerformanceDto.cs

// File: InfertilityTreatment.Entity/DTOs/Payments/
// - CreatePaymentDto.cs
// - PaymentResponseDto.cs
// - PaymentDto.cs
// - VNPayCallbackDto.cs
// - MomoCallbackDto.cs

// File: InfertilityTreatment.Entity/DTOs/Bookings/
// - BookTreatmentDto.cs
// - BookingResponseDto.cs
// - AvailabilityFilterDto.cs
// - TimeSlotDto.cs
```

#### **5. Register Foundation Services**

```csharp
// Add to ServiceCollectionExtensions.cs:
services.AddScoped<IAnalyticsService, AnalyticsService>();
services.AddScoped<IPaymentService, PaymentService>();
services.AddScoped<IBookingService, BookingService>();
```

### **Definition of Done:**

- [ ] All foundation service interfaces created
- [ ] Basic service implementations with stubs
- [ ] DTOs structure created for Week 6 features
- [ ] Services registered in DI container
- [ ] Application compiles và runs successfully
- [ ] Foundation ready cho Week 6 feature implementation

---

## Issue #BE-FIX004: Database Optimization & Performance Indexes

**Assignee**: BE2  
**Priority**: MEDIUM  
**Story Points**: 3  
**Timeline**: 0.5 day

### **Description:**

Add critical database indexes để improve performance cho Week 6 analytics và payment features.

### **Acceptance Criteria:**

#### **1. Create Performance Migration**

```bash
# Create new migration
cd InfertilityTreatment.Data
dotnet ef migrations add AddPerformanceIndexes --startup-project ../InfertilityTreatment.API
```

#### **2. Add Critical Indexes**

```sql
-- File: Migration Up() method
migrationBuilder.Sql(@"
    -- User performance indexes
    CREATE INDEX IX_Users_Role ON Users(Role);
    CREATE INDEX IX_Users_Email ON Users(Email);

    -- Treatment cycle indexes for analytics
    CREATE INDEX IX_TreatmentCycles_Status_StartDate ON TreatmentCycles(Status, StartDate);
    CREATE INDEX IX_TreatmentCycles_CustomerId_Status ON TreatmentCycles(CustomerId, Status);
    CREATE INDEX IX_TreatmentCycles_DoctorId_Status ON TreatmentCycles(DoctorId, Status);

    -- Appointment indexes for booking
    CREATE INDEX IX_Appointments_DoctorId_ScheduledDateTime ON Appointments(DoctorId, ScheduledDateTime);
    CREATE INDEX IX_Appointments_Status_ScheduledDateTime ON Appointments(Status, ScheduledDateTime);

    -- Test result indexes for analytics
    CREATE INDEX IX_TestResults_CycleId_TestDate ON TestResults(CycleId, TestDate);
    CREATE INDEX IX_TestResults_TestType_Status ON TestResults(TestType, Status);

    -- Review indexes for doctor statistics
    CREATE INDEX IX_Reviews_DoctorId_IsApproved ON Reviews(DoctorId, IsApproved);
    CREATE INDEX IX_Reviews_Rating_CreatedAt ON Reviews(Rating, CreatedAt);

    -- Notification indexes
    CREATE INDEX IX_Notifications_UserId_IsRead ON Notifications(UserId, IsRead);
    CREATE INDEX IX_Notifications_Type_ScheduledAt ON Notifications(Type, ScheduledAt);
");
```

#### **3. Apply Migration**

```bash
dotnet ef database update --startup-project ../InfertilityTreatment.API
```

### **Definition of Done:**

- [ ] Performance indexes migration created
- [ ] Migration applied successfully
- [ ] Database performance improved cho common queries
- [ ] Ready cho Week 6 analytics queries

---

## Issue #BE-FIX005: Integration Testing & Verification

**Assignee**: BE1 + BE2  
**Priority**: MEDIUM  
**Story Points**: 2  
**Timeline**: 0.5 day

### **Description:**

Comprehensive testing để verify all fixes working và system ready cho Week 6.

### **Acceptance Criteria:**

#### **1. API Integration Testing**

```bash
# Test all controllers working
GET /api/users/profile
GET /api/doctors
GET /api/treatment-cycles
GET /api/appointments
GET /api/test-results
GET /api/medications
GET /api/prescriptions
GET /api/reviews
GET /api/notifications
```

#### **2. Service Dependency Testing**

```csharp
// Verify all services can be resolved:
var services = serviceProvider.GetServices<IAnalyticsService>();
var services = serviceProvider.GetServices<IPaymentService>();
var services = serviceProvider.GetServices<IBookingService>();
// All should resolve without errors
```

#### **3. UnitOfWork Testing**

```csharp
// Test all repositories accessible:
using var unitOfWork = serviceProvider.GetService<IUnitOfWork>();
var users = await unitOfWork.Users.GetAllAsync();
var treatments = await unitOfWork.TreatmentServices.GetAllAsync();
var phases = await unitOfWork.TreatmentPhases.GetAllAsync();
var testResults = await unitOfWork.TestResults.GetAllAsync();
var notifications = await unitOfWork.Notifications.GetAllAsync();
```

#### **4. Database Performance Testing**

```sql
-- Test new indexes working:
EXPLAIN QUERY PLAN
SELECT * FROM TreatmentCycles
WHERE Status = 'InProgress' AND StartDate > '2024-01-01';
-- Should use IX_TreatmentCycles_Status_StartDate
```

### **Definition of Done:**

- [ ] All APIs responding correctly
- [ ] No dependency injection errors
- [ ] All repositories accessible through UnitOfWork
- [ ] Database indexes improving query performance
- [ ] System stable và ready cho Week 6 implementation

---

## 🎯 **SUCCESS CRITERIA:**

**After completing all fixes:**

1. ✅ Application builds và runs without errors
2. ✅ All Swagger endpoints accessible
3. ✅ No dependency injection runtime errors
4. ✅ Database queries optimized với indexes
5. ✅ Foundation services ready cho Week 6 features
6. ✅ System assessment rating: **8.5/10** (Ready for Week 6)

---

# Week 6: Payment Integration & Booking System Enhancement

## 🎯 Sprint Goals

- Complete payment gateway integration (VNPay & Momo)
- Implement comprehensive booking system
- Add invoice management system
- Finalize missing Week 5 components

### Issue #BE019: Analytics & Dashboard Foundation (⚠️ IN PROGRESS)

**Assignee**: BE1  
**Priority**: HIGH  
**Story Points**: 8

**Description:**
Complete advanced analytics system với comprehensive dashboard data cho all user roles.

**Acceptance Criteria:**

- [ ] Create AnalyticsController:
  ```csharp
  GET /api/analytics/dashboard/{role}?startDate=&endDate=
  GET /api/analytics/treatment-success-rates?serviceId=&doctorId=
  GET /api/analytics/revenue-reports?startDate=&endDate=&serviceId=
  GET /api/analytics/doctor-performance/{doctorId}
  GET /api/analytics/patient-demographics
  POST /api/analytics/export-report
  ```
- [ ] Implement IAnalyticsService:
  - GetDashboardStatsAsync(UserRole role, int userId, DateRangeDto dateRange)
  - GetTreatmentSuccessRatesAsync(SuccessRateFilterDto filter)
  - GetRevenueReportAsync(RevenueReportDto filter)
  - GetDoctorPerformanceAsync(int doctorId, PerformanceFilterDto filter)
  - ExportReportToPdfAsync/ExportReportToExcelAsync
- [ ] Create database views cho performance:
  ```sql
  vw_TreatmentSuccessRates, vw_RevenueAnalytics, vw_DoctorPerformance
  ```
- [ ] Add export functionality (PDF/Excel) using libraries
- [ ] Implement role-based analytics:
  - Customer: Personal treatment progress, appointment history
  - Doctor: Patient statistics, success rates, appointment load
  - Manager/Admin: System-wide analytics, revenue reports

**Definition of Done:**

- Dashboard APIs returning proper data cho all roles
- Export functionality working (PDF/Excel)
- Database views created cho performance
- Analytics data accurate và consistent

---

### Issue #BE020: System Integration & Optimization (⚠️ IN PROGRESS)

**Assignee**: BE2  
**Priority**: HIGH  
**Story Points**: 6

**Description:**
Complete system integration, optimize performance, và finalize missing components.

**Acceptance Criteria:**

- [ ] Complete missing entity relationships:
  - TreatmentCycle ↔ Payment integration
  - Appointment ↔ Invoice linking
  - User ↔ Payment history tracking
- [ ] Add comprehensive database indexes:
  ```sql
  -- Performance indexes
  CREATE INDEX IX_Payments_CustomerId ON Payments(CustomerId);
  CREATE INDEX IX_Payments_Status ON Payments(Status);
  CREATE INDEX IX_TreatmentCycles_Status_StartDate ON TreatmentCycles(Status, StartDate);
  CREATE INDEX IX_Appointments_DoctorId_ScheduledDateTime ON Appointments(DoctorId, ScheduledDateTime);
  ```
- [ ] Implement caching strategy:
  - Doctor availability caching
  - Treatment service/package caching
  - Analytics data caching
- [ ] Add comprehensive logging:
  - Payment transaction logging
  - Booking process logging
  - Error tracking và monitoring
- [ ] Create health check endpoints:
  - Database connectivity
  - External service availability
  - System resource monitoring
- [ ] Optimize query performance:
  - Reduce N+1 queries
  - Implement lazy loading strategically
  - Add query optimization

**Definition of Done:**

- All entity relationships complete
- Performance optimized với caching
- Comprehensive logging implemented
- Health checks functional
- System ready for production load

---

### Issue #BE021: Payment Gateway Integration (VNPay & Momo)

**Assignee**: BE1  
**Priority**: CRITICAL  
**Story Points**: 10

**Description:**
Implement comprehensive payment gateway integration với VNPay và Momo cho treatment package payments.

**Acceptance Criteria:**

- [ ] Create Payment entity migration:
  ```bash
  dotnet ef migrations add AddPayments --startup-project API
  ```
- [ ] Create Payment entity:
  ```csharp
  Payment:
  - Id, CustomerId FK, TreatmentCycleId FK
  - PaymentMethod (VNPay/Momo/Cash), Amount, Currency
  - Status (Pending/Completed/Failed/Refunded)
  - TransactionId, PaymentGatewayResponse (JSON)
  - CreatedAt, CompletedAt
  ```
- [ ] Create PaymentLogs entity cho audit trail
- [ ] Implement IPaymentService:
  - CreateVNPayPaymentAsync(CreatePaymentDto) → PaymentResponseDto
  - CreateMomoPaymentAsync(CreatePaymentDto) → PaymentResponseDto
  - HandleVNPayCallbackAsync(VNPayCallbackDto) → bool
  - HandleMomoCallbackAsync(MomoCallbackDto) → bool
  - GetPaymentHistoryAsync(int customerId) → PaginatedResultDto<PaymentDto>
  - ProcessRefundAsync(RefundRequestDto) → RefundResponseDto
  - GetPaymentStatusAsync(int paymentId) → PaymentStatusDto
- [ ] Create PaymentController:
  ```csharp
  POST /api/payments/create
  POST /api/payments/vnpay/callback
  POST /api/payments/momo/callback
  GET /api/payments/history/{customerId}
  GET /api/payments/status/{paymentId}
  POST /api/payments/refund (Admin only)
  ```
- [ ] Implement VNPay integration:
  - Payment URL generation với proper hashing
  - Callback verification với signature validation
  - IPN (Instant Payment Notification) handling
  - Return URL processing
- [ ] Implement Momo integration:
  - Payment request với proper authentication
  - Webhook callback handling
  - Payment status verification
  - Error handling và retry logic
- [ ] Add payment security measures:
  - Request signing và verification
  - Callback URL validation
  - Amount tampering prevention
  - Duplicate transaction prevention
- [ ] Create payment configuration trong appsettings:
  ```json
  "PaymentGateways": {
    "VNPay": { "TmnCode", "HashSecret", "PaymentUrl", "CallbackUrl" },
    "Momo": { "PartnerCode", "AccessKey", "SecretKey", "Endpoint" }
  }
  ```

**Definition of Done:**

- VNPay integration working với sandbox environment
- Momo integration functional với test credentials
- Payment callbacks handled securely
- Payment history tracking accurate
- Refund processing implemented
- Security measures in place

---

### Issue #BE022: Invoice Management & Generation System

**Assignee**: BE2  
**Priority**: HIGH  
**Story Points**: 8

**Description:**
Create comprehensive invoice system với PDF generation và email delivery.

**Acceptance Criteria:**

- [ ] Create Invoice entities migration:
  ```bash
  dotnet ef migrations add AddInvoices --startup-project API
  ```
- [ ] Create Invoice entity:
  ```csharp
  Invoice:
  - Id, InvoiceNumber (auto-generated), CustomerId FK
  - PaymentId FK, TreatmentCycleId FK
  - SubtotalAmount, TaxAmount, TotalAmount, Currency
  - Status (Draft/Sent/Paid/Cancelled)
  - IssueDate, DueDate, PaidDate, Notes
  ```
- [ ] Create InvoiceItems entity:
  ```csharp
  InvoiceItem:
  - Id, InvoiceId FK, Description
  - Quantity, UnitPrice, TotalPrice
  ```
- [ ] Implement IInvoiceService:
  - GenerateInvoiceAsync(GenerateInvoiceDto) → InvoiceDto
  - GenerateInvoicePdfAsync(int invoiceId) → byte[]
  - SendInvoiceEmailAsync(int invoiceId) → bool
  - GetCustomerInvoicesAsync(int customerId) → PaginatedResultDto<InvoiceDto>
  - GetInvoiceByIdAsync(int invoiceId) → InvoiceDetailDto
  - UpdateInvoiceStatusAsync(int invoiceId, InvoiceStatus status)
- [ ] Create InvoiceController:
  ```csharp
  POST /api/invoices/generate
  GET /api/invoices/{id}
  GET /api/invoices/{id}/pdf
  POST /api/invoices/{id}/send-email
  GET /api/invoices/customer/{customerId}
  PUT /api/invoices/{id}/status
  ```
- [ ] Implement PDF generation:
  - Install iTextSharp hoặc equivalent library
  - Create professional invoice template
  - Include company branding và details
  - Support Vietnamese currency formatting
- [ ] Implement auto invoice number generation:
  - Format: INV-YYYY-MM-NNNN (e.g., INV-2025-01-0001)
  - Ensure unique numbering
  - Handle concurrent generation
- [ ] Add email delivery integration:
  - HTML email template với invoice attachment
  - Email delivery status tracking
  - Retry mechanism cho failed deliveries
- [ ] Create invoice business rules:
  - Auto-generate invoice on payment completion
  - Update invoice status on payment status changes
  - Support partial payments và adjustments

**Definition of Done:**

- Invoice generation working automatically
- PDF generation với professional template
- Email delivery functional
- Invoice numbering system robust
- Customer can view và download invoices

---

### Issue #BE023: Enhanced Booking & Scheduling System

**Assignee**: BE1  
**Priority**: CRITICAL  
**Story Points**: 9

**Description:**
Complete booking system với comprehensive scheduling, availability management, và conflict resolution.

**Acceptance Criteria:**

- [ ] Create additional booking entities:

  ```csharp
  DoctorSchedule:
  - Id, DoctorId FK, DayOfWeek, StartTime, EndTime
  - IsAvailable, EffectiveDate, ExpiryDate

  BookingSlot:
  - Id, DoctorId FK, SlotDateTime, Duration
  - IsBooked, AppointmentId FK, CreatedAt
  ```

- [ ] Create migration cho new booking entities:
  ```bash
  dotnet ef migrations add EnhancedBookingSystem --startup-project API
  ```
- [ ] Implement IBookingService:
  - BookTreatmentCycleAsync(BookTreatmentDto) → BookingResponseDto
  - GetAvailableSlotsAsync(AvailabilityFilterDto) → List<TimeSlotDto>
  - BookDoctorAppointmentAsync(BookAppointmentDto) → AppointmentDto
  - RescheduleBookingAsync(int bookingId, RescheduleDto) → bool
  - CancelBookingAsync(int bookingId, CancellationDto) → bool
  - GetCustomerBookingsAsync(int customerId) → List<BookingDto>
  - CheckSlotAvailabilityAsync(int doctorId, DateTime slotTime) → bool
- [ ] Create BookingController:
  ```csharp
  POST /api/bookings/treatment-cycle
  GET /api/bookings/available-slots?doctorId=&date=&serviceType=
  POST /api/bookings/appointment
  PUT /api/bookings/{id}/reschedule
  DELETE /api/bookings/{id}/cancel
  GET /api/bookings/customer/{customerId}
  GET /api/bookings/doctor/{doctorId}
  ```
- [ ] Implement doctor availability system:
  - Weekly schedule management
  - Holiday và exception handling
  - Real-time availability updates
  - Block booking functionality cho doctors
- [ ] Add comprehensive conflict detection:
  - Prevent double booking same time slot
  - Check doctor working hours
  - Validate appointment duration
  - Handle timezone considerations
- [ ] Implement booking workflow:
  - Treatment package selection → Doctor selection → Time slot selection → Payment → Confirmation
  - Integration với payment system
  - Automatic appointment creation
  - Email confirmations
- [ ] Add booking business rules:
  - Minimum advance booking time (e.g., 24 hours)
  - Maximum booking window (e.g., 3 months ahead)
  - Cancellation policies và deadlines
  - Rescheduling limitations
- [ ] Create notification integration:
  - Booking confirmations
  - Reminder notifications
  - Cancellation notifications
  - Rescheduling confirmations

**Definition of Done:**

- Complete booking workflow functional
- Doctor availability system working
- Conflict detection prevents double booking
- Integration với payment và notification systems
- Booking rules enforced properly

---

### Issue #BE024: Complete Medication & Prescription System

**Assignee**: BE2  
**Priority**: MEDIUM  
**Story Points**: 7

**Description:**
Finalize medication management system với prescription tracking và monitoring.

**Acceptance Criteria:**

- [ ] Uncomment và implement Medication & Prescription entities:
  ```csharp
  public DbSet<Medication> Medications { get; set; }
  public DbSet<Prescription> Prescriptions { get; set; }
  ```
- [ ] Create medications migration:
  ```bash
  dotnet ef migrations add AddMedicationsAndPrescriptions --startup-project API
  ```
- [ ] Implement IMedicationService:
  - GetAllMedicationsAsync() → List<MedicationDto>
  - CreateMedicationAsync(CreateMedicationDto) - admin only
  - UpdateMedicationAsync(int medicationId, UpdateMedicationDto)
  - SearchMedicationsAsync(string searchTerm) → List<MedicationDto>
- [ ] Implement IPrescriptionService:
  - CreatePrescriptionAsync(CreatePrescriptionDto) → PrescriptionDto
  - GetPrescriptionsByPhaseAsync(int phaseId) → List<PrescriptionDto>
  - GetPatientPrescriptionsAsync(int customerId) → List<PrescriptionDto>
  - UpdatePrescriptionAsync(int prescriptionId, UpdatePrescriptionDto)
  - MarkPrescriptionCompleteAsync(int prescriptionId)
- [ ] Create controllers:

  ```csharp
  MedicationsController:
  - GET /api/medications
  - POST /api/medications (Admin only)
  - PUT /api/medications/{id}
  - GET /api/medications/search?term={searchTerm}

  PrescriptionsController:
  - POST /api/prescriptions (Doctor only)
  - GET /api/prescriptions/phase/{phaseId}
  - GET /api/prescriptions/customer/{customerId}
  - PUT /api/prescriptions/{id}
  - PUT /api/prescriptions/{id}/complete
  ```

- [ ] Add prescription validation:
  - Doctor can only prescribe for their patients
  - Medication dosage validation
  - Drug interaction checking (basic)
  - Prescription duration validation
- [ ] Implement medication tracking:
  - Prescription adherence monitoring
  - Side effects reporting
  - Medication schedule notifications
- [ ] Add medication database với common fertility drugs:
  - Clomid, Letrozole, Gonal-F, Menopur, etc.
  - Standard dosages và administration routes
  - Common side effects và contraindications

**Definition of Done:**

- Medication database populated với common drugs
- Doctors can prescribe medications trong treatment phases
- Patients can view their prescriptions
- Basic validation và safety checks implemented

---

### Issue #BE025: Enhanced Email & Notification Integration

**Assignee**: BE1  
**Priority**: MEDIUM  
**Story Points**: 6

**Description:**
Complete email system integration với automated notifications và templating.

**Acceptance Criteria:**

- [ ] Implement IEmailService completely:
  - SendAppointmentReminderAsync(int appointmentId)
  - SendTestResultNotificationAsync(int customerId, int testResultId)
  - SendMedicationReminderAsync(int customerId, List<int> prescriptionIds)
  - SendTreatmentMilestoneAsync(int cycleId, string milestone)
  - SendInvoiceEmailAsync(int invoiceId)
  - SendBookingConfirmationAsync(int bookingId)
- [ ] Create email templates:
  - HTML templates cho each notification type
  - Professional styling với company branding
  - Support cho Vietnamese language
  - Responsive design cho mobile viewing
- [ ] Implement template engine:
  - Dynamic content insertion
  - Personalization với customer data
  - Conditional content based on treatment type
- [ ] Add SMTP configuration enhancement:
  - Support cho multiple email providers
  - Connection pooling cho performance
  - Retry logic cho failed sends
  - Email delivery status tracking
- [ ] Create notification scheduling:
  - Background service cho scheduled notifications
  - Appointment reminders (24h, 2h before)
  - Medication reminders (daily schedule)
  - Treatment milestone notifications
- [ ] Add notification preferences:
  - User can opt-in/opt-out của specific notifications
  - Email vs SMS preferences
  - Notification frequency settings
- [ ] Implement email analytics:
  - Delivery rates tracking
  - Open rates monitoring
  - Click-through tracking for links
  - Bounce handling

**Definition of Done:**

- Automated email notifications working
- Professional email templates implemented
- Notification scheduling functional
- User preferences respected
- Email analytics tracking

---

## 📋 **Week 6 Summary & Integration**

### **New Database Tables:**

- Payments, PaymentLogs
- Invoices, InvoiceItems
- DoctorSchedule, BookingSlot
- Medications, Prescriptions (if not already created)

### **Major API Endpoints Added:**

- `/api/payments/*` - Payment processing
- `/api/invoices/*` - Invoice management
- `/api/bookings/*` - Enhanced booking system
- `/api/medications/*` - Medication management
- `/api/prescriptions/*` - Prescription management

### **External Integrations:**

- VNPay payment gateway
- Momo payment gateway
- Enhanced email service với templates
- PDF generation library

## 📋 **Week 6 Priority Order**

### **TUẦN NÀY CẦN HOÀN THÀNH:**

#### **Priority 1: Complete Pending Issues (Days 1-2)**

1. **#BE019: Analytics & Dashboard Foundation** (⚠️ PENDING - 8 pts)
2. **#BE020: System Integration & Optimization** (⚠️ PENDING - 6 pts)

#### **Priority 2: New Critical Features (Days 3-5)**

3. **#BE021: Payment Gateway Integration** (NEW - 10 pts)
4. **#BE023: Enhanced Booking System** (NEW - 9 pts)

#### **Priority 3: Supporting Features (Days 6-7)**

5. **#BE022: Invoice Management** (NEW - 8 pts)
6. **#BE024: Medication System** (NEW - 7 pts)

### **⚠️ KHUYẾN NGHỊ:**

**ĐẦU TIÊN:** Close #BE019 và #BE020 trước khi start issues mới  
**LÝ DO:** Payment system cần Analytics data và System Integration hoàn chỉnh

### **Week 6 Goals Achievement:**

- ✅ Complete payment processing end-to-end
- ✅ Professional invoice system
- ✅ Comprehensive booking workflow
- ✅ Complete medication management
- ✅ Enhanced notification system

### **Preparation for Week 7:**

- System optimization và performance tuning
- Advanced reporting và analytics
- Mobile API enhancements
- Production deployment preparation
