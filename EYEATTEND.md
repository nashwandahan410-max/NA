# EYEATTEND — PHASE-BY-PHASE ENGINEERING PROMPTS

## PHASE 0 — Repository & Architecture Forensics

### Engineering Prompt

Inspect the entire EyeAttend repository before making architectural changes.

Analyze:

* Django apps
* models.py
* serializers
* views
* URLs
* services
* repositories
* templates
* frontend JavaScript
* Tailwind configuration
* migrations
* biometric modules
* AI modules
* database access
* authentication
* authorization
* tests
* configuration
* Docker files
* environment configuration

Create a dependency map:

```text
Frontend
 ↓
URLs/API
 ↓
Views/Controllers
 ↓
Services
 ↓
Models/Repositories
 ↓
PostgreSQL
```

Also identify:

* duplicate models
* duplicate business logic
* circular imports
* legacy dependencies
* unused models
* conflicting migrations
* direct database access from inappropriate layers
* biometric code coupled to Django auth
* attendance logic duplicated across applications

Do not modify the project.

Output:

1. Repository inventory
2. Application architecture map
3. Dependency graph
4. Duplicate model report
5. Legacy dependency report
6. Security risk report
7. Target architecture proposal
8. Migration impact report

---

# PHASE 1 — DATABASE FORENSICS

### Engineering Prompt

Inspect the live PostgreSQL database and compare the actual schema against the EyeAttend canonical architecture.

Inventory:

* tables
* row counts
* columns
* PKs
* FKs
* indexes
* unique constraints
* check constraints
* defaults
* nullable fields
* sequences
* triggers

Classify tables as:

* CANONICAL
* LEGACY
* DJANGO_CORE
* DERIVED
* AI
* BIOMETRIC
* AUDIT
* CONFIGURATION
* DUPLICATE
* AMBIGUOUS

Use the existing 45-table forensic inventory as the baseline.

Do not modify or delete anything.

Output the complete:

Database Forensics & Reconciliation Matrix.

---

# PHASE 2 — IDENTITY RECONCILIATION

### Engineering Prompt

Reconcile legacy and canonical entities.

Student:

```text
users_student
        ↓
students
```

Lecturer:

```text
users_doctor
        ↓
lecturers
```

University:

```text
users_university
        ↓
universities
```

College:

```text
users_college
        ↓
colleges
```

Department:

```text
users_department
        ↓
departments
```

Academic level:

```text
users_academiclevel
        ↓
levels
```

Course:

```text
users_course
        ↓
courses
```

Classroom:

```text
users_classroom
        ↓
classrooms
```

Schedule:

```text
users_schedule
        ↓
schedules
```

Attendance:

```text
users_attendance
        ↓
attendance
```

Use multiple identity attributes.

Never match by name alone.

Generate:

* MATCHED
* CONFLICT
* DUPLICATE
* ORPHAN
* UNMATCHED
* REVIEW_REQUIRED

Store every mapping in reconciliation tables.

No destructive operation.

---

# PHASE 3 — CANONICAL DATABASE

### Engineering Prompt

Design and implement the final canonical EyeAttend PostgreSQL schema.

The schema must represent:

University
College
Department
Level
Course
Classroom
Student
Lecturer
LecturerCourse
Schedule
Session
Attendance
AttendanceEvent
AttendancePolicy
Exception
LectureMetric
InferredAbsenceReason
StudentBiometric
LecturerBiometric

Implement:

* PK
* FK
* indexes
* unique constraints
* check constraints
* timestamps
* audit fields where necessary

Critical rule:

```text
UNIQUE(session_id, student_id)
```

for attendance.

Do not introduce duplicate domain entities.

Do not use legacy users_* tables as the canonical application schema.

---

# PHASE 4 — DJANGO DOMAIN/BACKEND

### Engineering Prompt

Refactor the Django backend so that each canonical domain entity has one authoritative model.

Remove conceptual duplication between applications.

Do not create separate competing versions of:

* University
* Student
* Lecturer
* Course
* Schedule
* Attendance

Use:

Models
→ Services
→ Repositories
→ APIs

Business rules must reside in the service/domain layer.

The backend must operate against canonical tables only after migration readiness is confirmed.

Generate:

* models
* managers/querysets where needed
* services
* repositories
* validators
* exceptions
* tests
* migrations

Do not delete legacy data.

---

# PHASE 5 — AUTHENTICATION & RBAC

### Engineering Prompt

Design secure authentication and role-based authorization.

Roles:

* Super Admin
* University Admin
* Lecturer
* Student

Separate:

Authentication
from
Domain Identity
from
Authorization.

Implement permissions such as:

Super Admin:

