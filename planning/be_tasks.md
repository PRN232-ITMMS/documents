# Backend Tasks - Hoàn thiện Workflow Điều trị

## 📋 Tổng quan

Hoàn thiện các API endpoints còn thiếu và tối ưu hóa business logic để support đầy đủ Treatment Workflow. Focus vào 12 flows chính với ưu tiên cao cho core treatment features.

---

## 🔴 **DAY 1 - HIGH PRIORITY (Core Treatment APIs)**

### **Issue #BE-001: Hoàn thiện Treatment Phase Management APIs**

**Priority:** 🔴 Critical
**Estimated Time:** 4 hours
**Status:** Missing APIs

#### **Description:**

Hiện tại chỉ có basic CRUD cho TreatmentPhase. Cần thêm APIs để quản lý flow lifecycle của treatment phases (start, monitor, complete).

#### **Required APIs:**

```csharp
// Trong TreatmentCyclesController.cs
[HttpPatch("{cycleId}/phases/{phaseId}/start")]
public async Task<IActionResult> StartPhase(int cycleId, int phaseId, [FromBody] StartPhaseDto dto)

[HttpPatch("{cycleId}/phases/{phaseId}/complete")]
public async Task<IActionResult> CompletePhase(int cycleId, int phaseId, [FromBody] CompletePhaseDto dto)

[HttpGet("{cycleId}/phases/{phaseId}/progress")]
public async Task<IActionResult> GetPhaseProgress(int cycleId, int phaseId)

[HttpPost("{cycleId}/phases/generate")]
public async Task<IActionResult> GenerateDefaultPhases(int cycleId, [FromBody] GeneratePhasesDto dto)
```

#### **Implementation Details:**

1. **Create DTOs:**

   - `StartPhaseDto`: StartDate, Instructions, Notes
   - `CompletePhaseDto`: EndDate, Results, NextPhaseInstructions
   - `GeneratePhasesDto`: TreatmentType (IUI/IVF), CustomSettings
   - `PhaseProgressDto`: Progress percentage, milestones, current status

2. **Update ICycleService interface:**

```csharp
public interface ICycleService
{
    Task<PhaseResponseDto> StartPhaseAsync(int cycleId, int phaseId, StartPhaseDto dto);
    Task<PhaseResponseDto> CompletePhaseAsync(int cycleId, int phaseId, CompletePhaseDto dto);
    Task<PhaseProgressDto> GetPhaseProgressAsync(int cycleId, int phaseId);
    Task<List<PhaseResponseDto>> GenerateDefaultPhasesAsync(int cycleId, GeneratePhasesDto dto);
}
```

3. **Implement business logic trong CycleService.cs:**
   - Phase transition validation
   - Auto-generate standard IUI/IVF phases
   - Progress calculation based on appointments & test results
   - Phase milestone tracking

#### **Acceptance Criteria:**

- [ ] APIs hoạt động với proper validation
- [ ] Phase status transitions work correctly
- [ ] Auto-generate phases cho IUI (4 phases) và IVF (6 phases)
- [ ] Progress calculation accurate
- [ ] Proper error handling

---

### **Issue #BE-002: Tạo Missing Controllers cho Prescription & Medication**

**Priority:** 🔴 Critical  
**Estimated Time:** 3 hours
**Status:** Controllers missing

#### **Description:**

Services đã có nhưng thiếu Controllers để expose APIs. Cần tạo đầy đủ REST endpoints cho medication và prescription management.

#### **Files to Create:**

1. **MedicationsController.cs**
2. **PrescriptionsController.cs**

#### **Required APIs - MedicationsController:**

