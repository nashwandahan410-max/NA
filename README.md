# NA

You are the Principal Database Architect and Django Migration Engineer for the EyeAttend project.

Your task is NOT to modify the database immediately.

Perform a complete Database Forensics and Reconciliation analysis of the existing PostgreSQL database.

DATABASE:
eyeattend_db

CURRENT DATABASE:
45 tables exist.

The schema contains two generations:

CANONICAL/NEW:
universities
colleges
departments
levels
students
lecturers
courses
classrooms
lecturer_courses
schedules
sessions
attendance
attendance_events
attendance_policy
exceptions
lecture_metrics
inferred_absence_reasons
student_biometrics
lecturer_biometrics

LEGACY/OLD:
users_university
users_college
users_department
users_academiclevel
users_student
users_doctor
users_course
users_classroom
users_schedule
users_attendance
users_detectedlecturesession
users_doctorteachingassignment
users_doctor_assigned_colleges

BIOMETRIC LEGACY:
biometrics_studentbiometric

AUTH:
auth_user
auth_group
auth_permission
etc.

PRIMARY OBJECTIVE:

Create a verified reconciliation plan that produces one canonical domain schema without uncontrolled data loss.

DO NOT:
- drop tables;
- truncate data;
- rename tables blindly;
- delete migrations;
- modify production data;
- assume tables with similar names are identical;
- assume legacy rows are unused;
- overwrite conflicting records automatically.

PHASE 1 — INVENTORY

For every table collect:

- table name
- row count
- primary key
- data types
- nullable fields
- defaults
- unique constraints
- check constraints
- indexes
- foreign keys
- referenced tables
- referencing tables
- triggers
- sequences
- ownership
- migration origin

PHASE 2 — CLASSIFICATION

Classify every table:

CANONICAL
LEGACY
DJANGO_CORE
DUPLICATE
AMBIGUOUS
DERIVED
AI
AUDIT
CONFIGURATION

PHASE 3 — DUPLICATE DETECTION

Compare:

users_student ↔ students
users_doctor ↔ lecturers
users_university ↔ universities
users_college ↔ colleges
users_department ↔ departments
users_academiclevel ↔ levels
users_course ↔ courses
users_classroom ↔ classrooms
users_schedule ↔ schedules
users_attendance ↔ attendance
users_doctorteachingassignment ↔ lecturer_courses
biometrics_studentbiometric ↔ student_biometrics

PHASE 4 — IDENTITY RECONCILIATION

For Student matching use:

- student_number
- email
- university
- department
- level
- normalized full name

For Lecturer matching use:

- staff_number
- email
- university
- normalized full name

Never match solely by name.

Produce:

MATCHED
CONFLICT
ORPHAN
DUPLICATE
UNMATCHED

PHASE 5 — BIOMETRIC RECONCILIATION

Determine:

- every biometric record owner;
- whether owner is auth_user, Student, or Lecturer;
- vector dimensions;
- duplicate biometric records;
- orphan biometric records;
- model version;
- registration timestamp.

The canonical relationship must be:

Student → StudentBiometric

Lecturer → LecturerBiometric

Do not allow biometric records to reference auth_user directly unless an explicit architecture decision proves it necessary.

PHASE 6 — ATTENDANCE RECONCILIATION

Validate:

sessions
attendance
attendance_events
attendance_policy
exceptions
lecture_metrics

Ensure:

Attendance belongs to Session + Student.

Enforce:

UNIQUE(session_id, student_id)

Do not delete historical attendance because a student record is being migrated.

PHASE 7 — MIGRATION MAPPING

Generate an explicit mapping:

legacy_table
legacy_pk
canonical_table
canonical_pk
mapping_status
match_method
confidence
conflict_reason

PHASE 8 — ORPHAN DETECTION

Find:

- students without departments;
- students without levels;
- attendance without students;
- attendance without sessions;
- sessions without schedules;
- biometrics without students;
- biometrics without lecturers;
- policies without universities;
- policies referencing deleted courses;
- teaching assignments without lecturers/courses/levels.

PHASE 9 — TARGET SCHEMA

Produce the proposed canonical schema.

Every domain entity must have exactly one canonical table.

PHASE 10 — MIGRATION PLAN

Produce:

1. backup plan;
2. staging tables;
3. reconciliation tables;
4. data migration order;
5. validation queries;
6. rollback strategy;
7. legacy deprecation plan;
8. final DROP plan.

PHASE 11 — DJANGO ALIGNMENT

Compare:

PostgreSQL schema
vs
Django Models
vs
Django migrations

Every discrepancy must be listed.

PHASE 12 — ACCEPTANCE CRITERIA

The final architecture must satisfy:

- one Student table;
- one Lecturer table;
- one University table;
- one College table;
- one Department table;
- one Level table;
- one Course table;
- one Classroom table;
- one Schedule table;
- one Attendance table;
- one canonical StudentBiometric table;
- one canonical LecturerBiometric table.

Django migrations must be able to build the canonical database from an empty PostgreSQL database.

No application code may reference legacy users_* tables.

No biometric table may use auth_user as the domain owner.

No destructive SQL may be executed until reconciliation and backup are verified.

OUTPUT:

Produce:

A. 45-table forensic matrix
B. FK dependency graph
C. duplicate mapping matrix
D. data conflict report
E. orphan report
F. biometric reconciliation report
G. attendance reconciliation report
H. canonical ERD
I. target Django model architecture
J. migration execution plan
K. rollback plan
L. final legacy DROP list
M. test/validation SQL
N. engineering implementation prompts for each migration phase
