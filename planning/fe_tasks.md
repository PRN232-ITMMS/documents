# Frontend Tasks - 1 Week Implementation Plan (REVISED)

## 📋 Tổng quan

Frontend development plan **1 tuần** được điều chỉnh dựa trên **guide.md APIs** để đảm bảo 100% khả thi và đúng main flow business.

---

## 🎯 **API READINESS STATUS (UPDATED)**

### **✅ APIs SẴN SÀNG (100% Ready từ guide.md):**

- **Authentication & Users** ✅ - `/api/auth/*`
- **Treatment Services & Packages** ✅ - `/api/treatment-services`, `/api/treatment-packages`
- **Doctors** ✅ - `/api/doctors`, `/api/doctors/{id}/availability`
- **Treatment Cycles** ✅ - `/api/treatment-cycles/*`
- **Treatment Phases** ✅ - `/api/treatment-phases`, `/api/treatment-cycles/{id}/initialize`
- **Appointments** ✅ - `/api/appointments/*`, dual availability APIs
- **Test Results** ✅ - `/api/test-results/*`
- **Prescriptions** ✅ - `/api/prescriptions/*`, `/api/medications`
- **Payments** ✅ - `/api/payments/*` (VNPay)
- **Reviews** ✅ - `/api/reviews`
- **Analytics** ✅ - `/api/analytics/*`

### **❌ KHÔNG CÓ APIS (Skip features):**

- **Real-time SignalR** ❌ - Use polling thay thế
- **Advanced Notifications** ❌ - Basic notification UI only

---

## 🗓️ **1-WEEK TIMELINE BREAKDOWN (REVISED)**

### **DAY 1: SERVICE DISCOVERY & PACKAGE SELECTION**

#### **Task #FE-007: Complete Service Catalog**

**Priority:** 🔴 Critical  
**Time:** 8 hours
**API Status:** ✅ 100% Ready

**Backend APIs:**

```json
✅ GET /api/treatment-services
✅ GET /api/treatment-services/{id}
✅ GET /api/treatment-packages
✅ GET /api/treatment-packages/{id}
✅ GET /api/treatment-packages/by-service/{serviceId}
```

**Implementation:**

```typescript
// Day 1 Morning (4h): Service Browser
/src/pages/services/
├── ServicesBrowser.tsx          // Main catalog page
├── ServiceDetail.tsx            // Service detail view
└── PackageComparison.tsx        // Package comparison table

/src/components/services/
├── ServiceCard.tsx              // Service display card
├── PackageCard.tsx              // Package pricing card
├── ServiceFilters.tsx           // Filter by type/price
└── PriceCalculator.tsx          // Cost estimation

// Day 1 Afternoon (4h): Package Selection
├── PackageSelector.tsx          // Multi-package selection
├── PackageComparison.tsx        // Side-by-side comparison
├── ServiceRecommendation.tsx    // Based on user profile
└── AddToCartFlow.tsx            // Selection confirmation
```

**Features by Role:**

- **Customer:** Browse services, compare packages, view pricing
- **Doctor:** View for recommendation purposes
- **Manager/Admin:** Service management interface

---

### **DAY 2: TREATMENT CYCLE + PHASE MANAGEMENT**

#### **Task #FE-008: Complete Treatment Cycle System**

**Priority:** 🔴 Critical
**Time:** 8 hours
**API Status:** ✅ 100% Ready

**Backend APIs:**

```json
✅ GET /api/treatment-cycles (role-based filtering)
✅ GET /api/treatment-cycles/{id}
✅ POST /api/treatment-cycles
✅ PUT /api/treatment-cycles/{id}
✅ POST /api/treatment-cycles/{id}/initialize    // Auto-generate phases
✅ POST /api/treatment-phases                    // Manual phases
✅ GET /api/treatment-cycles/{id}/phases
✅ PUT /api/treatment-cycles/{id}/status         // Update status
```

**Implementation:**

```typescript
// Day 2 Morning (4h): Core Cycle Management
/src/pages/cycles/
├── TreatmentCycles.tsx          // Cycle listing (role-based)
├── CycleDetail.tsx              // Detailed cycle view
├── CreateCycle.tsx              // Create new cycle (Doctor/Manager)
└── CycleTimeline.tsx            // Progress visualization

// Day 2 Afternoon (4h): Phase Management
├── CycleInitialization.tsx      // Auto-generate phases
├── PhaseManagement.tsx          // Manual phase creation/edit
├── PhaseTracking.tsx            // Phase progress tracking
└── CycleStatusUpdate.tsx        // Update cycle status

/src/components/cycles/
├── CycleCard.tsx                // Cycle summary card
├── PhaseCard.tsx                // Individual phase display
├── CycleProgress.tsx            // Progress bar/timeline
├── PhaseWizard.tsx              // Manual phase creation
└── StatusUpdateModal.tsx        // Cycle completion
```