```csharp
[HttpGet]
public async Task<IActionResult> GetAllMedications([FromQuery] MedicationFilterDto filters)

[HttpGet("{id}")]
public async Task<IActionResult> GetMedicationById(int id)

[HttpPost]
[Authorize(Roles = "Doctor,Admin")]
public async Task<IActionResult> CreateMedication([FromBody] CreateMedicationDto dto)

[HttpPut("{id}")]
[Authorize(Roles = "Doctor,Admin")]
public async Task<IActionResult> UpdateMedication(int id, [FromBody] UpdateMedicationDto dto)

[HttpDelete("{id}")]
[Authorize(Roles = "Admin")]
public async Task<IActionResult> DeleteMedication(int id)

[HttpGet("search")]
public async Task<IActionResult> SearchMedications([FromQuery] string query)
```

#### **Required APIs - PrescriptionsController:**

```csharp
[HttpPost("phase/{phaseId}")]
[Authorize(Roles = "Doctor")]
public async Task<IActionResult> CreatePrescription(int phaseId, [FromBody] CreatePrescriptionDto dto)

[HttpGet("phase/{phaseId}")]
public async Task<IActionResult> GetPrescriptionsByPhase(int phaseId)

[HttpGet("{id}")]
public async Task<IActionResult> GetPrescriptionById(int id)

[HttpPut("{id}")]
[Authorize(Roles = "Doctor")]
public async Task<IActionResult> UpdatePrescription(int id, [FromBody] UpdatePrescriptionDto dto)

[HttpDelete("{id}")]
[Authorize(Roles = "Doctor")]
public async Task<IActionResult> DeletePrescription(int id)

[HttpGet("customer/{customerId}")]
public async Task<IActionResult> GetPrescriptionsByCustomer(int customerId)

[HttpGet("customer/{customerId}/active")]
public async Task<IActionResult> GetActivePrescriptions(int customerId)
```

#### **DTOs cần tạo:**

```csharp
// Trong Entity/DTOs/Medications/
public class CreateMedicationDto
public class UpdateMedicationDto
public class MedicationFilterDto

// Trong Entity/DTOs/Prescriptions/
public class CreatePrescriptionDto
public class UpdatePrescriptionDto
public class PrescriptionSummaryDto
```

#### **Acceptance Criteria:**

- [ ] All endpoints working với proper authentication
- [ ] Pagination support for listing
- [ ] Search functionality for medications
- [ ] Proper error handling và validation
- [ ] Role-based authorization implemented

---

### **Issue #BE-003: Enhance Cycle Initialization APIs**

**Priority:** 🔴 Critical
**Estimated Time:** 2 hours  
**Status:** Missing business logic

#### **Description:**

Hiện tại cycle creation chỉ tạo record cơ bản. Cần thêm initialization logic để setup đầy đủ treatment workflow.

#### **Enhanced APIs cần thêm:**

```csharp
// Trong TreatmentCyclesController.cs
[HttpPost("{id}/initialize")]
[Authorize(Roles = "Doctor")]
public async Task<IActionResult> InitializeCycle(int id, [FromBody] InitializeCycleDto dto)

[HttpPost("{id}/start-treatment")]
[Authorize(Roles = "Doctor")]
public async Task<IActionResult> StartTreatment(int id, [FromBody] StartTreatmentDto dto)

[HttpGet("{id}/timeline")]
public async Task<IActionResult> GetCycleTimeline(int id)
```

#### **DTOs cần tạo:**

```csharp
public class InitializeCycleDto
{
    public DateTime StartDate { get; set; }
    public string TreatmentPlan { get; set; }
    public List<string> SpecialInstructions { get; set; }
    public bool AutoGeneratePhases { get; set; }
    public bool AutoScheduleAppointments { get; set; }
}

public class StartTreatmentDto
{
    public DateTime TreatmentStartDate { get; set; }
    public string DoctorNotes { get; set; }
    public List<int> RequiredTestIds { get; set; }
}

public class CycleTimelineDto
{
    public List<TimelineEventDto> Events { get; set; }
    public int CompletionPercentage { get; set; }
    public DateTime? EstimatedCompletion { get; set; }
}
```

#### **Business Logic Implementation:**

1. **Initialize Cycle:**

   - Auto-generate standard phases based on treatment type
   - Create initial appointments
   - Setup notification schedule
   - Calculate estimated timeline

