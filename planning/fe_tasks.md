# Frontend Tasks - 1 Week Implementation Plan

## 📋 Tổng quan

Frontend development plan **1 tuần** tập trung vào **API-driven implementation** với những features có Backend APIs đã hoàn thành 100%. Ưu tiên business impact cao và user experience.

---

## 🎯 **API READINESS STATUS**

### **✅ APIs SẴN SÀNG (Có thể implement ngay):**

- **Authentication & Users** ✅ - AuthController, UserController, CustomersController
- **Treatment Services & Packages** ✅ - TreatmentServiceController, TreatmentPackageController
- **Treatment Cycles** ✅ - TreatmentCyclesController (90% APIs)
- **Appointments** ✅ - AppointmentsController (Full CRUD)
- **Test Results** ✅ - TestResultController (Complete)
- **Payments** ✅ - PaymentsController (VNPay ready)
- **Reviews** ✅ - ReviewsController (Complete)
- **Analytics** ✅ - AnalyticsController (Dashboard ready)

### **❌ APIs CHƯA SẴN SÀNG (Skip trong tuần này):**

- **Medications & Prescriptions** ❌ - Controllers missing
- **Real-time Notifications** ❌ - SignalR Hub chưa có
- **Advanced Phase Management** ❌ - Some APIs missing

---

## 🗓️ **1-WEEK TIMELINE BREAKDOWN**

### **DAY 1-2: HIGH-IMPACT QUICK WINS (16h)**

#### **Task #FE-007: Service Browser & Package Comparison**

**Priority:** 🔴 Critical  
**Time:** 8 hours (Day 1)
**API Status:** ✅ 100% Ready

**Backend APIs:**

```
✅ GET /api/treatment-services
✅ GET /api/treatment-services/{id}
✅ GET /api/treatment-packages
✅ GET /api/treatment-packages/{id}
✅ GET /api/treatment-packages/by-service/{serviceId}
```

**Implementation:**

```typescript
// Day 1 Morning (4h): Core Structure
/src/pages/services/
├── ServicesBrowser.tsx          // Main catalog page
├── ServiceDetail.tsx            // Service detail view
└── PackageComparison.tsx        // Package comparison

/src/components/services/
├── ServiceCard.tsx              // Service display card
├── PackageCard.tsx              // Package card with pricing
├── ServiceFilters.tsx           // Filter controls
└── PriceComparison.tsx          // Comparison table

// Day 1 Afternoon (4h): Business Logic
- Service catalog with search/filter
- Package comparison table
- Price calculator
- Add to cart functionality
```

**Role-based Features:**

- **Customer:** Browse, compare, select packages
- **Doctor:** View for recommendations
- **Manager/Admin:** Full management interface

---

#### **Task #FE-008: Treatment Cycle Dashboard**

**Priority:** 🔴 Critical
**Time:** 8 hours (Day 2)
**API Status:** ✅ 90% Ready

**Backend APIs:**

```
✅ GET /api/treatment-cycles (role-based filtering)
✅ GET /api/treatment-cycles/{id}
✅ POST /api/treatment-cycles
✅ PUT /api/treatment-cycles/{id}
✅ GET /api/treatment-cycles/{id}/phases
```

**Implementation:**

```typescript
// Day 2 Morning (4h): Customer View
/src/pages/cycles/
├── MyTreatmentCycles.tsx        // Customer cycle list
├── TreatmentCycleDetail.tsx     // Cycle detail view
└── CycleProgress.tsx            // Progress tracking

// Day 2 Afternoon (4h): Doctor/Manager Views
├── DoctorCycles.tsx             // Doctor cycle management
├── CreateCycle.tsx              // Create new cycle
└── CycleAnalytics.tsx           // Manager analytics

/src/components/cycles/
├── CustomerCycleCard.tsx        // Patient-focused view
├── DoctorCycleCard.tsx          // Medical management view
├── CycleTimeline.tsx            // Progress visualization
└── CycleActions.tsx             // Role-based actions
```

**Role-specific Features:**

- **Customer:** View progress, next appointments, costs
- **Doctor:** Create cycles, manage phases, assign treatments
- **Manager:** Analytics, resource allocation, performance

---

### **DAY 3-4: USER ENGAGEMENT FEATURES (16h)**

#### **Task #FE-009: Complete Appointment System**

**Priority:** 🔴 Critical
**Time:** 8 hours (Day 3)
**API Status:** ✅ 100% Ready

**Backend APIs:**

```
✅ POST /api/appointments
✅ GET /api/appointments (role-based)
✅ PUT /api/appointments/{id}/reschedule
✅ PUT /api/appointments/{id}/cancel
✅ GET /api/doctors/{id}/availability
```

**Implementation:**