**Main Flow Integration:**

- **Flow 2.1:** Create Treatment Cycle
- **Flow 2.2.1:** Manual phase creation
- **Flow 2.2.2:** Auto-initialization with phases
- **Flow 6.1:** Update cycle status to "Completed"

---

### **DAY 3: COMPLETE APPOINTMENT SYSTEM**

#### **Task #FE-009: End-to-End Appointment Management**

**Priority:** 🔴 Critical
**Time:** 8 hours
**API Status:** ✅ 100% Ready

**Backend APIs:**

```json
✅ GET /api/doctors/{id}/availability?date=YYYY-MM-DD           // Single date
✅ GET /api/appointments/availability?doctorId=X&startDate=X    // Date range
✅ POST /api/appointments
✅ GET /api/appointments (role-based)
✅ PUT /api/appointments/{id}/reschedule
✅ PUT /api/appointments/{id}/cancel
```

**Implementation:**

```typescript
// Day 3 Morning (4h): Booking Flow
/src/pages/appointments/
├── AppointmentBooking.tsx       // Multi-step booking wizard
├── DoctorSelection.tsx          // Choose doctor with availability
├── TimeSlotPicker.tsx           // Available slots picker
└── BookingConfirmation.tsx      // Confirm appointment details

// Day 3 Afternoon (4h): Management & Calendar
├── MyAppointments.tsx           // Customer appointments
├── DoctorSchedule.tsx           // Doctor schedule management
├── AppointmentCalendar.tsx      // Calendar view (all roles)
└── AppointmentManagement.tsx    // Manager overview

/src/components/appointments/
├── AvailabilityChecker.tsx      // Dual API availability check
├── AppointmentCard.tsx          // Appointment display
├── CalendarWidget.tsx           // Calendar component
├── RescheduleModal.tsx          // Reschedule interface
└── BookingWizard.tsx            // Step-by-step booking
```

**Main Flow Integration:**

- **Flow 3.1:** Check doctor availability (both methods)
- **Flow 3.2:** Book appointment with all details

---

### **DAY 4: TEST RESULTS VISUALIZATION**

#### **Task #FE-010: Medical Data & Test Results**

**Priority:** 🟡 Medium
**Time:** 8 hours
**API Status:** ✅ 100% Ready

**Backend APIs:**

```json
✅ GET /api/test-results (with filters)
✅ GET /api/test-results/{id}
✅ POST /api/test-results (Doctor only)
✅ GET /api/treatment-cycles/{cycleId}/test-results
```

**Implementation:**

```typescript
// Day 4 Morning (4h): Test Results Display
/src/pages/medical/
├── TestResults.tsx              // Results listing with filters
├── TestResultDetail.tsx         // Individual result view
├── AddTestResult.tsx            // Doctor input form
└── MedicalHistory.tsx           // Patient medical timeline

// Day 4 Afternoon (4h): Charts & Visualization
/src/components/medical/
├── TestResultsChart.tsx         // Trend charts (recharts)
├── ResultsTable.tsx             // Tabular data display
├── NormalRangeIndicator.tsx     // Visual reference ranges
├── TrendAnalysis.tsx            // Progress over time
└── MedicalTimeline.tsx          // Chronological view
```

**Main Flow Integration:**

- **Flow 4.1:** Record test results (Doctor workflow)
- **Flow G:** Conduct tests (Patient view results)

---

### **DAY 5: PAYMENT INTEGRATION**

#### **Task #FE-011: Payment Integration**

**Priority:** 🟡 Medium
**Time:** 8 hours
**API Status:** ✅ 100% Ready

**Backend APIs:**

```json
✅ POST /api/payments/create (VNPay)
✅ GET /api/payments/history/{customerId}
✅ GET /api/payments/status/{paymentId}
✅ GET /api/payments/vnpay/return
✅ POST /api/payments/refund (Admin)
```

**Implementation:**

```typescript
// Day 5 Morning (4h): Payment Flow
/src/pages/payments/
├── PaymentCheckout.tsx          // Checkout process
├── VNPayGateway.tsx             // Gateway integration
├── PaymentSuccess.tsx           // Success confirmation
└── PaymentFailed.tsx            // Error handling

// Day 5 Afternoon (4h): Payment Management
├── PaymentHistory.tsx           // Customer payment history
├── PaymentManagement.tsx        // Admin payment oversight
├── InvoiceGeneration.tsx        // PDF invoice creation
└── RefundProcess.tsx            // Admin refund interface

/src/components/payments/
├── PaymentMethodSelector.tsx    // VNPay selection
├── PaymentSummary.tsx           // Order breakdown
├── PaymentStatus.tsx            // Real-time status
├── InvoiceTemplate.tsx          // PDF template
└── RefundModal.tsx              // Refund interface
```