2. **Start Treatment:**
   - Update cycle status to InProgress
   - Activate first phase
   - Send welcome notifications
   - Schedule initial tests

#### **Acceptance Criteria:**

- [ ] Cycle initialization creates complete treatment plan
- [ ] Timeline calculation accurate
- [ ] Automatic phase generation works
- [ ] Proper status transitions

---

## 🟡 **DAY 2 - MEDIUM PRIORITY (Enhanced Features)**

### **Issue #BE-004: Implement Real-time Notifications với SignalR**

**Priority:** 🟡 Medium
**Estimated Time:** 5 hours
**Status:** Not implemented

#### **Description:**

Hiện tại notifications chỉ là database records. Cần implement real-time notifications để improve user experience.

#### **Implementation Steps:**

1. **Install SignalR package:**

```xml
<PackageReference Include="Microsoft.AspNetCore.SignalR" Version="8.0.0" />
```

2. **Create NotificationHub.cs:**

```csharp
// Trong API/Hubs/
public class NotificationHub : Hub
{
    public async Task JoinUserGroup(string userId)
    {
        await Groups.AddToGroupAsync(Context.ConnectionId, $"user_{userId}");
    }

    public async Task LeaveUserGroup(string userId)
    {
        await Groups.RemoveFromGroupAsync(Context.ConnectionId, $"user_{userId}");
    }
}
```

3. **Create INotificationHubService:**

```csharp
public interface INotificationHubService
{
    Task SendNotificationToUser(int userId, object notification);
    Task SendNotificationToDoctor(int doctorId, object notification);
    Task SendBroadcastNotification(object notification);
}
```

4. **Update NotificationService để integrate SignalR:**

```csharp
public class NotificationService : INotificationService
{
    private readonly INotificationHubService _hubService;

    public async Task CreateNotificationAsync(CreateNotificationDto dto)
    {
        // Existing database logic...

        // Send real-time notification
        await _hubService.SendNotificationToUser(dto.UserId, notificationDto);
    }
}
```

5. **Configure SignalR trong Program.cs:**

```csharp
builder.Services.AddSignalR();
app.MapHub<NotificationHub>("/notificationHub");
```

#### **Enhanced Notification APIs:**

```csharp
[HttpPost("broadcast")]
[Authorize(Roles = "Admin")]
public async Task<IActionResult> BroadcastNotification([FromBody] BroadcastNotificationDto dto)

[HttpPost("schedule")]
[Authorize(Roles = "Doctor,Admin")]
public async Task<IActionResult> ScheduleNotification([FromBody] ScheduleNotificationDto dto)

[HttpGet("user/{userId}/real-time-status")]
public async Task<IActionResult> GetRealTimeNotificationStatus(int userId)
```

#### **Acceptance Criteria:**

- [ ] Real-time notifications working
- [ ] User groups management
- [ ] Broadcast functionality
- [ ] Scheduled notifications
- [ ] Connection status tracking

---

### **Issue #BE-005: Advanced Appointment Features**

**Priority:** 🟡 Medium
**Estimated Time:** 4 hours
**Status:** Basic CRUD only

#### **Description:**

Hiện tại appointment APIs chỉ có basic CRUD. Cần thêm advanced features như bulk operations, availability checking, automated scheduling.

#### **Enhanced APIs cần thêm:**

```csharp
// Trong AppointmentsController.cs
[HttpGet("availability")]
public async Task<IActionResult> CheckAvailability([FromQuery] AvailabilityQueryDto query)

[HttpPost("bulk-create")]
[Authorize(Roles = "Doctor,Admin")]
public async Task<IActionResult> CreateBulkAppointments([FromBody] BulkCreateAppointmentsDto dto)

[HttpPost("auto-schedule")]
[Authorize(Roles = "Doctor")]
public async Task<IActionResult> AutoScheduleAppointments([FromBody] AutoScheduleDto dto)

[HttpGet("conflicts")]
[Authorize(Roles = "Doctor,Admin")]
public async Task<IActionResult> GetScheduleConflicts([FromQuery] ConflictCheckDto query)

[HttpPost("{id}/send-reminder")]
[Authorize(Roles = "Doctor,Admin")]
public async Task<IActionResult> SendAppointmentReminder(int id)
```

