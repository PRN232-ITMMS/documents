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

# Week 5: Test Results & Medication Management (🚧 IN PROGRESS)

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

### Issue #BE015: Enhanced Email & Notification Integration (🚧 IN PROGRESS)

**Assignee**: BE1  
**Priority**: MEDIUM  
**Story Points**: 5

**Description:**
Complete notification system với email integration và automatic triggers.

**Acceptance Criteria:**

- [ ] Complete IEmailService implementation:
  - SendEmailAsync(EmailDto emailDto)
  - SendTemplateEmailAsync(string template, object data, string toEmail)
- [ ] Create essential email templates:
  - Appointment confirmation
  - Appointment reminder (24h before)
  - Test results available notification
  - Critical test result alert
- [ ] Implement automatic notification triggers:
  - When appointment is created/confirmed
  - When test results are added
  - When treatment cycle status changes
  - When critical test results are flagged
- [ ] Add EmailService registration trong DI container
- [ ] Create email configuration:
  - SMTP settings trong appsettings
  - Email template structure
- [ ] Update NotificationService với email integration:
  - Auto-send emails for critical notifications
  - Link notifications với related entities
  - Email delivery status tracking
- [ ] Add basic email queue mechanism:
  - Simple in-memory queue for email sending
  - Background service for processing emails
  - Error handling và retry logic
- [ ] Implement email preferences:
  - User can enable/disable email notifications
  - Different notification types configuration

**Definition of Done:**

- Email service functional với SMTP
- Essential email templates working
- Automatic notifications triggered by system events
- Email-notification integration complete
- Basic email queue working
- Email configuration ready for production
- User email preferences functional

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

# Week 6: Medication Management & Advanced Features

## 🎯 Sprint Goals

- Complete medication và prescription system
- Implement review và rating system
- Advanced analytics foundation
- System integration và optimization

### Issue #BE017: Complete Medication & Prescription System (🚧 IN PROGRESS)

**Assignee**: BE2  
**Priority**: HIGH  
**Story Points**: 8

**Description:**
Implement full medication management và prescription tracking system.

**Acceptance Criteria:**

- [ ] Uncomment và implement Medication/Prescription DbSets:
  ```csharp
  public DbSet<Medication> Medications { get; set; }
  public DbSet<Prescription> Prescriptions { get; set; }
  ```
- [ ] Create migration cho medication entities:
  ```bash
  dotnet ef migrations add AddMedicationsAndPrescriptions --startup-project API
  ```
- [ ] Implement missing repositories:
  - IMedicationRepository & MedicationRepository
  - IPrescriptionRepository & PrescriptionRepository
  - Add to UnitOfWork
- [ ] Create IMedicationService:
  - GetAllMedicationsAsync() → List<MedicationDto>
  - GetMedicationByIdAsync(int id) → MedicationDetailDto
  - CreateMedicationAsync(CreateMedicationDto) - admin only
  - UpdateMedicationAsync(int medicationId, UpdateMedicationDto)
  - SearchMedicationsAsync(string searchTerm) → List<MedicationDto>
- [ ] Create IPrescriptionService:
  - CreatePrescriptionAsync(CreatePrescriptionDto) - doctor only
  - GetPrescriptionsByPatientAsync(int customerId) → List<PrescriptionDto>
  - GetPrescriptionsByDoctorAsync(int doctorId) → List<PrescriptionDto>
  - UpdatePrescriptionAsync(int prescriptionId, UpdatePrescriptionDto)
  - DiscontinuePrescriptionAsync(int prescriptionId)
  - GetActivePrescriptionsAsync(int customerId) → List<PrescriptionDto>
- [ ] Create controllers:

  ```csharp
  MedicationsController:
  - GET /api/medications
  - POST /api/medications (Admin only)

  PrescriptionsController:
  - POST /api/prescriptions (Doctor only)
  - GET /api/prescriptions (filtered by patient/doctor)
  - PUT /api/prescriptions/{id}
  - GET /api/customers/{id}/prescriptions
  ```

- [ ] Implement prescription-phase integration:
  - Link prescriptions to treatment phases
  - Automatic prescription suggestions based on phase
- [ ] Add comprehensive safety validations:
  - Dosage validation
  - Drug interaction basic checking (simplified)
  - Allergy checking với patient medical history
  - Prescription date validations