**Features:**

- VNPay gateway integration
- Real-time payment status tracking
- PDF invoice generation
- Admin refund processing
- Payment history with search/filter

---

### **DAY 6: ANALYTICS & REPORTING DASHBOARD (8h)**

#### **Task #FE-012: Business Intelligence Dashboard**

**Priority:** 🟡 Medium
**Time:** 8 hours
**API Status:** ✅ 80% Ready

**Backend APIs:**

```json
✅ GET /api/analytics/dashboard-stats
✅ GET /api/analytics/revenue
✅ GET /api/analytics/success-rates
✅ GET /api/analytics/doctor-performance
✅ POST /api/analytics/export
```

**Implementation:**

```typescript
// Day 6 Morning (4h): Role-based Dashboards
/src/pages/analytics/
├── CustomerDashboard.tsx        // Personal health metrics
├── DoctorDashboard.tsx          // Clinical performance metrics
├── ManagerDashboard.tsx         // Business intelligence
└── ReportsGeneration.tsx        // Export functionality

// Day 6 Afternoon (4h): Advanced Visualization
/src/components/analytics/
├── MetricsCard.tsx              // KPI display cards
├── RevenueChart.tsx             // Financial performance charts
├── SuccessRateMetrics.tsx       // Treatment success statistics
├── PatientDemographics.tsx      // Demographics breakdown
├── PerformanceMetrics.tsx       // Doctor performance tracking
└── ExportInterface.tsx          // Report export tools
```

**Dashboard Features by Role:**

- **Customer:** Personal treatment progress, health trends, appointment history
- **Doctor:** Patient outcomes, appointment schedule, success rates
- **Manager:** Revenue analytics, resource utilization, system performance

---

### **DAY 7: PRESCRIPTION MANAGEMENT + REVIEWS (8h)**

#### **Task #FE-013: Prescription System & User Feedback**

**Priority:** 🟢 Low
**Time:** 8 hours
**API Status:** ✅ 100% Ready

**Backend APIs:**

```json
✅ POST /api/prescriptions/phase/{id}           // Create prescription
✅ GET /api/prescriptions/customer/{id}/active   // Active prescriptions
✅ GET /api/medications                          // Medication catalog
✅ POST /api/reviews                             // Create review
✅ GET /api/reviews                              // Get reviews
```

**Implementation:**

```typescript
// Day 7 Morning (4h): Prescription Management
/src/pages/prescriptions/
├── PrescriptionManagement.tsx   // Doctor prescribing interface
├── ActivePrescriptions.tsx      // Patient medication tracking
├── MedicationCatalog.tsx        // Available medications
└── MedicationMonitoring.tsx     // Usage tracking

// Day 7 Afternoon (4h): Reviews & Polish
/src/pages/reviews/
├── ReviewSystem.tsx             // Submit reviews
├── ReviewDisplay.tsx            // Display reviews
├── ReviewModeration.tsx         // Admin moderation
└── ReviewStatistics.tsx         // Review analytics

/src/components/prescriptions/
├── PrescriptionForm.tsx         // Doctor prescription form
├── MedicationCard.tsx           // Medication display
├── DosageTracker.tsx            // Patient usage tracking
├── ReviewForm.tsx               // Review submission
├── RatingStars.tsx              // Star rating component
└── ReviewCard.tsx               // Review display card
```

**Main Flow Integration:**

- **Flow 4.2:** Create prescription (Doctor)
- **Flow 5.1:** Monitor medication usage (Patient)
- **Flow 6.2:** Write reviews (Patient)

---

## 🎯 **ROLE-BASED FEATURE MATRIX (UPDATED)**

### **Customer Journey:**

| Day | Feature                 | Customer Use Case                      |
| --- | ----------------------- | -------------------------------------- |
| 1   | Service Browser         | Browse and select treatment packages   |
| 2   | Treatment Cycles        | View treatment progress and timeline   |
| 3   | Appointments            | Book appointments with doctors         |
| 4   | Test Results            | View test results and health trends    |
| 5   | Payments                | Make payments and view payment history |
| 6   | Analytics               | Personal health dashboard              |
| 7   | Prescriptions & Reviews | Track medications, submit reviews      |

### **Doctor Workflow:**

| Day | Feature                 | Doctor Use Case                          |
| --- | ----------------------- | ---------------------------------------- |
| 1   | Service Browser         | Recommend appropriate treatments         |
| 2   | Treatment Cycles        | Create/manage patient treatment cycles   |
| 3   | Appointments            | Manage schedule and patient appointments |
| 4   | Test Results            | Record and analyze test results          |
| 5   | Payments                | View payment status for treatments       |
| 6   | Analytics               | Performance metrics and patient outcomes |
| 7   | Prescriptions & Reviews | Prescribe medications, view feedback     |