* manage universities
* manage system configuration
* manage administrators
* inspect system-wide reports

University Admin:

* manage university data
* colleges
* departments
* levels
* courses
* classrooms
* lecturers
* students
* schedules
* attendance reports

Lecturer:

* view assigned courses
* start sessions
* monitor attendance
* review exceptions
* view reports

Student:

* view personal attendance
* view schedules
* view attendance status

Apply least privilege.

Do not expose privileged APIs to unauthorized roles.

---

# PHASE 6 — REST API

### Engineering Prompt

Build a versioned REST API around the canonical EyeAttend domain.

Use:

```text
/api/v1/
```

Implement endpoints for:

* universities
* colleges
* departments
* levels
* courses
* classrooms
* students
* lecturers
* lecturer courses
* schedules
* sessions
* attendance
* attendance events
* biometrics
* policies
* exceptions
* reports

Every API must support:

* authentication
* authorization
* validation
* pagination
* filtering
* consistent error format
* logging
* request correlation
* secure serialization

Never expose raw biometric vectors unnecessarily.

---

# PHASE 7 — BIOMETRIC / AI ENGINE

### Engineering Prompt

Architect the EyeAttend biometric engine as a separate AI processing subsystem.

Pipeline:

```text
Camera
 ↓
Frame Capture
 ↓
Preprocessing
 ↓
Face/Eye Detection
 ↓
Feature Extraction
 ↓
Embedding Generation
 ↓
Vector Matching
 ↓
Confidence Evaluation
 ↓
Identity Candidate
 ↓
Attendance Decision
 ↓
Attendance Event
```

Support:

* face recognition
* eye/iris-related recognition where implemented
* vector storage
* model versioning
* confidence thresholds
* duplicate detection
* model compatibility

Every recognition result must include:

* candidate identity
* confidence
* model version
* timestamp
* session
* camera
* recognition mode

Low confidence must produce:

```text
UNVERIFIED
```

rather than automatically creating trusted attendance.

The AI layer must communicate with the backend through a controlled service/API boundary.

---

# PHASE 8 — ATTENDANCE ENGINE

### Engineering Prompt

Implement attendance as a domain workflow.

Lifecycle:

```text
Schedule
 ↓
Session Created
 ↓
Session Activated
 ↓
Camera Processing
 ↓
Recognition Event
 ↓
Attendance Event
 ↓
Attendance Record
 ↓
Presence Calculation
 ↓
Policy Evaluation
 ↓
Final Status
```

Attendance must support:

* PRESENT
* ABSENT
* LATE
* EXCUSED
* PARTIAL
* UNVERIFIED

Use AttendancePolicy to calculate required attendance.

Calculate:

* entry time
* last seen
* exit
* presence minutes
* required minutes
* percentage
* final attendance status

Never create duplicate attendance for the same student/session.

---

# PHASE 9 — FRONTEND DESIGN SYSTEM

### Engineering Prompt

Build the EyeAttend frontend according to the provided Public Frontend Design Notes.

Mandatory:

```html
<html lang="ar" dir="rtl">
```

Use:

* Tailwind CSS
* Tajawal
* slate-50
* blue-600
* blue-700
* blue-50
* slate-800
* slate-500
* white surfaces
* soft borders
* rounded functional components

Navigation:

```text
bg-white/80
backdrop-blur-md
sticky top-0
```

Use responsive containers:

```text
max-w-6xl mx-auto px-6
```

Do not introduce:

* gradients
* noisy backgrounds
* second color palette
* second typography system
* unrelated component styles

Implement reusable design components first.

---

# PHASE 10 — PUBLIC FRONTEND

### Engineering Prompt

Implement the public EyeAttend experience.

Pages:

1. Home
2. Role Selection
3. Login
4. Authentication feedback
5. Public information sections

Role selection must provide:

* Super Admin
* University Admin
* Lecturer
* Student

Use large responsive cards.

Login:

```text
max-w-md
```

Use:

* blue icon block
* Arabic heading
* RTL fields
* full-width primary button
* visible focus
* loading state
* error state
* success state

Use subtle:

```text
transition-all duration-300
```

Do not make the UI visually noisy.

---

# PHASE 11 — ROLE PORTALS

### Engineering Prompt

Create role-specific dashboards using the same EyeAttend design system.

## University Admin

Dashboard:

* university information
* colleges
* departments
* levels
* courses
* classrooms
* lecturers
* students
* schedules
* attendance statistics

## Lecturer

Dashboard:

* assigned courses
* today's schedule
* active sessions
* attendance
* exceptions
* session metrics

## Student

Dashboard:

* personal profile
* today's schedule
* attendance percentage
* attendance history
* biometric registration status

## Super Admin

Use:

