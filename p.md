# EyeAttend — Database Forensics & Reconciliation Prompt

Audit the complete PostgreSQL EyeAttend database.

Classify every table as:

* CANONICAL
* LEGACY
* DJANGO_CORE
* BIOMETRIC
* AI
* AUDIT
* CONFIGURATION
* DERIVED
* DUPLICATE
* AMBIGUOUS

Reconcile:

users_student → students
users_doctor → lecturers
users_university → universities
users_college → colleges
users_department → departments
users_academiclevel → levels
users_course → courses
users_classroom → classrooms
users_schedule → schedules
users_attendance → attendance
biometrics_studentbiometric → student_biometrics

Use identity matching based on:

Student:
student_number + email + university + department + level

Lecturer:
staff_number + email + university

Never match using name alone.

Generate:

MATCHED
CONFLICT
DUPLICATE
ORPHAN
UNMATCHED
REVIEW_REQUIRED

Do not:

* DROP
* TRUNCATE
* blindly RENAME
* overwrite conflicting records

Create:

recon_identity_map
recon_student_match
recon_lecturer_match
recon_biometric_map
recon_attendance_map
recon_orphan_report
recon_conflict_log

The final database must contain one authoritative canonical representation of each domain entity.

Validate all foreign keys and attendance uniqueness.


# EyeAttend — Canonical Database & Vector Architecture Prompt

Implement the canonical PostgreSQL architecture.

Core entities:

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

Biometric architecture:

Student
→ StudentBiometric

Lecturer
→ LecturerBiometric

No canonical biometric entity may directly depend on auth_user.

Implement pgvector for biometric embeddings where appropriate.

For every embedding store:

* owner
* vector
* dimension
* model
* model_version
* created_at
* verification status

Validate:

D512 embeddings
vector dimension
model compatibility
duplicate vectors
orphan vectors

Implement appropriate vector indexes and benchmark vector search performance.

The database must never expose raw biometric vectors to normal frontend clients.


# EyeAttend — Dataset Engineering & Leakage Prevention Prompt

Audit and document the complete biometric/periocular dataset.

Report:

* total images
* unique identities
* sessions
* capture conditions
* image resolution
* preprocessing
* augmentation
* class distribution

Create explicit train/validation/test partitions.

Prevent leakage using:

DISJOINT IDENTITY SPLIT

and/or

DISJOINT SESSION SPLIT

depending on the experiment.

No identity/session used in test may leak into training where the experimental protocol requires disjointness.

For every experiment report:

* number of identities
* number of images
* number of sessions
* training samples
* validation samples
* test samples

Generate a dataset audit table and leakage validation script.

Do not make performance claims until leakage checks pass.


Attendance must enforce:

UNIQUE(session_id, student_id)







# EyeAttend — Biometric Pipeline Engineering Prompt

Implement and evaluate the following experimental pipelines independently:

## Pipeline A

Camera
→ OpenCV/RTSP
→ YOLO
→ Crop
→ ArcFace
→ Embedding
→ Matching

## Pipeline B

Camera
→ OpenCV/RTSP
→ YOLO
→ Crop
→ SAM 2
→ Mask
→ ArcFace
→ Embedding
→ Matching

## Pipeline C — if required by the experiment

Camera
→ YOLO
→ Periocular Crop
→ preprocessing
→ ArcFace
→ Matching

For each pipeline measure:

* Precision
* Recall
* F1
* Rank-1
* EER
* FAR
* TAR
* FAR@TAR
* inference latency
* FPS
* preprocessing time
* segmentation time
* feature extraction time
* matching time

Use ArcFace/InsightFace consistently and document:

* model name
* checkpoint
* embedding dimension
* preprocessing
* similarity metric
* threshold

Do not claim SAM 2 improves performance without an ablation experiment.

Do not claim ArcFace is superior without comparative evidence.

All experiments must use the same evaluation protocol and dataset split.






# EyeAttend — Ablation Study Prompt

