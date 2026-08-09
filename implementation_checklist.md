# Implementation Checklist: Frappe Schema for Everyone Can Make

## Phase 1: Core Learning (Week 1-2) ⏱️

### Doctypes to Create

- [ ] **Skill Domain**
  - Simple master data (name, description, icon)
  - Pre-populate: Electronics, Fabrication, Design, Software, Hardware Design, Mechanical Engineering
  - No dependencies

- [ ] **Lesson Objective** (Child Table)
  - Field: objective (text)
  - Field: level (Recall / Understand / Apply / Analyze / Evaluate)
  - Will be used in Lesson

- [ ] **Lesson Prerequisite** (Child Table)
  - Field: prerequisite_lesson (Link to Lesson)
  - Field: mastery_required (checkbox, default checked)
  - Self-referential link

- [ ] **Lesson Equipment** (Child Table)
  - Field: equipment_item (Link to Lab Equipment - create stub first)
  - Field: quantity_per_learner (float)
  - Field: total_session_hours (float)

- [ ] **Lesson Material** (Child Table)
  - Field: material_item (Link to Item)
  - Field: qty_per_learner (float)
  - Field: unit (Link to UOM)

- [ ] **Lesson**
  - Parent doctype combining all above child tables
  - Difficulty level: Beginner / Intermediate / Advanced
  - Lesson type: Concept / Hands-On / Challenge / Integration
  - Status workflow: Draft → Published → Archived
  - Add a custom field for `skill_domain` Link to Skill Domain
  - **CRITICAL:** Add prerequisites child table for lesson unlocking logic

### Frappe LMS Integration

- [ ] Check if Frappe LMS has native "Lesson" doctype - if yes, EXTEND it with:
  - skill_domain field
  - lesson_type field
  - prerequisites table
  - equipment/materials requirements
  - mastery_threshold field (default 80%)
  
- [ ] If no native Lesson, create our custom Lesson independent of LMS

- [ ] Create a Quiz linking doctype (if using Frappe LMS quizzes)

### Testing & Data

- [ ] Create 3-5 test lessons across different domains
- [ ] Test prerequisite relationships
- [ ] Validate child table inserts

---

## Phase 2: Resources (Week 3) 🏭

### Doctypes to Create

- [ ] **Lab Location**
  - Field: location_name (unique)
  - Field: address, phone, email
  - Child table: Location Hours (day_of_week, open_time, close_time, is_closed)
  - Field: max_simultaneous_bookings (int)

- [ ] **Lab Equipment**
  - Field: equipment_name (required)
  - Field: equipment_code / serial_number (unique)
  - Field: category (Fabrication / Electronics / Computing / Support)
  - Field: location (Link to Lab Location)
  - Field: is_bookable (checkbox, default checked)
  - Field: requires_operator_supervision (checkbox)
  - Field: max_concurrent_users (int, default 1)
  - Field: hourly_rate (currency)
  - Field: requires_training (checkbox)
  - Field: training_lesson (Link to Lesson - optional)
  - Field: status (Active / Inactive / Maintenance / Decommissioned)
  - Field: last_maintenance_date
  - Validation: Can't book if status != Active

- [ ] **Asset Lifecycle**
  - Field: equipment (Link to Lab Equipment, unique)
  - Child table: Maintenance Log (date, type, performed_by, cost, notes, next_due)
  - Field: total_hours_used (read-only, calculated)
  - Field: original_cost, depreciation_rate, current_book_value

- [ ] **Instructor Skill** (Child Table)
  - Field: skill_domain (Link to Skill Domain)
  - Field: experience_level (Beginner / Intermediate / Advanced / Expert)
  - Field: years_experience (int)

- [ ] **Instructor Certification** (Child Table)
  - Field: equipment_item (Link to Lab Equipment)
  - Field: certified_date, expiry_date
  - Field: can_certify_others (checkbox)

- [ ] **Instructor Hours** (Child Table)
  - Field: day_of_week
  - Field: start_time, end_time
  - Field: location (Link to Lab Location)

- [ ] **Instructor**
  - Field: user_link (Link to User, unique, required)
  - Field: full_name (fetch from user)
  - Field: email (fetch from user, read-only)
  - Child tables:
    - Instructor Skill (multiple)
    - Instructor Certification (multiple)
    - Instructor Hours (availability slots)
  - Field: primary_location (Link to Lab Location)
  - Field: hourly_rate (currency)
  - Field: flat_rate_per_session (currency, optional)
  - Field: status (Active / Inactive / OnLeave)
  - Validation: At least one skill domain certified

