# EyeAttend — Database Forensics & Reconciliation Prompt

Audit the complete PostgreSQL EyeAttend database.

Classify every table as:

- CANONICAL
- LEGACY
- DJANGO_CORE
- BIOMETRIC
- AI
- AUDIT
- CONFIGURATION
- DERIVED
- DUPLICATE
- AMBIGUOUS

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

- DROP
- TRUNCATE
- blindly RENAME
- overwrite conflicting records

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

Attendance must enforce:

UNIQUE(session_id, student_id)