```typescript
// Day 3 Morning (4h): Booking Flow
/src/pages/appointments/
├── AppointmentBooking.tsx       // Multi-step booking wizard
├── MyAppointments.tsx           // Customer appointments
└── AppointmentCalendar.tsx      // Calendar view

// Day 3 Afternoon (4h): Management
├── DoctorSchedule.tsx           // Doctor schedule management
└── AppointmentManagement.tsx    // Manager view

/src/components/appointments/
├── BookingWizard.tsx            // Step-by-step booking
├── DoctorSelector.tsx           // Doctor selection
├── TimeSlotPicker.tsx           // Available time slots
├── AppointmentCard.tsx          // Appointment display
└── RescheduleModal.tsx          // Reschedule interface
```

**Booking Flow:**

```
Customer: Select Service → Choose Doctor → Pick Time → Confirm
Doctor: View Schedule → Confirm/Cancel → Add Notes
Manager: View All → Manage Conflicts → Analytics
```

---

#### **Task #FE-010: Test Results Visualization**

**Priority:** 🟡 Medium
**Time:** 8 hours (Day 4)
**API Status:** ✅ 100% Ready

**Backend APIs:**

```
✅ GET /api/test-results (with filters)
✅ GET /api/test-results/{id}
✅ POST /api/test-results (Doctor only)
✅ GET /api/treatment-cycles/{cycleId}/test-results
```

**Implementation:**

```typescript
// Day 4 Morning (4h): Visualization
/src/pages/medical/
├── TestResults.tsx              // Results listing
├── TestResultDetail.tsx         // Single result view
└── HealthTimeline.tsx           // Timeline view

// Day 4 Afternoon (4h): Charts & Analytics
/src/components/medical/
├── TestResultsChart.tsx         // Trend charts (recharts)
├── HealthMetrics.tsx            // Key indicators
├── TrendAnalysis.tsx            // Progress trends
├── NormalRangeIndicator.tsx     // Visual ranges
└── ResultsTimeline.tsx          // Chronological view
```

**Role-specific Views:**

- **Customer:** Simple, easy-to-understand charts
- **Doctor:** Detailed medical data with references
- **Manager:** Aggregated analytics

---

### **DAY 5-6: BUSINESS CRITICAL FEATURES (16h)**

#### **Task #FE-011: Payment Integration UI**

**Priority:** 🟡 Medium
**Time:** 8 hours (Day 5)
**API Status:** ✅ 100% Ready (VNPay implemented)

**Backend APIs:**

```
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
├── PaymentHistory.tsx           // Customer history
├── PaymentSuccess.tsx           // Success page
└── PaymentManagement.tsx        // Admin management

// Day 5 Afternoon (4h): Components
/src/components/payments/
├── PaymentMethodSelector.tsx    // VNPay/Bank selection
├── PaymentSummary.tsx           // Order breakdown
├── VNPayIntegration.tsx         // Gateway integration
├── PaymentStatus.tsx            // Status tracking
├── InvoiceGenerator.tsx         // PDF receipts
└── RefundInterface.tsx          // Admin refunds
```

**Payment Features:**

- VNPay gateway integration
- Real-time payment status
- Payment history with filters
- Invoice generation
- Admin refund processing

---

#### **Task #FE-012: Analytics & Reporting Dashboard**

**Priority:** 🟡 Medium
**Time:** 8 hours (Day 6)
**API Status:** ✅ 80% Ready

**Backend APIs:**

```
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
├── DoctorDashboard.tsx          // Clinical performance
├── ManagerDashboard.tsx         // Business intelligence
└── ReportGenerator.tsx          // Export functionality

// Day 6 Afternoon (4h): Visualization Components
/src/components/analytics/
├── MetricsCard.tsx              // KPI cards
├── TreatmentProgressChart.tsx   // Progress tracking
├── RevenueChart.tsx             // Financial charts
├── SuccessRateMetrics.tsx       // Success statistics
├── PatientDemographics.tsx      // Demographics charts
└── PerformanceMetrics.tsx       // Performance tracking
```

**Dashboard Features by Role:**

- **Customer:** Personal progress, health trends
- **Doctor:** Patient outcomes, performance metrics
- **Manager:** Revenue, operations, resource utilization

---

### **DAY 7: POLISH & ADDITIONAL FEATURES (8h)**

#### **Task #FE-013: Review System & Notifications**

**Priority:** 🟢 Low
**Time:** 8 hours (Day 7)
**API Status:** ✅ 100% Ready

**Reviews Implementation (4h):**

```typescript
/src/components/reviews/
├── ReviewForm.tsx               // Submit reviews
├── ReviewCard.tsx               // Display reviews
├── RatingStars.tsx              // Star rating component
├── ReviewStats.tsx              // Statistics
└── ReviewModeration.tsx         // Admin moderation
```

**Notifications Implementation (4h):**