### **Manager/Admin Features:**

| Day | Feature                 | Manager Use Case                         |
| --- | ----------------------- | ---------------------------------------- |
| 1   | Service Browser         | Manage service catalog and pricing       |
| 2   | Treatment Cycles        | Oversee all treatment cycles             |
| 3   | Appointments            | Manage facility scheduling               |
| 4   | Test Results            | System-wide test result analytics        |
| 5   | Payments                | Financial management and reporting       |
| 6   | Analytics               | Business intelligence dashboard          |
| 7   | Prescriptions & Reviews | Moderate reviews, prescription oversight |

---

## 🚀 **IMPLEMENTATION GUIDELINES (UPDATED)**

### **API Integration Pattern:**

```typescript
// Consistent hook pattern for all APIs:
/src/hooks/api/
├── useAuth.ts              // Authentication APIs
├── useServices.ts          // Treatment services & packages
├── useDoctors.ts           // Doctor management & availability
├── useTreatmentCycles.ts   // Cycles & phases management
├── useAppointments.ts      // Appointment booking & management
├── useTestResults.ts       // Test results & medical data
├── usePrescriptions.ts     // Prescription & medication management
├── usePayments.ts          // Payment processing
├── useAnalytics.ts         // Analytics & reporting
└── useReviews.ts           // Review system
```

### **Component Architecture:**

```typescript
// Feature-based component organization:
/src/components/
├── common/                 // Shared components
│   ├── DataTable.tsx
│   ├── FilterPanel.tsx
│   ├── LoadingStates.tsx
│   └── ErrorBoundary.tsx
├── auth/                   // Authentication components
├── services/               // Service browser components
├── cycles/                 // Treatment cycle components
├── appointments/           // Appointment system components
├── medical/                // Test results & medical components
├── payments/               // Payment system components
├── analytics/              // Analytics & dashboard components
├── prescriptions/          // Prescription management
└── reviews/                // Review system components
```

### **State Management Strategy:**

```typescript
// Zustand stores for complex state:
/src/stores/
├── auth.store.ts          // ✅ Already implemented
├── services.store.ts      // Service selection state
├── booking.store.ts       // Appointment booking flow
├── cycles.store.ts        // Treatment cycle management
└── notifications.store.ts // UI notifications
```

---

## 🎯 **SUCCESS METRICS - End of Week**

### **Functional Goals:**

- [ ] Complete patient journey from service selection to review
- [ ] Doctor workflow from cycle creation to prescription
- [ ] Manager dashboard with full analytics
- [ ] Payment integration with VNPay working
- [ ] All APIs from guide.md integrated

### **Technical Goals:**

- [ ] Mobile responsive (all breakpoints)
- [ ] TypeScript strict mode compliance
- [ ] React Query caching optimized
- [ ] Error boundaries and loading states
- [ ] Performance score >85 on Lighthouse

### **Business Goals:**

- [ ] End-to-end treatment flow functional
- [ ] Role-based permissions working correctly
- [ ] Payment processing operational
- [ ] Analytics dashboard providing insights
- [ ] User feedback system complete

---

## 📋 **RISK MITIGATION (UPDATED)**

### **Low-Risk Features (APIs 100% Ready):**

- All main flow APIs verified in guide.md ✅
- No backend dependencies missing ✅
- Clear API documentation available ✅

### **Medium-Risk Items:**

- Real-time notifications → Use polling instead
- Complex chart visualizations → Use simple charts first
- PDF generation → Use browser print if needed

### **Contingency Plans:**

- If prescription APIs have issues → Focus on read-only medication display
- If payment gateway fails → Implement payment tracking only
- If analytics APIs incomplete → Use mock data for UI

---

## 🚀 **GETTING STARTED - DAY 1 CHECKLIST**

### **Environment Setup:**

```bash
# Verify API connectivity
curl https://localhost:7178/api/treatment-services
curl https://localhost:7178/api/treatment-packages

# Install additional dependencies if needed
npm install recharts jspdf html2canvas

# Create Day 1 feature structure
mkdir -p src/pages/services
mkdir -p src/components/services
mkdir -p src/hooks/api
```

### **Day 1 Development Start:**

```typescript
// 1. Create useServices hook
// 2. Implement ServicesBrowser page
// 3. Create ServiceCard components
// 4. Add package comparison functionality
// 5. Test with real APIs
```

---

**This revised plan ensures 100% alignment with guide.md APIs and delivers a complete, functional treatment management system in 7 days.**