- [ ] Implement prescription status tracking:
  - Active/Completed/Discontinued/Expired
  - Automatic status updates
- [ ] Add prescription notifications:
  - New prescription notification
  - Medication reminder notifications
  - Prescription expiry warnings

**Definition of Done:**

- Medication CRUD fully functional với search
- Prescription system integrated với treatment phases
- Doctor can prescribe medications với safety checks
- Patient can view prescription history và active medications
- Comprehensive safety validations implemented
- Prescription status tracking working
- Automatic notifications for prescriptions

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

### Issue #BE019: Analytics & Dashboard Foundation (🚧 IN PROGRESS)

**Assignee**: BE2  
**Priority**: MEDIUM  
**Story Points**: 5

**Description:**
Create analytics foundation và basic dashboard APIs.

**Acceptance Criteria:**

- [ ] Create IAnalyticsService:
  - GetTreatmentSuccessRatesAsync() → TreatmentAnalyticsDto
  - GetDoctorPerformanceAsync(int doctorId) → DoctorPerformanceDto
  - GetPatientSatisfactionAsync() → SatisfactionMetricsDto
  - GetSystemOverviewAsync() → SystemOverviewDto
- [ ] Create AnalyticsController:
  ```csharp
  GET /api/analytics/success-rates
  GET /api/analytics/doctor-performance/{id}
  GET /api/analytics/patient-satisfaction
  GET /api/analytics/overview
  ```
- [ ] Implement basic dashboard data:
  - Customer dashboard: current cycle, appointments, test results
  - Doctor dashboard: today's appointments, patient count
  - Manager dashboard: system statistics, success rates
- [ ] Create IDashboardService với basic implementations
- [ ] Add caching cho analytics data:
  - Cache success rate calculations
  - Refresh cache when data changes

**Definition of Done:**

- Basic analytics APIs functional
- Dashboard data accurate
- Caching improving performance
- Role-based data access working

---

### Issue #BE020: System Integration & Optimization (🚧 IN PROGRESS)

**Assignee**: BE1  
**Priority**: MEDIUM  
**Story Points**: 4

**Description:**
Complete system integration và basic optimizations.

**Acceptance Criteria:**

- [ ] Complete all missing service integrations:
  - TreatmentCycle ↔ Appointments
  - Appointments ↔ TestResults
  - TreatmentPhases ↔ Prescriptions
  - Notifications ↔ All major events
- [ ] Implement comprehensive event system:
  - Appointment created → send notification
  - Test result added → notify patient
  - Prescription added → notify patient
  - Cycle completed → enable reviews
- [ ] Add validation improvements:
  - Cross-entity validation
  - Business rule enforcement
  - Data consistency checks
- [ ] Basic performance optimizations:
  - Add database indexes cho frequently queried fields
  - Optimize includes trong repositories
  - Add pagination cho large datasets
- [ ] Create comprehensive DTOs:
  - Dashboard DTOs với aggregated data
  - Detail DTOs với full relationships
  - Summary DTOs cho lists

**Definition of Done:**

- All major entities properly integrated
- Automatic notifications working
- Performance optimized cho common operations
- Data validation comprehensive
- Business rules enforced

---

# Week 7: Dashboard APIs & Production Ready

## 🎯 Sprint Goals

- Complete dashboard APIs cho all roles
- Performance optimization
- Production deployment preparation

### Issue #BE019: Role-Based Dashboard APIs

**Assignee**: BE1  
**Priority**: High  
**Story Points**: 8

**Description:**
Create comprehensive dashboard APIs cho all user roles.

**Acceptance Criteria:**

- [ ] Create IDashboardService với role-based methods:
  - GetCustomerDashboardAsync(int customerId) → CustomerDashboardDto
  - GetDoctorDashboardAsync(int doctorId) → DoctorDashboardDto
  - GetManagerDashboardAsync() → ManagerDashboardDto
- [ ] Implement customer dashboard data:
  - Current treatment cycle status
  - Upcoming appointments (next 7 days)
  - Recent test results summary
  - Today's medication schedule
  - Progress tracking metrics
- [ ] Implement doctor dashboard data:
  - Today's appointment list
  - Patient caseload summary
  - Performance metrics
  - Pending tasks (test results to review)
- [ ] Implement manager dashboard data:
  - Success rates by treatment type
  - Revenue metrics
  - Doctor performance comparison
  - Patient satisfaction scores