#### **DTOs cần tạo:**

```csharp
public class AvailabilityQueryDto
{
    public int DoctorId { get; set; }
    public DateTime StartDate { get; set; }
    public DateTime EndDate { get; set; }
    public int Duration { get; set; } // minutes
    public string AppointmentType { get; set; }
}

public class BulkCreateAppointmentsDto
{
    public List<CreateAppointmentDto> Appointments { get; set; }
    public bool CheckConflicts { get; set; }
    public bool SendNotifications { get; set; }
}

public class AutoScheduleDto
{
    public int CycleId { get; set; }
    public int DoctorId { get; set; }
    public List<string> AppointmentTypes { get; set; }
    public DateTime PreferredStartDate { get; set; }
    public TimeSpan PreferredTimeRange { get; set; }
}
```

#### **Business Logic Enhancement:**

1. **Availability Checking:**

   - Check doctor schedule
   - Consider existing appointments
   - Account for buffer time
   - Return available time slots

2. **Auto-scheduling:**
   - Generate appointment series for treatment cycle
   - Respect doctor availability
   - Consider treatment phase requirements
   - Send notifications automatically

#### **Acceptance Criteria:**

- [ ] Availability checking accurate
- [ ] Bulk operations working
- [ ] Auto-scheduling generates proper series
- [ ] Conflict detection working
- [ ] Automated reminders

---

## 🟢 **DAY 3 - OPTIMIZATION & ENHANCEMENT**

### **Issue #BE-006: Advanced Analytics & Reporting APIs**

**Priority:** 🟢 Low
**Estimated Time:** 4 hours
**Status:** Basic implementation

#### **Description:**

Mở rộng analytics APIs để cung cấp insights chi tiết hơn cho management dashboard.

#### **Enhanced Analytics APIs:**

```csharp
// Trong AnalyticsController.cs
[HttpGet("treatment-outcomes")]
public async Task<IActionResult> GetTreatmentOutcomes([FromQuery] OutcomeAnalysisDto filters)

[HttpGet("efficiency-metrics")]
public async Task<IActionResult> GetEfficiencyMetrics([FromQuery] EfficiencyQueryDto query)

[HttpGet("patient-journey")]
public async Task<IActionResult> GetPatientJourneyAnalytics([FromQuery] PatientJourneyDto filters)

[HttpGet("predictive-analytics")]
public async Task<IActionResult> GetPredictiveAnalytics([FromQuery] PredictiveQueryDto query)

[HttpPost("custom-report")]
[Authorize(Roles = "Manager,Admin")]
public async Task<IActionResult> GenerateCustomReport([FromBody] CustomReportDto dto)
```

#### **Advanced DTOs:**

```csharp
public class OutcomeAnalysisDto
{
    public DateTime StartDate { get; set; }
    public DateTime EndDate { get; set; }
    public string TreatmentType { get; set; }
    public int? DoctorId { get; set; }
    public string GroupBy { get; set; } // Age, TreatmentType, Doctor
}

public class EfficiencyMetrics
{
    public double AverageAppointmentDuration { get; set; }
    public double DoctorUtilizationRate { get; set; }
    public double PatientSatisfactionScore { get; set; }
    public int TotalCyclesCompleted { get; set; }
    public decimal AverageRevenuePer Cycle { get; set; }
}
```

#### **Implementation Features:**

1. **Treatment Outcomes:**

   - Success rates by various criteria
   - Time-to-success analysis
   - Comparative analysis between doctors
   - Trend analysis over time

2. **Efficiency Metrics:**
   - Resource utilization
   - Patient throughput
   - Revenue analysis
   - Cost per success ratio

#### **Acceptance Criteria:**

