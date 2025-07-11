# Main Flow của dự án:

## Flow 1:

### 1. Customer đăng ký (đã hoàn thành cả FE và BE)

### 2. Chọn gói điều trị (đã hoàn thành cả FE và BE)

### 3. Chọn bác sĩ (PENDING)

- Hiển thị danh sách bác sĩ chuyên khoa

**Endpoint**: `GET /api/doctors`

```json
{
  "success": true,
  "data": {
    "items": [
      {
        "id": 5,
        "userId": 17,
        "fullName": "Doctor 1",
        "specialization": "Reproductive Endocrinology",
        "isAvailable": true,
        "successRate": 92.5,
        "yearsOfExperience": 12,
        "licenseNumber": "LIC123456"
      },
      {
        "id": 6,
        "userId": 18,
        "fullName": "Doctor 2",
        "specialization": "Gynecology",
        "isAvailable": true,
        "successRate": 88.3,
        "yearsOfExperience": 8,
        "licenseNumber": "LIC654321"
      },
      {
        "id": 7,
        "userId": 19,
        "fullName": "Doctor 3",
        "specialization": "Urology",
        "isAvailable": false,
        "successRate": 85.7,
        "yearsOfExperience": 10,
        "licenseNumber": "LIC112233"
      },
      {
        "id": 8,
        "userId": 23,
        "fullName": "Doctor",
        "specialization": "string",
        "isAvailable": true,
        "successRate": 0,
        "yearsOfExperience": 0,
        "licenseNumber": "string"
      }
    ],
    "pageNumber": 1,
    "pageSize": 10,
    "totalCount": 4,
    "totalPages": 1
  },
  "message": "Operation successful",
  "errors": null,
  "timestamp": "2025-07-11T13:43:28.4759812Z"
}
```

- Chọn bác sĩ

### 4. Đặt lịch hẹn khám ban đầu (PENDING)

- Hiển thị lịch làm việc của bác sĩ

- Chọn ngày và giờ phù hợp

**Endpoint**: `GET /api/doctors/{doctorId}/availability?date=2025-07-11'`

**Response**

```json
{
  "success": true,
  "data": {
    "items": [
      {
        "id": 23,
        "startTime": "13:00:00",
        "endTime": "14:00:00"
      },
      {
        "id": 24,
        "startTime": "14:00:00",
        "endTime": "15:00:00"
      },
      {
        "id": 25,
        "startTime": "15:00:00",
        "endTime": "16:00:00"
      }
    ],
    "pageNumber": 1,
    "pageSize": 100,
    "totalCount": 3,
    "totalPages": 1
  },
  "message": "Doctor availability retrieved successfully.",
  "errors": null,
  "timestamp": "2025-07-11T13:47:21.1181398Z"
}
```

- Xác nhận lịch hẹn (Khách)
  **Endpoint**: `POST /api/appointments'`
  **Request Body**

```json
{
  "customerId": 13,
  "doctorId": 5,
  "doctorScheduleId": 23,
  "appointmentType": "0",
  "scheduledDateTime": "2025-07-11T13:48:39.046Z",
  "notes": "string",
  "results": "string"
}
```

**Response**

```json
{
  "success": true,
  "data": {
    "id": 98,
    "customerId": 13,
    "cycleId": null,
    "doctorId": 5,
    "doctorScheduleId": 23,
    "appointmentType": "Consultation",
    "scheduledDateTime": "2025-07-11T13:48:39.046Z",
    "status": "Scheduled",
    "notes": "string",
    "results": null
  },
  "message": "Appointment created successfully.",
  "errors": null,
  "timestamp": "2025-07-11T13:49:09.0264843Z"
}
```

- Doctor: notification về lịch hẹn mới

- Hiển thị thông tin lịch hẹn đã đặt

## Flow 2:

- Customer đến cơ sở y tế vào ngày hẹn

### 1. Bác sĩ Tạo TreatmentCycle (done FE và BE)

**Endpoint**: `POST /api/treatment-cycles'`
**Request Body**