```text
bg-slate-900 text-white
```

for system-level navigation where appropriate.

Show:

* universities
* subscriptions
* system health
* administrators
* AI status
* database health
* audit activity

All dashboards must remain calm, readable, and task-oriented.

---

# PHASE 12 — FRONTEND ↔ API INTEGRATION

### Engineering Prompt

Connect the frontend to the canonical REST API.

Implement:

* authentication state
* role-aware routing
* API client
* request handling
* loading states
* empty states
* validation errors
* server errors
* pagination
* filtering
* optimistic UI only where safe

Never place business rules in frontend code.

The frontend displays and requests domain operations.

The backend remains authoritative.

---

# PHASE 13 — AI ↔ BACKEND ↔ DATABASE INTEGRATION

### Engineering Prompt

Integrate the biometric subsystem with the canonical backend.

Required architecture:

```text
Camera
 ↓
AI Service
 ↓
Recognition Result
 ↓
Backend API
 ↓
Session Validation
 ↓
Student/Identity Resolution
 ↓
Attendance Service
 ↓
AttendanceEvent
 ↓
Attendance
 ↓
Metrics
```

The AI service must not directly write arbitrary attendance records.

The backend must validate:

* active session
* valid classroom
* valid student
* recognition confidence
* duplicate attendance
* policy
* timestamp
* authorization/context

Store sufficient metadata to audit every recognition decision.

---

# PHASE 14 — SECURITY ENGINEERING

### Engineering Prompt

Perform a complete security audit.

Inspect:

* authentication
* authorization
* Django settings
* secrets
* environment variables
* database credentials
* API permissions
* CSRF
* CORS
* SQL injection
* XSS
* IDOR
* insecure direct object access
* file uploads
* biometric storage
* logs
* error responses

Pay special attention to biometric information.

Never expose sensitive biometric data through normal frontend APIs unless explicitly required.

Implement:

* least privilege
* secure secrets
* encryption in transit
* controlled access
* audit logging
* secure error handling
* rate limiting
* security headers

---

# PHASE 15 — TESTING & QUALITY ASSURANCE

### Engineering Prompt

Create a complete automated test strategy.

Database tests:

* FK integrity
* uniqueness
* orphan detection
* duplicate detection
* reconciliation validation

Backend tests:

* models
* services
* APIs
* permissions
* authentication
* attendance rules
* policy rules

AI tests:

* known identity
* unknown identity
* low confidence
* duplicate recognition
* invalid vector
* model mismatch

Frontend tests:

* RTL
* login
* role routing
* forms
* dashboards
* API errors
* responsive behavior
* accessibility

Integration test:

```text
Login
→ Dashboard
→ Schedule
→ Start Session
→ AI Recognition
→ Attendance Event
→ Attendance
→ Metrics
→ Report
```

---

# PHASE 16 — MIGRATION CUTOVER

### Engineering Prompt

Execute the final migration only after reconciliation reaches an approved state.

Required sequence:

1. Backup
2. Freeze writes
3. Verify backup
4. Create reconciliation snapshot
5. Complete identity mapping
6. Resolve critical conflicts
7. Migrate parent entities
8. Migrate students
9. Migrate lecturers
10. Migrate courses
11. Migrate classrooms
12. Migrate schedules
13. Migrate sessions
14. Migrate biometrics
15. Migrate attendance
16. Validate constraints
17. Validate row counts
18. Validate relationships
19. Run application tests
20. Switch application to canonical tables
21. Keep legacy tables read-only
22. Monitor
23. Archive legacy data

No DROP during cutover.

---

# PHASE 17 — LEGACY DEPRECATION

### Engineering Prompt

After canonical operation has been validated:

Move legacy tables through:

```text
ACTIVE
 ↓
READ_ONLY
 ↓
ARCHIVED
 ↓
DEPRECATED
 ↓
REMOVAL_APPROVED
 ↓
DROP
```

Before final DROP confirm:

* zero application references
* zero unresolved critical mappings
* zero required legacy data
* backup verified
* archive verified
* rollback window completed
* stakeholder approval

Only then generate DROP SQL.

Do not execute destructive SQL automatically.

---

# PHASE 18 — PRODUCTION READINESS

### Engineering Prompt

Prepare EyeAttend for production deployment.

Validate:

* environment configuration
* PostgreSQL
* migrations
* backups
* restore procedures
* HTTPS
* secrets
* logging
* monitoring
* health checks
* API availability
* AI service availability
* camera connectivity
* database connection pooling
* static assets
* frontend build
* error handling

Create:

* deployment checklist
* rollback checklist
* backup policy
* disaster recovery plan
* monitoring plan
* incident response procedure

The system must have a verified rollback procedure before production release.