- [ ] Advanced analytics working
- [ ] Performance optimized for large datasets
- [ ] Export functionality
- [ ] Role-based access

---

### **Issue #BE-007: Implement Email Integration**

**Priority:** 🟢 Low
**Estimated Time:** 3 hours
**Status:** Service exists but not configured

#### **Description:**

EmailService đã có implementation cơ bản nhưng chưa được configure và integrate với workflow.

#### **Configuration Steps:**

1. **Update appsettings.json:**

```json
{
  "EmailSettings": {
    "SmtpHost": "smtp.gmail.com",
    "SmtpPort": 587,
    "FromEmail": "noreply@infertilitytreatment.com",
    "FromName": "Infertility Treatment System",
    "Username": "your-email@gmail.com",
    "Password": "your-app-password",
    "EnableSsl": true
  }
}
```

2. **Create Email Templates:**

```csharp
// Trong Business/Templates/
public static class EmailTemplates
{
    public static string WelcomeEmail(string customerName) { }
    public static string AppointmentConfirmation(AppointmentDto appointment) { }
    public static string TreatmentPhaseUpdate(TreatmentPhase phase) { }
    public static string TestResultNotification(TestResult result) { }
    public static string PaymentConfirmation(Payment payment) { }
}
```

3. **Email APIs:**

```csharp
// Trong API/Controllers/EmailController.cs
[HttpPost("send-appointment-reminder")]
[Authorize(Roles = "Doctor,Admin")]
public async Task<IActionResult> SendAppointmentReminder([FromBody] SendReminderDto dto)

[HttpPost("send-test-result")]
[Authorize(Roles = "Doctor")]
public async Task<IActionResult> SendTestResultEmail([FromBody] SendTestResultDto dto)

[HttpPost("send-bulk-notification")]
[Authorize(Roles = "Admin")]
public async Task<IActionResult> SendBulkNotification([FromBody] BulkEmailDto dto)
```

#### **Integration Points:**

- Welcome email on registration
- Appointment confirmations & reminders
- Test result notifications
- Treatment phase updates
- Payment confirmations

#### **Acceptance Criteria:**

- [ ] SMTP configuration working
- [ ] Template system functional
- [ ] Automated emails triggered by events
- [ ] Bulk email capability
- [ ] Error handling for failed sends

---

## 🔵 **DAY 4-5 - TESTING & OPTIMIZATION**

### **Issue #BE-008: Comprehensive API Testing**

**Priority:** 🔵 Testing
**Estimated Time:** 8 hours (2 days)
**Status:** Needs implementation

#### **Description:**

Tạo comprehensive test suite để ensure all workflows function correctly end-to-end.

#### **Testing Strategy:**

1. **Unit Tests:**

```csharp
// Trong Tests/Business.Tests/
- CycleServiceTests.cs
- AppointmentServiceTests.cs
- PaymentServiceTests.cs
- NotificationServiceTests.cs
```

2. **Integration Tests:**

```csharp
// Trong Tests/API.Tests/
- TreatmentWorkflowIntegrationTests.cs
- AuthenticationIntegrationTests.cs
- PaymentIntegrationTests.cs
```

3. **End-to-End Workflow Tests:**

```csharp
[Test]
public async Task CompleteIVFTreatmentWorkflow_ShouldSucceed()
{
    // 1. Customer registration
    // 2. Service selection
    // 3. Doctor assignment
    // 4. Cycle creation
    // 5. Phase progression
    // 6. Appointments
    // 7. Test results
    // 8. Prescriptions
    // 9. Payment
    // 10. Completion & review
}
```

#### **Test Coverage Goals:**

- Unit tests: 80%+ coverage
- Integration tests: All controllers
- E2E tests: All major workflows
- Performance tests: High-load scenarios

#### **Acceptance Criteria:**

- [ ] All tests passing
- [ ] Coverage targets met
- [ ] Performance benchmarks established
- [ ] CI/CD pipeline integration

---

### **Issue #BE-009: Performance Optimization**