```json
{
  "customerId": 13,
  "doctorId": 5,
  "packageId": 22,
  "cycleNumber": 2,
  "startDate": "2025-07-11T13:54:54.588Z",
  "expectedEndDate": "2025-09-11T13:54:54.588Z",
  "totalCost": 0,
  "notes": "string"
}
```

**Response**

```json
{
  "success": true,
  "data": {
    "id": 24,
    "createdAt": "2025-07-11T13:55:22.3185461Z",
    "updatedAt": null,
    "isActive": true,
    "customerId": 13,
    "doctorId": 5,
    "packageId": 22,
    "cycleNumber": 2,
    "status": "Created",
    "startDate": "2025-07-11T13:54:54.588Z",
    "expectedEndDate": "2025-09-11T13:54:54.588Z",
    "actualEndDate": null,
    "totalCost": 0,
    "notes": "string",
    "isSuccessful": null,
    "phase": []
  },
  "message": "Treatment cycle created successfully.",
  "errors": null,
  "timestamp": "2025-07-11T13:55:22.3566752Z"
}
```

### 2. Phân chia TreatmentPhases (PENDING)

#### 2.1: Initialize Phase

**Endpoint**: `POST /api/treatment-cycles/{id}/initialize'`
**Request Body**

```json
{
  "startDate": "2025-07-11T13:56:56.934Z",
  "treatmentPlan": "string",
  "specialInstructions": "string",
  "estimatedCompletionDate": "2025-08-11T13:56:56.934Z",
  "autoGeneratePhases": true,
  "autoScheduleAppointments": true
}
```

**Response**

```json
{
  "success": true,
  "data": {
    "id": 24,
    "createdAt": "2025-07-11T13:55:22.3185461",
    "updatedAt": "2025-07-11T13:57:21.5676206Z",
    "isActive": true,
    "customerId": 13,
    "doctorId": 5,
    "packageId": 22,
    "cycleNumber": 2,
    "status": "Initialized",
    "startDate": "2025-07-11T13:54:54.588",
    "expectedEndDate": "2025-09-11T13:54:54.588",
    "actualEndDate": null,
    "totalCost": 0,
    "notes": "string",
    "isSuccessful": null,
    "phase": [
      {
        "id": 58,
        "cycleId": 24,
        "phaseName": "Pre-treatment Assessment",
        "phaseOrder": 1,
        "status": "Pending",
        "startDate": null,
        "endDate": null,
        "instructions": "Comprehensive evaluation and baseline tests",
        "notes": null
      },
      {
        "id": 59,
        "cycleId": 24,
        "phaseName": "Ovarian Stimulation",
        "phaseOrder": 2,
        "status": "Pending",
        "startDate": null,
        "endDate": null,
        "instructions": "Controlled ovarian hyperstimulation with monitoring",
        "notes": null
      },
      {
        "id": 60,
        "cycleId": 24,
        "phaseName": "Egg Retrieval",
        "phaseOrder": 3,
        "status": "Pending",
        "startDate": null,
        "endDate": null,
        "instructions": "Oocyte retrieval procedure under sedation",
        "notes": null
      },
      {
        "id": 61,
        "cycleId": 24,
        "phaseName": "Fertilization & Culture",
        "phaseOrder": 4,
        "status": "Pending",
        "startDate": null,
        "endDate": null,
        "instructions": "In vitro fertilization and embryo culture",
        "notes": null
      },
      {
        "id": 62,
        "cycleId": 24,
        "phaseName": "Embryo Transfer",
        "phaseOrder": 5,
        "status": "Pending",
        "startDate": null,
        "endDate": null,
        "instructions": "Transfer of selected embryos to uterus",
        "notes": null
      },
      {
        "id": 63,
        "cycleId": 24,
        "phaseName": "Post-Transfer Monitoring",
        "phaseOrder": 6,
        "status": "Pending",
        "startDate": null,
        "endDate": null,
        "instructions": "Luteal support and pregnancy monitoring",
        "notes": null
      }
    ]
  },
  "message": "Treatment cycle initialized successfully.",
  "errors": null,
  "timestamp": "2025-07-11T13:57:21.6771246Z"
}
```