```typescript
/src/components/notifications/
├── NotificationCenter.tsx       // Notification panel
├── NotificationItem.tsx         // Single notification
├── NotificationBadge.tsx        // Count indicator
└── NotificationSettings.tsx     // User preferences
```

---

## 🎯 **ROLE-BASED FEATURE MATRIX**

### **Customer Features:**

| Feature                 | Status | Day |
| ----------------------- | ------ | --- |
| Browse Services         | ✅     | 1   |
| View Treatment Progress | ✅     | 2   |
| Book Appointments       | ✅     | 3   |
| View Test Results       | ✅     | 4   |
| Make Payments           | ✅     | 5   |
| Personal Analytics      | ✅     | 6   |
| Submit Reviews          | ✅     | 7   |

### **Doctor Features:**

| Feature                 | Status | Day |
| ----------------------- | ------ | --- |
| Recommend Services      | ✅     | 1   |
| Manage Treatment Cycles | ✅     | 2   |
| Schedule Management     | ✅     | 3   |
| Add Test Results        | ✅     | 4   |
| View Payments           | ✅     | 5   |
| Performance Analytics   | ✅     | 6   |
| View Reviews            | ✅     | 7   |

### **Manager/Admin Features:**

| Feature                | Status | Day |
| ---------------------- | ------ | --- |
| Service Management     | ✅     | 1   |
| Cycle Analytics        | ✅     | 2   |
| Appointment Management | ✅     | 3   |
| Medical Reports        | ✅     | 4   |
| Financial Management   | ✅     | 5   |
| Business Intelligence  | ✅     | 6   |
| Review Moderation      | ✅     | 7   |

---

## 🚀 **IMPLEMENTATION GUIDELINES**

### **Daily Development Flow:**

**Morning (4h):** Core functionality implementation
**Afternoon (4h):** UI polish, testing, integration

### **Code Organization:**

```
src/
├── pages/                       # Route components by feature
│   ├── services/               # Service browser
│   ├── cycles/                 # Treatment cycles
│   ├── appointments/           # Appointments
│   ├── medical/                # Test results
│   ├── payments/               # Payments
│   └── analytics/              # Analytics
├── components/                 # Reusable components
│   ├── common/                 # Shared components
│   ├── role-based/             # Role-specific components
│   └── [feature]/              # Feature-specific components
└── hooks/                      # Custom hooks
    ├── api/                    # API hooks
    └── business/               # Business logic hooks
```

### **Permission Pattern:**

```typescript
<PermissionGate requiredRoles={[UserRole.Doctor]} resourceOwnerId={cycleId}>
  <CreateCycleButton />
</PermissionGate>
```

### **Component Naming:**

```typescript
CustomerServiceCard.tsx; // Customer-specific view
DoctorServiceCard.tsx; // Doctor-specific view
BaseServiceCard.tsx; // Shared component with permissions
```

---

## 🎯 **SUCCESS METRICS - End of Week**

### **Functional Goals:**

- [ ] Service catalog fully functional with package comparison
- [ ] Treatment cycle management for all roles
- [ ] Complete appointment booking flow
- [ ] Test results visualization with charts
- [ ] VNPay payment integration working
- [ ] Analytics dashboard for all roles
- [ ] Review system functional

### **Technical Goals:**

- [ ] Mobile responsive (all breakpoints)
- [ ] TypeScript strict mode compliance
- [ ] Performance score >85 on Lighthouse
- [ ] Error boundaries implemented
- [ ] Loading states for all async operations

### **User Experience Goals:**

- [ ] Intuitive navigation for all roles
- [ ] Consistent design system usage
- [ ] Fast loading times (<2s)
- [ ] Smooth animations and transitions
- [ ] Clear error messages

---

## 📋 **RISK MITIGATION**

### **Low-Risk Features (APIs ready):**

- Service browser ✅
- Treatment cycles ✅
- Appointments ✅
- Test results ✅
- Payments ✅

### **Fallback Plans:**

- Mock prescription features if needed
- Skip real-time notifications (use polling)
- Basic notification system without SignalR

### **Dependencies:**

- ✅ **Zero external dependencies** - all APIs ready
- ✅ **No backend coordination needed** for Week 1 features
- ✅ **Can start immediately**

---

## 🚀 **GETTING STARTED**

### **Day 1 Morning Checklist:**

- [ ] Setup routing for services pages
- [ ] Create ServicesBrowser page structure
- [ ] Implement ServiceCard component
- [ ] Connect to treatment-services API
- [ ] Add basic filtering

### **Quick Start Commands:**

```bash
# Start development
cd D:\idle\ITM\FE
npm run dev

# Create new feature branch
git checkout -b feature/service-browser

# Start with services implementation
mkdir src/pages/services
mkdir src/components/services
```

**This plan ensures 100% executable features with immediate business value and no dependencies on incomplete backend APIs.**