**Priority:** 🔵 Optimization
**Estimated Time:** 4 hours
**Status:** Needs optimization

#### **Description:**

Optimize database queries và improve API response times cho production readiness.

#### **Optimization Areas:**

1. **Database Optimization:**

```csharp
// Add missing indexes
CREATE INDEX IX_TreatmentCycles_CustomerId_Status ON TreatmentCycles(CustomerId, Status);
CREATE INDEX IX_Appointments_DoctorId_ScheduledDateTime ON Appointments(DoctorId, ScheduledDateTime);
CREATE INDEX IX_TestResults_CycleId_TestDate ON TestResults(CycleId, TestDate);
CREATE INDEX IX_Notifications_UserId_IsRead ON Notifications(UserId, IsRead);
```

2. **Query Optimization:**

```csharp
// Implement query optimization service
public class QueryOptimizationService : IQueryOptimizationService
{
    public async Task<List<TreatmentCycle>> GetCyclesOptimized(int customerId)
    {
        return await _context.TreatmentCycles
            .Include(c => c.TreatmentPackage)
            .Include(c => c.Doctor.User)
            .Where(c => c.CustomerId == customerId)
            .OrderByDescending(c => c.StartDate)
            .ToListAsync();
    }
}
```

3. **Caching Implementation:**

```csharp
// Implement Redis caching
public class CacheService : ICacheService
{
    public async Task<T> GetOrSetAsync<T>(string key, Func<Task<T>> getItem, TimeSpan? expiry = null)
    public async Task RemoveAsync(string key)
    public async Task RemoveByPatternAsync(string pattern)
}
```

#### **Performance Targets:**

- API response time < 200ms for simple queries
- Complex queries < 500ms
- File uploads < 2s
- Database queries optimized with proper indexing

#### **Acceptance Criteria:**

- [ ] Performance benchmarks met
- [ ] Database queries optimized
- [ ] Caching implemented
- [ ] Memory usage optimized

---

## 🟣 **DAY 6-7 - FINAL INTEGRATION & DEPLOYMENT PREP**

### **Issue #BE-010: API Documentation & Validation**

**Priority:** 🟣 Documentation
**Estimated Time:** 4 hours
**Status:** Needs completion

#### **Description:**

Hoàn thiện API documentation và validation để ready cho frontend integration.

#### **Documentation Tasks:**

1. **Enhanced Swagger Configuration:**

```csharp
// Trong Program.cs
builder.Services.AddSwaggerGen(c =>
{
    c.SwaggerDoc("v1", new OpenApiInfo
    {
        Title = "Infertility Treatment API",
        Version = "v1",
        Description = "Complete API for Infertility Treatment Management System"
    });

    c.AddSecurityDefinition("Bearer", new OpenApiSecurityScheme
    {
        Description = "JWT Authorization header using the Bearer scheme",
        Name = "Authorization",
        In = ParameterLocation.Header,
        Type = SecuritySchemeType.ApiKey,
        Scheme = "Bearer"
    });

    var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
    var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
    c.IncludeXmlComments(xmlPath);
});
```

2. **API Documentation Comments:**

```csharp
/// <summary>
/// Creates a new treatment cycle for a customer
/// </summary>
/// <param name="createCycleDto">Treatment cycle creation data</param>
/// <returns>Created treatment cycle with generated phases</returns>
/// <response code="201">Treatment cycle created successfully</response>
/// <response code="400">Invalid input data</response>
/// <response code="401">Unauthorized</response>
[HttpPost]
[ProducesResponseType(typeof(ApiResponseDto<CycleResponseDto>), 201)]
[ProducesResponseType(typeof(ApiResponseDto<object>), 400)]
public async Task<IActionResult> CreateCycle([FromBody] CreateCycleDto createCycleDto)
```

3. **Postman Collection Generation:**
   - Export complete Swagger to Postman
   - Add example requests/responses
   - Create test scenarios

#### **Acceptance Criteria:**