- [ ] Create DashboardController:
  ```csharp
  GET /api/dashboard/customer
  GET /api/dashboard/doctor
  GET /api/dashboard/manager
  ```
- [ ] Add data aggregation optimizations:
  - Efficient queries cho dashboard data
  - Caching cho frequently accessed metrics

**Definition of Done:**

- Role-based dashboards functional và informative
- Dashboard data accurate và up-to-date
- Performance optimized cho fast loading
- All user roles have appropriate dashboard

---

### Issue #BE020: Performance Optimization & Caching

**Assignee**: BE2  
**Priority**: High  
**Story Points**: 6

**Description:**
Optimize backend performance và implement comprehensive caching strategy.

**Acceptance Criteria:**

- [ ] Implement database query optimizations:
  - Add proper indexing cho all foreign keys
  - Optimize Entity Framework Include statements
  - Remove N+1 query problems
  - Add database query logging
- [ ] Add response caching:
  - Cache frequently accessed data (doctors list, treatment services)
  - Implement cache invalidation strategies
  - Add cache expiration policies
- [ ] Implement pagination optimizations:
  - Optimize large dataset queries
  - Add count optimization cho paginated results
- [ ] Add API performance monitoring:
  - Response time logging
  - Slow query detection
  - Performance metrics collection
- [ ] Optimize authentication:
  - JWT token validation optimization
  - Refresh token cleanup job
- [ ] Add health checks:
  - Database connectivity check
  - External service availability check

**Definition of Done:**

- API response times under 500ms for standard operations
- Database queries optimized với proper indexing
- Caching reducing database load
- Health checks operational

---

### Issue #BE021: Production Deployment & Final Testing

**Assignee**: BE1 & BE2  
**Priority**: High  
**Story Points**: 7

**Description:**
Final production preparation, security hardening, và comprehensive testing.

**Acceptance Criteria:**

- [ ] Complete security audit:
  - Input validation on all endpoints
  - SQL injection prevention verification
  - XSS protection headers
  - HTTPS enforcement
  - JWT security best practices
- [ ] Create comprehensive API tests:
  - Integration tests cho all major workflows
  - Authentication flow testing
  - Authorization testing cho all endpoints
  - Error handling testing
- [ ] Add comprehensive logging:
  - Structured logging với Serilog
  - Error tracking và alerting
  - Performance metrics logging
  - Security event logging
- [ ] Prepare production configuration:
  - Environment-specific appsettings
  - Database migration scripts
  - Docker containerization (optional)
  - CI/CD pipeline preparation
- [ ] Create API documentation:
  - Complete Swagger documentation
  - API usage examples
  - Authentication guide
  - Error code documentation
- [ ] Database optimization for production:
  - Final indexing review
  - Database size optimization
  - Backup strategy implementation

**Definition of Done:**

- Security vulnerabilities addressed và tested
- Integration tests covering all major scenarios
- Comprehensive logging implemented
- Production configuration ready
- API documentation complete và accurate
- Application ready for production deployment

---

## Backend Development Guidelines

### Code Standards

- Follow C# coding conventions và naming standards
- Implement SOLID principles trong all services
- Use async/await cho all database operations
- Implement proper exception handling và logging
- Write comprehensive unit tests cho business logic

### Architecture Patterns

- **4-Layer Architecture** với separate projects
- **Repository Pattern** cho data access layer
- **Unit of Work Pattern** cho transaction management
- **Dependency Injection** cho all services
- **Code-First** approach với EF Core migrations

### Database Guidelines

- Use Entity Framework Core migrations cho all schema changes
- Implement proper indexing cho performance
- Use soft deletes (IsActive flag) instead of hard deletes
- Store sensitive data encrypted
- Maintain audit trails với timestamps

### API Design

- Follow RESTful conventions
- Use proper HTTP status codes
- Implement consistent error response format
- Add comprehensive input validation
- Include proper authorization checks

### Testing Strategy

- Unit tests for all services and repositories
- Integration tests for API endpoints
- Mock external dependencies in tests
- Test both success and failure scenarios
- Maintain high test coverage (>80%)

### Performance Guidelines

- Implement caching for frequently accessed data
- Use pagination for large datasets
- Optimize database queries and avoid N+1 problems
- Implement async operations for I/O bound tasks
- Monitor and log performance metrics
