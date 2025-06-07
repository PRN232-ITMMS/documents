# Backend Development Tasks - Infertility Treatment Management System

# Week 1: Project Foundation & 4-Layer Setup

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

# Week 2: Authentication System & Business Logic

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

# Week 3: Doctor Management & Treatment Services

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

# Week 4: Treatment Cycle Management & Appointments

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

# Week 5: Test Results & Medication Management

## 🎯 Sprint Goals

- Test results management system
- Medication và prescription tracking
- Enhanced treatment monitoring

### Issue #BE013: Test Results Management

**Assignee**: BE1  
**Priority**: High  
**Story Points**: 8

**Description:**
Implement comprehensive test results management system.

**Acceptance Criteria:**

- [ ] Create TestResult entity migration:
  ```bash
  dotnet ef migrations add AddTestResults --startup-project API
  ```
- [ ] Create ITestResultService:
  - CreateTestResultAsync(CreateTestResultDto) → TestResultDto
  - GetTestResultsByCycleAsync(int cycleId) → List<TestResultDto>
  - GetTestResultByIdAsync(int testResultId) → TestResultDetailDto
  - UpdateTestResultAsync(int testResultId, UpdateTestResultDto)
  - GetTestResultsByTypeAsync(int cycleId, TestType type)
- [ ] Create TestResultsController:
  ```csharp
  POST /api/test-results (Doctor only)
  GET /api/test-results (filtered by cycle/type/date)
  GET /api/test-results/{id}
  PUT /api/test-results/{id} (Doctor only)
  GET /api/treatment-cycles/{id}/test-results
  ```
- [ ] Support different test types:
  - BloodTest, Ultrasound, HormoneLevel, PregnancyTest
- [ ] Implement result interpretation logic:
  - Normal/Abnormal/RequiresAttention based on reference ranges
- [ ] Add doctor notes system

**Definition of Done:**

- Test results can be recorded với proper structure
- Different test types supported
- Result interpretation working
- Doctor notes system functional

---

### Issue #BE014: Medication & Prescription System

**Assignee**: BE2  
**Priority**: High  
**Story Points**: 9

**Description:**
Implement medication management và prescription tracking system.

**Acceptance Criteria:**

- [ ] Create Medication và Prescription entities migration:
  ```bash
  dotnet ef migrations add AddMedicationsAndPrescriptions --startup-project API
  ```
- [ ] Create IMedicationService:
  - GetAllMedicationsAsync() → List<MedicationDto>
  - CreateMedicationAsync(CreateMedicationDto) - admin only
  - UpdateMedicationAsync(int medicationId, UpdateMedicationDto)
- [ ] Create IPrescriptionService:
  - CreatePrescriptionAsync(CreatePrescriptionDto) - doctor only
  - GetPrescriptionsByPatientAsync(int customerId) → List<PrescriptionDto>
  - UpdatePrescriptionAsync(int prescriptionId, UpdatePrescriptionDto)
  - DiscontinuePrescriptionAsync(int prescriptionId)
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

- [ ] Implement prescription status tracking:
  - Active/Completed/Discontinued
- [ ] Add basic drug interaction checking

**Definition of Done:**

- Medications can be managed in system
- Prescriptions can be created by doctors
- Prescription tracking working correctly
- Basic safety checks implemented

---

### Issue #BE015: Enhanced Email Notification System

**Assignee**: BE1  
**Priority**: Medium  
**Story Points**: 6

**Description:**
Implement comprehensive email notification system.

**Acceptance Criteria:**

- [ ] Create IEmailService implementation:
  - SendEmailAsync(EmailDto emailDto)
  - SendTemplateEmailAsync(string template, object data, string toEmail)
- [ ] Create email templates:
  - Appointment confirmation
  - Appointment reminder
  - Treatment phase update
  - Test results available
- [ ] Add EmailService registration trong DI container
- [ ] Implement email queue system (basic):
  - Background service cho email sending
  - Retry mechanism cho failed emails
- [ ] Add email tracking:
  - EmailLog entity cho tracking sent emails
  - Delivery status tracking
- [ ] Create email configuration:
  - SMTP settings trong appsettings
  - Email template configuration

**Definition of Done:**

- Email notifications sent automatically
- Email templates working correctly
- Background email processing implemented
- Email delivery tracking functional

---

# Week 6: Reviews, Blog & Advanced Features