- [ ] All APIs documented
- [ ] Swagger UI comprehensive
- [ ] Postman collection ready
- [ ] API validation rules complete

---

### **Issue #BE-011: Environment Configuration & Deployment Prep**

**Priority:** 🟣 Deployment
**Estimated Time:** 4 hours
**Status:** Needs setup

#### **Description:**

Prepare backend cho production deployment với proper configuration management.

#### **Configuration Tasks:**

1. **Environment-specific appsettings:**

```json
// appsettings.Production.json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=${DB_SERVER};Database=${DB_NAME};User Id=${DB_USER};Password=${DB_PASSWORD};TrustServerCertificate=true;"
  },
  "JwtSettings": {
    "SecretKey": "${JWT_SECRET}",
    "Issuer": "${JWT_ISSUER}",
    "Audience": "${JWT_AUDIENCE}",
    "AccessTokenExpiryMinutes": 60,
    "RefreshTokenExpiryDays": 7
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  }
}
```

2. **Health Checks Enhancement:**

```csharp
// Trong Program.cs
builder.Services.AddHealthChecks()
    .AddDbContext<ApplicationDbContext>()
    .AddUrlGroup(new Uri("https://api.vnpay.vn"), "VNPay")
    .AddSmtpHealthCheck(options =>
    {
        options.Host = builder.Configuration["EmailSettings:SmtpHost"];
        options.Port = int.Parse(builder.Configuration["EmailSettings:SmtpPort"]);
    });

app.MapHealthChecks("/health", new HealthCheckOptions
{
    ResponseWriter = UIResponseWriter.WriteHealthCheckUIResponse
});
```

3. **Docker Configuration:**

```dockerfile
# Dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY ["InfertilityTreatment.API/InfertilityTreatment.API.csproj", "InfertilityTreatment.API/"]
COPY ["InfertilityTreatment.Business/InfertilityTreatment.Business.csproj", "InfertilityTreatment.Business/"]
COPY ["InfertilityTreatment.Data/InfertilityTreatment.Data.csproj", "InfertilityTreatment.Data/"]
COPY ["InfertilityTreatment.Entity/InfertilityTreatment.Entity.csproj", "InfertilityTreatment.Entity/"]

RUN dotnet restore "InfertilityTreatment.API/InfertilityTreatment.API.csproj"
COPY . .
WORKDIR "/src/InfertilityTreatment.API"
RUN dotnet build "InfertilityTreatment.API.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "InfertilityTreatment.API.csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "InfertilityTreatment.API.dll"]
```

#### **Acceptance Criteria:**

- [ ] Environment configurations ready
- [ ] Health checks comprehensive
- [ ] Docker configuration working
- [ ] Logging configured properly
- [ ] Security headers implemented

---

## 📊 **BACKEND TASK SUMMARY**

### **Day-by-Day Breakdown:**

**Day 1 (8h):** 🔴 Critical APIs

- Treatment Phase Management APIs (4h)
- Missing Controllers (3h)
- Cycle Initialization (2h)

**Day 2 (8h):** 🟡 Enhanced Features

- SignalR Implementation (5h)
- Advanced Appointment Features (4h)

**Day 3 (8h):** 🟢 Optimization

- Advanced Analytics (4h)
- Email Integration (3h)

**Day 4-5 (16h):** 🔵 Testing & Performance

- Comprehensive Testing (8h)
- Performance Optimization (4h)

**Day 6-7 (8h):** 🟣 Documentation & Deployment

- API Documentation (4h)
- Deployment Preparation (4h)

### **Total Estimated Time:** 40 hours (1 tuần)

### **Success Metrics:**

- [ ] All 12 treatment workflows supported by APIs
- [ ] 90%+ API test coverage
- [ ] Response time < 500ms for complex queries
- [ ] Real-time notifications working
- [ ] Production-ready configuration
- [ ] Complete API documentation

### **Risk Mitigation:**

- Daily standups để track progress
- Prioritize critical path items first
- Have fallback plan for complex features
- Ensure basic functionality working before advanced features