### Frappe Native Integration

- [ ] Verify Frappe Item + Warehouse setup for consumables
  - Item: represents materials (Arduino, soldering wire, plywood, etc.)
  - Warehouse: represents storage locations
  - No custom doctypes needed here - use Frappe's existing system

### Testing & Data

- [ ] Create 2 Lab Locations (main lab, branch/school)
- [ ] Create 8-10 Lab Equipment items (laser, 3D printer, soldering station, computers, etc.)
- [ ] Create 3-5 test Instructors with skills & certifications
- [ ] Test equipment availability overlapping with location hours
- [ ] Test instructor availability conflicts

---

## Phase 3: Booking System (Week 4-5) 📅

### Doctypes to Create

- [ ] **Booking Resource** (Child Table)
  - Field: equipment_item (Link to Lab Equipment)
  - Field: qty (float, default 1)
  - Field: hours_needed (float)
  - Field: hourly_rate (fetch from equipment)
  - Field: subtotal (formula: hours_needed × hourly_rate)

- [ ] **Resource Booking**
  - Field: booking_code (auto-generated, unique, read-only)
  - Field: booking_type (Individual / School, required)
  - Field: individual_user (Link to User, show_if booking_type == "Individual")
  - Field: school (Link to School, show_if booking_type == "School")
  - Field: school_contact_person (text, show_if booking_type == "School")
  - Field: school_group_size (int, show_if booking_type == "School")
  - Field: booking_purpose (Course Outcome / Free Exploration / Advanced Project / Maintenance)
  - Field: course_outcome (Link, show_if booking_purpose == "Course Outcome")
  - Child table: Booking Resource (equipment items booked)
  - Field: instructor (Link to Instructor, optional)
  - Field: instructor_hours (float, read-only, calculated from resources)
  - Field: location (Link to Lab Location, required)
  - Field: start_datetime, end_datetime (required)
  - Field: recurring (checkbox)
  - Field: recurrence_pattern (Weekly / Bi-Weekly / Monthly, show_if recurring)
  - Field: recurrence_end_date (show_if recurring)
  - Calculated Fields (all read-only):
    - equipment_cost = SUM(booking_resources.subtotal)
    - instructor_cost = instructor.hourly_rate × instructor_hours
    - total_cost = equipment_cost + instructor_cost + material_cost - (discount)
  - Field: material_cost (currency)
  - Field: discount_percent (percent)
  - Field: status (Pending / Confirmed / Completed / Cancelled, default "Pending")
  - Field: payment_status (Unpaid / Partial / Paid / Refunded, default "Unpaid")
  - Field: payment_method (Cash / Card / Bank Transfer / School Invoice)
  - Field: notes (text)

- [ ] **Instructor Session**
  - Field: session_code (auto-generated, read-only, unique)
  - Field: learner_type (Individual / School Group)
  - Field: individual_learner (Link to User)
  - Field: school_booking (Link to Resource Booking)
  - Field: instructor (Link to Instructor, required)
  - Field: start_time, end_time (required, datetime)
  - Field: duration_hours (formula: (end_time - start_time) / 3600, read-only)
  - Field: session_type (Lesson Delivery / Hands-On Guidance / Certification / Troubleshooting / Mentoring)
  - Field: lesson_covered (Link to Lesson, optional)
  - Field: location (Link to Lab Location)
  - Field: notes, learner_feedback (text)
  - Field: attendance (Present / Absent / Late)
  - Field: cost (formula: instructor.hourly_rate × duration_hours, read-only)

- [ ] **School Coordinator** (Child Table)
  - Field: user (Link to User)
  - Field: name (fetch from user)
  - Field: role (Primary Admin / Teacher / Lab Technician)
  - Field: email (fetch from user)

- [ ] **School**
  - Field: school_name (unique, required)
  - Field: school_code (unique)
  - Field: primary_contact (name)
  - Field: email, phone, address
  - Field: membership_type (Partner School / Community Org / Corporate, default "Partner School")
  - Field: membership_status (Active / Inactive / Suspended, default "Active")
  - Field: start_date, end_date (for partnership tracking)
  - Field: max_concurrent_groups (int)
  - Field: monthly_lab_budget (currency, optional)
  - Child table: School Coordinator (multiple)
  - Field: invoice_frequency (Per Booking / Weekly / Monthly / Quarterly)
  - Field: payment_terms (text, e.g. "Net 30")
  - Read-only calculated fields:
    - total_bookings (count of resource bookings)
    - total_students_served (sum of school_group_size)
    - total_spent (sum of resource booking costs)