Perform a controlled ablation study.

Compare:

1. YOLO + ArcFace
2. YOLO + SAM 2 + ArcFace

Keep constant:

* dataset
* identity split
* preprocessing
* ArcFace checkpoint
* similarity metric
* threshold protocol
* hardware
* evaluation procedure

Change only the component under evaluation.

Report:

Accuracy-related metrics
Recognition metrics
Rank-1
EER
FAR@TAR
FPS
Latency
Memory usage

Produce:

* comparison table
* ROC/DET curves
* error analysis
* qualitative examples
* statistical interpretation

Determine whether SAM 2 provides a measurable benefit.

If SAM 2 does not provide meaningful improvement, document the result honestly and explain the trade-off.

Do not force a positive result.

# EyeAttend — Attendance Decision Engine Prompt

Implement the professor-required attendance threshold as an explicit configurable policy.

Primary rule:

A student is considered sufficiently present when:

presence_duration >= 75% × lecture_duration

Do not hard-code 75 inside business logic.

Store the threshold in AttendancePolicy.

Support experimental thresholds:

60%
70%
75%
80%
90%

when required for sensitivity analysis.

Calculate:

lecture_duration
presence_duration
attendance_percentage
required_percentage
final_status

Example:

lecture = 100 minutes

required = 75%

student presence = 78 minutes

attendance = PRESENT

student presence = 72 minutes

attendance = ABSENT or PARTIAL according to the approved policy.

Create unit tests for boundary cases:

74.99%
75.00%
75.01%

The lecturer must be able to inspect the calculation.


# EyeAttend — Django Backend Engineering Prompt

Refactor Django into a layered architecture:

API
↓
Application Services
↓
Domain Logic
↓
Repositories
↓
PostgreSQL

Implement services for:

* Authentication
* University management
* Student management
* Lecturer management
* Course management
* Schedule management
* Session management
* Attendance
* Attendance Policy
* Biometrics
* Recognition
* Reporting
* Reconciliation

The backend is authoritative for all business decisions.

The frontend must never calculate authoritative attendance.

The AI service must never bypass backend business rules.

All recognition events must pass through:

AI Result
→ Backend Validation
→ Session Validation
→ Identity Validation
→ Attendance Service
→ AttendanceEvent
→ Attendance


# EyeAttend — AI Service Integration Prompt

Separate AI processing from Django transactional logic.

Architecture:

RTSP Camera
↓
OpenCV Capture
↓
YOLO Detection
↓
SAM 2 optional segmentation
↓
Periocular preprocessing
↓
ArcFace
↓
D512 Embedding
↓
pgvector Search
↓
Similarity Score
↓
Threshold
↓
Recognition Result
↓
Django API
↓
Attendance Service

The AI service must return a structured recognition result.

It must not directly modify attendance tables.

Return:

identity
confidence/similarity
model
model_version
timestamp
camera
session
processing_time
status

Possible statuses:

MATCHED
LOW_CONFIDENCE
UNKNOWN
INVALID
ERROR


# EyeAttend — Frontend Engineering Prompt

Implement the EyeAttend frontend using the approved design system.

Requirements:

Arabic-first
RTL
Tajawal
Tailwind CSS

Use:

bg-slate-50
bg-blue-600
hover:bg-blue-700
text-slate-800
text-slate-500
bg-white
border-slate-100
border-slate-200

Use:

rounded-2xl
rounded-[45px]

Use:

max-w-6xl mx-auto px-6

Public pages:

Home
Role Selection
Login

Authenticated portals:

Super Admin
University Admin
Lecturer
Student

Lecturer attendance interface must clearly show:

* active session
* course
* classroom
* session time
* recognized students
* confidence/status
* attendance percentage
* 75% requirement
* exceptions

Do not expose biometric vectors.

Do not expose technical AI internals to ordinary users unless required for transparency.

Keep the UI calm, academic, accessible, and responsive.