#### 2.1: Start Phase

**Endpoint**: `PATCH /api/treatment-cycles/{cycleId}/phases/{phaseId}/start'`
**Request Body**

```json
{
  "startDate": "2025-07-11T13:58:55.670Z",
  "instructions": "string",
  "notes": "string"
}
```

**Response**

```json
{
  "success": true,
  "data": {
    "id": 58,
    "cycleId": 24,
    "phaseName": "Pre-treatment Assessment",
    "phaseOrder": 1,
    "status": "InProgress",
    "startDate": "2025-07-11T13:58:55.67Z",
    "endDate": null,
    "instructions": "string",
    "notes": "string"
  },
  "message": "Treatment Phase started successfully.",
  "errors": null,
  "timestamp": "2025-07-11T13:59:03.0150974Z"
}
```

#### 2.2: Start Phase:

**Endpoint**: `PATCH /api/treatment-cycles/{cycleId}/phases/{phaseId}/start'`
**Request Body**

```json
{
  "startDate": "2025-07-11T13:58:55.670Z",
  "instructions": "string",
  "notes": "string"
}
```

**Response**

```json
{
  "success": true,
  "data": {
    "id": 58,
    "cycleId": 24,
    "phaseName": "Pre-treatment Assessment",
    "phaseOrder": 1,
    "status": "InProgress",
    "startDate": "2025-07-11T13:58:55.67Z",
    "endDate": null,
    "instructions": "string",
    "notes": "string"
  },
  "message": "Treatment Phase started successfully.",
  "errors": null,
  "timestamp": "2025-07-11T13:59:03.0150974Z"
}
```

#### 2.3:

**Endpoint**: `PATCH /api/treatment-cycles/{cycleId}/phases/{phaseId}/cpmplete'`
**Request Body**

```json
{
  "endDate": "2025-07-11T14:01:42.505Z",
  "results": "string",
  "nextPhaseInstructions": "string",
  "notes": "string"
}
```

**Response**

```json
{
  "success": true,
  "data": {
    "id": 58,
    "cycleId": 24,
    "phaseName": "Pre-treatment Assessment",
    "phaseOrder": 1,
    "status": "Completed",
    "startDate": "2025-07-11T13:58:55.67",
    "endDate": "2025-07-11T14:01:42.505Z",
    "instructions": "string",
    "notes": "string\nResults: string\nNext Phase Instructions: string\nstring"
  },
  "message": "Treatment Phase completed successfully.",
  "errors": null,
  "timestamp": "2025-07-11T14:01:50.8752676Z"
}
```

## Flow 3:

### Đặt Appointments → Thực hiện xét nghiệm → Ghi TestResults,

#### 3.1 Đặt Appointments - Appoinment đã được auto tạo ở phần 2.1 - giờ đổi lịch

**Endpoint**: `PUT /api/appointments/99/reschedule'`
**Request Body**

```json
{
  "doctorScheduleId": 25,
  "scheduledDateTime": "2025-07-12T14:06:49.886Z"
}
```

**Response**

```json
{
  "success": true,
  "data": {
    "id": 99,
    "customerId": 0,
    "cycleId": 24,
    "doctorId": 5,
    "doctorScheduleId": 25,
    "appointmentType": "Consultation",
    "scheduledDateTime": "2025-07-12T14:06:49.886Z",
    "status": "Scheduled",
    "notes": "Auto-scheduled for Pre-treatment Assessment",
    "results": null
  },
  "message": "Appointment rescheduled successfully.",
  "errors": null,
  "timestamp": "2025-07-11T14:07:10.762127Z"
}
```

#### 3.2 Ghi TestResults

**Endpoint**: `POST /api/test-results'`
**Request Body**

```json
{
  "cycleId": 24,
  "testType": "BloodTest",
  "appointmentId": 99,
  "testDate": "2025-07-11T14:08:24.364Z",
  "results": "2.0",
  "referenceRange": "1.0-3.0",
  "status": "Pending",
  "doctorNotes": "string"
}
```

**Response**