### Availability & Conflict Checking

- [ ] **Create a Custom Script/Hook for Resource Booking validation:**
  ```python
  def validate_booking_availability(doc, method):
      # Check if equipment is available during time window
      # Check if instructor is available during time window
      # Check if lab location is open during time window
      # Flag conflicts
      # Suggest alternatives
  ```

- [ ] **Create Instructor Assignment Recommendation:**
  ```python
  def suggest_instructors(doc, method):
      # Find instructors certified for course_outcome skill domains
      # Find instructors certified for equipment in booking
      # Find instructors available at start_datetime
      # Return top 3 by rating/availability
  ```

### Testing & Data

- [ ] Create a test Individual Resource Booking (1-hour session)
  - Verify auto-cost calculation
  - Verify equipment + instructor cost sum
  - Test availability validation (should fail if equipment in use)

- [ ] Create a test School Resource Booking (recurring, 10 students)
  - Verify school_group_size sets
  - Verify recurrence pattern creates multiple sessions
  - Verify instructor assignment suggestion works

- [ ] Test payment workflows (Unpaid → Partial → Paid)

- [ ] Test cancellation (should free up equipment/instructor)

---

## Phase 4: Learner Tracking (Week 6) 📊

### Doctypes to Create

- [ ] **Learner Skill** (Child Table)
  - Field: skill_domain (Link to Skill Domain)
  - Field: proficiency_level (Novice / Beginner / Intermediate / Advanced / Expert)
  - Field: date_achieved (date)
  - Field: verified_by (Link to Instructor)

- [ ] **Lesson Completion** (Child Table)
  - Field: lesson (Link to Lesson)
  - Field: status (Not Started / In Progress / Completed / Mastered)
  - Field: start_date, completion_date
  - Field: quiz_score (percent)
  - Field: project_submission (file attachment)
  - Field: instructor_feedback (text)
  - Field: mastery_achieved (checkbox, read-only formula: quiz_score >= lesson.mastery_threshold)

- [ ] **Learner Progress**
  - Field: learner (Link to User, required, unique with course_outcome)
  - Field: full_name (fetch from learner user, read-only)
  - Field: course_outcome (Link to Course Outcome, required)
  - Field: enrollment_date (date)
  - Field: target_completion_date (date)
  - Field: status (Enrolled / In Progress / Completed / Dropped, default "Enrolled")
  - Child table: Lesson Completion (tracks each lesson in the outcome)
  - Field: overall_progress (percent, formula: completed_lessons / total_lessons * 100, read-only)
  - Field: overall_score (percent, formula: avg(lesson_completion.quiz_score), read-only)
  - Field: eligible_for_completion (checkbox, read-only formula: all prerequisites mastered + all lessons completed)
  - Child table: Learner Skill (skills acquired)

### Automation & Business Logic

- [ ] **Create Lesson Unlocking Automation:**
  ```python
  def unlock_next_lessons(doc, method):
      """
      Trigger: When Lesson Completion.mastery_achieved = True
      Action: Find all lessons that have this lesson as prerequisite
              Set their status to "Ready to Start" in learner progress
      """
  ```

- [ ] **Create Progress Dashboard Script:**
  - Show overall course progress %
  - Show per-lesson status (not started / in progress / completed / mastered)
  - Show quiz scores trend
  - Show next recommended lesson

- [ ] **Create Certificate Generation:**
  ```python
  def generate_certificate(doc, method):
      """
      Trigger: When Learner Progress.status = "Completed" 
               AND all prerequisites met
      Action: Generate PDF certificate
              Email to learner
              Store in file system
      """
  ```

### Testing & Data

- [ ] Create test Learner Progress record for a Course Outcome
- [ ] Complete first lesson, verify next lesson unlocks
- [ ] Complete second lesson but score < 80%, verify mastery not achieved
- [ ] Retry, score ≥ 80%, verify mastery achieved
- [ ] Complete all lessons, verify eligible_for_completion = True
- [ ] Test certificate generation

---

## Phase 5: Polish & Reports (Week 7-8) 📈