## 🎯 Sprint Goals

- Review và rating system
- Blog management system
- Advanced communication features

### Issue #BE016: Review & Rating System

**Assignee**: BE2  
**Priority**: High  
**Story Points**: 7

**Description:**
Implement comprehensive review và rating system.

**Acceptance Criteria:**

- [ ] Create Review entity migration:
  ```bash
  dotnet ef migrations add AddReviews --startup-project API
  ```
- [ ] Create IReviewService:
  - CreateReviewAsync(CreateReviewDto) → ReviewDto
  - GetReviewsByDoctorAsync(int doctorId) → List<ReviewDto>
  - ApproveReviewAsync(int reviewId) - admin only
  - GetReviewStatisticsAsync(int doctorId) → ReviewStatisticsDto
- [ ] Create ReviewsController:
  ```csharp
  POST /api/reviews (Customer only)
  GET /api/reviews (với filtering)
  PUT /api/reviews/{id}/approve (Admin only)
  GET /api/doctors/{id}/reviews
  ```
- [ ] Implement rating aggregation:
  - Average rating calculation
  - Rating distribution analysis
  - Update doctor success rate based on reviews
- [ ] Add review moderation system:
  - Auto-approval for trusted customers
  - Manual review for new customers

**Definition of Done:**

- Reviews can be submitted và managed
- Rating aggregation working correctly
- Moderation workflow operational
- Doctor ratings updated automatically

---

### Issue #BE017: Blog Management System

**Assignee**: BE1  
**Priority**: Medium  
**Story Points**: 6

**Description:**
Create blog và content management system cho educational content.

**Acceptance Criteria:**

- [ ] Create BlogPost entity migration:
  ```bash
  dotnet ef migrations add AddBlogPosts --startup-project API
  ```
- [ ] Create IBlogService:
  - CreateBlogPostAsync(CreateBlogPostDto) → BlogPostDto
  - GetPublishedPostsAsync(BlogFilterDto) → PaginatedResultDto<BlogPostDto>
  - GetBlogPostByIdAsync(int blogPostId) → BlogPostDetailDto
  - PublishBlogPostAsync(int blogPostId)
  - SearchBlogPostsAsync(string searchTerm) → List<BlogPostDto>
- [ ] Create BlogPostsController:
  ```csharp
  GET /api/blog-posts (published posts với pagination)
  GET /api/blog-posts/{id}
  POST /api/blog-posts (Author/Admin only)
  PUT /api/blog-posts/{id}/publish
  GET /api/blog-posts/search
  ```
- [ ] Implement blog features:
  - Category management
  - Tag system
  - Full-text search
  - View tracking
- [ ] Add content workflow:
  - Draft → Review → Published

**Definition of Done:**

- Blog posts can be created và managed
- Publishing workflow operational
- Search functionality working
- Category và tag system functional

---

### Issue #BE018: Advanced Analytics Foundation

**Assignee**: BE2  
**Priority**: Medium  
**Story Points**: 5

**Description:**
Create foundation cho advanced analytics và reporting.

**Acceptance Criteria:**

- [ ] Create IAnalyticsService:
  - GetSuccessRatesByTreatmentTypeAsync() → TreatmentAnalyticsDto
  - GetDoctorPerformanceAsync(int doctorId) → DoctorPerformanceDto
  - GetPatientSatisfactionAsync() → SatisfactionMetricsDto
  - GetRevenueAnalyticsAsync(DateTime from, DateTime to) → RevenueAnalyticsDto
- [ ] Create AnalyticsController:
  ```csharp
  GET /api/analytics/success-rates
  GET /api/analytics/doctor-performance/{id}
  GET /api/analytics/patient-satisfaction
  GET /api/analytics/revenue
  ```
- [ ] Implement calculation algorithms:
  - Success rate calculations by treatment type
  - Doctor performance metrics
  - Patient satisfaction scoring
  - Revenue tracking và forecasting
- [ ] Add caching cho analytics data:
  - Cache frequently requested analytics
  - Background calculation cho heavy reports
- [ ] Create analytics DTOs:
  - TreatmentAnalyticsDto, DoctorPerformanceDto
  - SatisfactionMetricsDto, RevenueAnalyticsDto

**Definition of Done:**

- Analytics APIs returning accurate data
- Success rates calculated correctly
- Performance metrics meaningful
- Caching improving response times

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