```json
{
  "success": true,
  "data": {
    "id": 8,
    "cycleId": 24,
    "appointmentId": 99,
    "testType": "BloodTest",
    "testDate": "2025-07-11T14:08:24.364Z",
    "results": "2.0",
    "referenceRange": "1.0-3.0",
    "status": "Pending",
    "doctorNotes": "string",
    "createdAt": "2025-07-11T14:09:02.4974951Z"
  },
  "message": "Test result created successfully.",
  "errors": null,
  "timestamp": "2025-07-11T14:09:02.4990653Z"
}
```

## Flow 4:

### Kê Prescriptions → Theo dõi medication,

#### 4.1 Kê Prescriptions

**Endpoint**: `POST /api/prescriptions/phase/{phaseId}'`
**Request Body**

```json
{
  "medicationId": 4,
  "dosage": "1 viên / ngày",
  "frequency": "Uống mỗi sáng sau khi ăn",
  "duration": 30,
  "instructions": "Không uống khi bụng đói. Uống với nhiều nước.",
  "startDate": "2025-07-08T10:40:20.455Z",
  "endDate": "2025-08-07T10:40:20.455Z"
}
```

**Response**

```json
{
  "success": true,
  "data": {
    "id": 4,
    "medicationId": 4,
    "medicationName": "Paracetamol 300mg",
    "phaseId": 52,
    "phaseName": "Pre-treatment Assessment",
    "dosage": "1 viên / ngày",
    "frequency": "Uống mỗi sáng sau khi ăn",
    "duration": 30,
    "instructions": "Không uống khi bụng đói. Uống với nhiều nước.",
    "startDate": "2025-07-08T10:40:20.455Z",
    "endDate": "2025-08-07T10:40:20.455Z",
    "isActive": true,
    "createdAt": "2025-07-11T14:11:44.4524156Z",
    "updatedAt": null
  },
  "message": "Prescription created successfully.",
  "errors": null,
  "timestamp": "2025-07-11T14:11:44.4789679Z"
}
```

#### 4.2 Theo dõi medication - những Prescription còn trong startDate endDate

**Endpoint**: `GET /api/prescriptions/customer/13/active'`

**Response**

```json
{
  "success": true,
  "data": {
    "items": [
      {
        "id": 4,
        "phaseId": 52,
        "phaseName": "Pre-treatment Assessment",
        "medicationId": 4,
        "medicationName": "Paracetamol 300mg",
        "dosage": "1 viên / ngày",
        "frequency": "Uống mỗi sáng sau khi ăn",
        "duration": 30,
        "instructions": "Không uống khi bụng đói. Uống với nhiều nước.",
        "startDate": "2025-07-08T10:40:20.455",
        "endDate": "2025-08-07T10:40:20.455",
        "isActive": true,
        "createdAt": "2025-07-11T14:11:44.4524156",
        "updatedAt": null
      }
    ],
    "pageNumber": 1,
    "pageSize": 100,
    "totalCount": 1,
    "totalPages": 1
  },
  "message": "Retrieved 1 active prescriptions for customer 13.",
  "errors": null,
  "timestamp": "2025-07-11T14:13:30.1036881Z"
}
```

## Flow 5: Hoàn thành cycle → Đánh giá Reviews

### Hoàn thành cycle → Đánh giá Reviews

#### 5.1 Hoàn thành cycle : khi Phase cuối completed thì nó tự completed

#### 5.2 Đánh giá Reviews

**Endpoint**: `POST /api/reviews'`
**Request Body**

```json
{
  "customerId": 13,
  "doctorId": 5,
  "cycleId": 23,
  "rating": 5,
  "comment": "string"
}
```

**Response**

```json
{
  "success": true,
  "data": {
    "id": 5,
    "customerId": 13,
    "doctorId": 5,
    "cycleId": 23,
    "rating": 5,
    "comment": "string",
    "createdAt": "2025-07-11T14:17:53.6591572Z",
    "isActive": true
  },
  "message": "Review created successfully.",
  "errors": null,
  "timestamp": "2025-07-11T14:17:53.6925165Z"
}
```