# EyeAttend — Biometric Privacy & Security Prompt

Design a privacy-preserving biometric architecture.

Separate:

Raw Images
from
Biometric Embeddings
from
Identity Metadata.

Implement:

* encryption at rest where appropriate
* encrypted transport
* access control
* least privilege
* audit logs
* retention policy
* deletion policy
* registration policy
* biometric replacement policy
* access monitoring

Evaluate whether raw images must be retained.

If images are not required after embedding generation, implement controlled deletion according to the approved retention policy.

Embeddings must not be treated as ordinary application data.

Protect D512 vectors and restrict access to authorized biometric services.

Document:

Who can register biometrics?
Who can view them?
Who can delete them?
How long are they retained?
How are they protected?
What happens when a student leaves the university?

Perform security tests for unauthorized biometric access.


# EyeAttend — Performance Benchmark Prompt

Benchmark the complete biometric pipeline.

Measure:

* camera capture FPS
* YOLO latency
* SAM 2 latency
* ArcFace latency
* vector search latency
* total recognition latency
* end-to-end attendance latency
* CPU usage
* GPU usage
* memory usage

Run experiments at:

1 stream
5 streams
10 streams
20 streams

where hardware permits.

Report:

mean
median
P95
P99
FPS

Measure the effect of:

YOLO
SAM 2
ArcFace
pgvector
preprocessing

Do not claim real-time performance without reporting measured latency and FPS.

Record hardware and software configuration for reproducibility.

# EyeAttend — Complete Validation Prompt

Create a complete validation suite.

## Database

Test:

* FK integrity
* duplicate students
* duplicate lecturers
* orphan records
* orphan biometrics
* duplicate attendance
* session/student uniqueness

## AI

Test:

* known identity
* unknown identity
* low confidence
* wrong identity
* occlusion
* illumination variation
* pose variation
* periocular-only recognition

## Ablation

Test:

YOLO + ArcFace

against:

YOLO + SAM2 + ArcFace

## Attendance

Test:

74.99%
75.00%
75.01%

## Security

Test:

* unauthorized biometric access
* unauthorized attendance modification
* IDOR
* privilege escalation
* invalid API access

## Frontend

Test:

* RTL
* mobile
* desktop
* keyboard navigation
* focus states
* validation
* API errors

No final claim may be made without corresponding test evidence.


# EyeAttend — Final System Integration Prompt

Integrate:

Frontend
+
Django Backend
+
PostgreSQL
+
pgvector
+
RTSP/OpenCV
+
YOLO
+
SAM 2
+
ArcFace
+
Attendance Engine
+
RBAC
+
Security
+
Monitoring

Final flow:

User Login
↓
Role Validation
↓
Portal
↓
Schedule
↓
Session
↓
Camera
↓
AI Recognition
↓
D512 Embedding
↓
pgvector Search
↓
Confidence Evaluation
↓
Backend Validation
↓
Attendance Event
↓
Attendance Record
↓
75% Policy
↓
Lecture Metrics
↓
Reports

Verify that no layer bypasses the architecture.

Run the complete end-to-end test.

Generate an architecture compliance report against every professor requirement.


# EyeAttend — Research Evidence & Thesis Compliance Prompt

Convert the implemented system into reproducible scientific evidence.

For every major claim provide:

Claim
→ Experiment
→ Dataset
→ Protocol
→ Metric
→ Result
→ Interpretation

Required evidence includes:

1. Dataset statistics
2. Leakage prevention
3. YOLO performance
4. ArcFace recognition performance
5. Periocular recognition performance
6. SAM 2 ablation
7. Recognition metrics
8. EER
9. FAR@TAR
10. Rank-1
11. Attendance 75% validation
12. Privacy architecture
13. FPS
14. latency
15. resource utilization
16. failure cases

Never invent missing results.

If an experiment has not been executed, mark it:

NOT YET VALIDATED

not PASS.

Create a final Professor Requirements Traceability Matrix.