### Reports to Create

- [ ] **Lab Utilization Report**
  - Equipment usage % by machine
  - Hours booked vs. available
  - Trend line (week-over-week)
  - Export: CSV

- [ ] **Instructor Analytics**
  - Billable hours by instructor
  - Hourly rate × hours = revenue
  - Students trained per instructor
  - Skill domain expertise breakdown

- [ ] **Student Progress Dashboard**
  - By individual: courses in progress, completion rate, skills acquired
  - By school: aggregate progress, group completion %, cost per student
  - Filter by date range, skill domain, difficulty

- [ ] **Revenue Analysis**
  - Equipment revenue (bookings × hourly_rate)
  - Instructor revenue (sessions × hourly_rate)
  - Material revenue (consumables used)
  - School vs. Individual split

- [ ] **Equipment Maintenance Schedule**
  - Next maintenance due (per machine)
  - Last maintenance date + notes
  - Maintenance cost YTD
  - Preventive vs. corrective breakdown

- [ ] **Outcome Effectiveness Report**
  - Course outcome completion rate
  - Average learner score per outcome
  - Skills distribution across outcomes
  - Student satisfaction (if feedback collected)

### Performance Optimization

- [ ] Index frequently filtered fields (start_datetime, status, location)
- [ ] Add caching for Instructor availability queries
- [ ] Optimize Learner Progress calculations (don't recalc on every view)

### UI/UX Polish

- [ ] Test responsive layout on mobile/tablet
- [ ] User role-based access (learner sees only own progress, lab manager sees all)
- [ ] Mobile booking form for walk-ins
- [ ] Email notifications:
  - Booking confirmation
  - Lesson unlock notification
  - Completion reminder
  - Certificate ready

---

## Critical Implementation Notes

### Data Integrity

- **Cascading deletes:** Set up properly to avoid orphaned records
  - If Lesson deleted, what happens to Course Outcomes? (Keep outcome, remove lesson from it)
  - If Equipment deleted, what happens to bookings? (Prevent if booking exists)

- **Unique constraints:** Enforce at DB level
  - Lesson: lesson_code (unique)
  - Lab Equipment: equipment_code (unique)
  - Instructor: user_link (unique, 1-to-1)
  - School: school_name (unique)
  - Learner Progress: (learner, course_outcome) combo unique

### Calculated Fields

- Don't over-rely on formulas - use script for complex calcs
- Example: `total_cost` in Resource Booking should be a Script Report, not a formula field
- Refresh calculated fields on child table save

### Permissions

```
Role: Learner
  Lesson: Read only
  Learner Progress: Read own record only
  Resource Booking: Read own bookings
  Quiz: Read + Submit answers

Role: Instructor
  Lab Equipment: Read all
  Instructor Session: Create + Read all
  Lesson Completion: Update (add feedback)
  Learner Progress: Read (students they teach)

Role: Lab Manager
  All doctypes: Full CRUD
  Except: Cannot delete published lessons

Role: School Admin
  School: Update own school only
  Resource Booking: Create/Read/Update for own school
  Learner Progress: Read students in own school
  Instructor: Read available instructors only

Role: Admin
  Full access
```

---

## Deployment Checklist

- [ ] Backup existing Frappe instance
- [ ] Create doctypes in dev environment first
- [ ] Test all automations & validations
- [ ] Load test data (100+ lessons, 50+ instructors, 1000+ bookings)
- [ ] Performance test (report generation, booking search)
- [ ] UAT with 5-10 power users (instructors, lab manager, school admin)
- [ ] Fix bugs found in UAT
- [ ] Migrate to production
- [ ] Train users (video + docs)
- [ ] Go-live support (first week, daily check-ins)

---

## Success Metrics

✅ **Core Learning:**
- Can create lessons, link prerequisites, compose into outcomes
- Lesson unlock triggers when prerequisite mastered

✅ **Resource Management:**
- All equipment tracked, maintenance logged
- Instructor availability visible, no double-booking

✅ **Booking:**
- Individuals can book equipment + instructors
- Schools can book recurring sessions for groups
- Costs auto-calculated, invoicing works

✅ **Learner Tracking:**
- Progress visible, next steps clear
- Certificates auto-generated on completion

✅ **Reporting:**
- Lab utilization clear
- Revenue tracked by equipment + instructor + materials
- School performance visible (cost/student, completion %)
