# Frappe LMS Schema Design: Everyone Can Make + FabInABox

**Author:** Murtaza (makermurtaza@bitandbrick.com)  
**Date:** August 2026  
**Last Updated:** August 11, 2026 (Phase 1 simplification)  
**Model:** FabInABox (Individual + School Booking)

**⚠️ Note (August 11, 2026):** Phase 1 Learning Architecture simplified. Removed child table doctypes (Lesson Objective, Lesson Prerequisite, Lesson Equipment, Lesson Material, Outcome Lesson). Atomic Learning now serves as a thin metadata wrapper over LMS's Course Lesson. Skills tracked via Course Outcome → Outcome Skill.

---

## Executive Summary

This schema enables:
- **Modular lessons** that compose into multiple course outcomes
- **Skill tracking** with prerequisite unlocking
- **Resource booking** (equipment, instructor time) for individuals & schools
- **Inventory management** with asset tracking
- **Multi-tenant capability** for school vs. personal learner paths

---

## Part 1: Learning Architecture

### 1.1 Core Learning Doctype: `Atomic Learning` (Lesson Wrapper)

**Purpose:** Thin wrapper around LMS's `Course Lesson` that adds FabInABox-specific metadata tracking.

**Note (August 2026):** Simplified to core metadata only. Child tables (Lesson Objective, Prerequisite, Equipment, Material) removed to streamline the data model. Skills are now tracked at the Course Outcome level via Outcome Skill.

```python
{
  "doctype": "Atomic Learning",
  "fields": [
    # Core Reference
    {"fieldname": "lesson_title", "fieldtype": "Link", "label": "Course Lesson", "options": "Course Lesson", "reqd": 1},
    # Links to native Frappe LMS Course Lesson
    
    # Metadata
    {"fieldname": "lesson_code", "fieldtype": "Data", "label": "Lesson Code", "reqd": 1, "unique": 1},
    {"fieldname": "description", "fieldtype": "Text", "label": "Description"},
    
    # Duration
    {"fieldname": "duration_section", "fieldtype": "Section Break", "label": "Duration"},
    {"fieldname": "duration_hours", "fieldtype": "Int", "label": "Duration (hours)", "default": 1},
    {"fieldname": "duration_minutes", "fieldtype": "Int", "label": "Duration (minutes)", "default": 0},
    
    # Level
    {"fieldname": "difficulty_level", "fieldtype": "Select", "options": "\nBeginner\nIntermediate\nAdvanced"},
    
    # Status
    {"fieldname": "status_section", "fieldtype": "Section Break", "label": "Status"},
    {"fieldname": "status", "fieldtype": "Select", "options": "\nDraft\nPublished\nArchived", "default": "Draft"},
  ]
}
```

---

### 1.2 Skill Domain Doctype: `Skill Domain`

**Purpose:** Organize lessons by domain (Electronics, Fabrication, Design, Software).

```python
{
  "doctype": "Skill Domain",
  "fields": [
    {"fieldname": "domain_name", "fieldtype": "Data", "label": "Domain Name", "reqd": 1, "unique": 1},
    # e.g., "Electronics", "Digital Fabrication", "Design Thinking", "Software"
    {"fieldname": "description", "fieldtype": "Text", "label": "Description"},
    {"fieldname": "icon", "fieldtype": "Data", "label": "Icon (emoji or FontAwesome)"},
  ]
}
```

**Master data:**
```
- Electronics (sensors, Arduino, circuits, soldering)
- Digital Fabrication (laser, CNC, 3D printing)
- Design (CAD, sketching, ideation, user research)
- Software (Python, JavaScript, embedded systems)
- Hardware Design (PCB, schematics, prototyping)
- Mechanical Engineering (CAM, structural design)
```

---

### 1.3 Course Outcome Doctype: `Course Outcome`

**Purpose:** Define a specific skill/project learners achieve. Composes multiple lessons.

```python
{
  "doctype": "Course Outcome",
  "fields": [
    # Basic Info
    {"fieldname": "outcome_title", "fieldtype": "Data", "label": "Outcome Title", "reqd": 1},
    # e.g., "Build a Smart Garden Monitor"
    
    {"fieldname": "outcome_code", "fieldtype": "Data", "label": "Code", "unique": 1},
    # e.g., "ECO-001"
    
    {"fieldname": "description", "fieldtype": "Text", "label": "What learners will do"},
    {"fieldname": "learning_outcomes_text", "fieldtype": "Text", "label": "Learning Outcomes (bullet list)"},
    
    # Metadata
    {"fieldname": "difficulty_level", "fieldtype": "Select", "options": "Beginner\nIntermediate\nAdvanced"},
    {"fieldname": "total_hours", "fieldtype": "Float", "label": "Total Duration (hours)", "read_only": 1},
    # Auto-calculated from lessons
    
    {"fieldname": "target_audience", "fieldtype": "Data", "label": "Target: Individual/School/Both"},
    
    # Skills Developed
    {"fieldname": "skills_section", "fieldtype": "Section Break", "label": "Skills Developed"},
    {"fieldname": "skill_domains", "fieldtype": "Table", "label": "Skill Domains Covered", "childtable": "Outcome Skill"},
    
    # Resources Required
    {"fieldname": "resources_section", "fieldtype": "Section Break", "label": "Resource Requirements"},
    {"fieldname": "total_instructor_hours", "fieldtype": "Float", "read_only": 1},
    {"fieldname": "equipment_summary", "fieldtype": "Table", "label": "Equipment Needed", "childtable": "Outcome Equipment"},
    
    # Status
    {"fieldname": "status", "fieldtype": "Select", "options": "Draft\nPublished\nArchived"},
    {"fieldname": "created_by", "fieldtype": "Link", "options": "User"},
  ]
}
```

**Child Table: `Outcome Skill`**
```python
{
  "doctype": "Outcome Skill",
  "fields": [
    {"fieldname": "skill_domain", "fieldtype": "Link", "options": "Skill Domain"},
    {"fieldname": "mastery_level", "fieldtype": "Select", "options": "Foundational\nIntermediatePractitioner\nAdvanced"},
  ]
}
```

**Child Table: `Outcome Equipment`**
```python
{
  "doctype": "Outcome Equipment",
  "fields": [
    {"fieldname": "equipment_item", "fieldtype": "Link", "options": "Lab Equipment"},
    {"fieldname": "total_hours_needed", "fieldtype": "Float"},
    {"fieldname": "notes", "fieldtype": "Small Text"},
  ]
}
```

---

## Part 2: Resource Management (FabInABox Model)

### 2.1 Lab Equipment Doctype: `Lab Equipment`

**Purpose:** Track physical equipment in your lab/makerspace.

```python
{
  "doctype": "Lab Equipment",
  "fields": [
    # Basic Info
    {"fieldname": "equipment_name", "fieldtype": "Data", "label": "Equipment Name", "reqd": 1},
    # e.g., "Laser Cutter - Trotec Speedy 100"
    
    {"fieldname": "equipment_code", "fieldtype": "Data", "label": "Asset Tag/Serial", "unique": 1},
    {"fieldname": "category", "fieldtype": "Select", "options": "Fabrication\nElectronics\nComputing\nSupport", "reqd": 1},
    
    # Physical Details
    {"fieldname": "details_section", "fieldtype": "Section Break", "label": "Details"},
    {"fieldname": "description", "fieldtype": "Text"},
    {"fieldname": "manufacturer", "fieldtype": "Data"},
    {"fieldname": "model", "fieldtype": "Data"},
    {"fieldname": "serial_number", "fieldtype": "Data"},
    
    # Operational
    {"fieldname": "operations_section", "fieldtype": "Section Break", "label": "Operations"},
    {"fieldname": "location", "fieldtype": "Link", "options": "Lab Location", "reqd": 1},
    # FabInABox may have multiple locations
    
    {"fieldname": "acquisition_date", "fieldtype": "Date"},
    {"fieldname": "warranty_expiry", "fieldtype": "Date"},
    {"fieldname": "maintenance_schedule", "fieldtype": "Select", "options": "Weekly\nMonthly\nQuarterly\nAnnually"},
    
    # Booking & Availability
    {"fieldname": "booking_section", "fieldtype": "Section Break", "label": "Booking"},
    {"fieldname": "is_bookable", "fieldtype": "Check", "default": 1, "label": "Available for Booking"},
    {"fieldname": "requires_operator_supervision", "fieldtype": "Check", "label": "Requires Certified Operator"},
    {"fieldname": "max_concurrent_users", "fieldtype": "Int", "default": 1, "label": "Max Users Simultaneously"},
    {"fieldname": "hourly_rate", "fieldtype": "Currency", "label": "Booking Rate/Hour"},
    
    # Certification
    {"fieldname": "certification_section", "fieldtype": "Section Break", "label": "Certification"},
    {"fieldname": "requires_training", "fieldtype": "Check", "label": "Requires Safety Training"},
    {"fieldname": "training_lesson", "fieldtype": "Link", "options": "Lesson", "label": "Linked Training Lesson"},
    
    # Status & Maintenance
    {"fieldname": "status_section", "fieldtype": "Section Break", "label": "Status"},
    {"fieldname": "status", "fieldtype": "Select", "options": "Active\nInactive\nMaintenance\nDecommissioned", "default": "Active"},
    {"fieldname": "last_maintenance_date", "fieldtype": "Date"},
    {"fieldname": "maintenance_notes", "fieldtype": "Text"},
  ]
}
```

---

### 2.2 Lab Location Doctype: `Lab Location`

**Purpose:** Support multi-location FabInABox deployments (school branches, community labs).

```python
{
  "doctype": "Lab Location",
  "fields": [
    {"fieldname": "location_name", "fieldtype": "Data", "label": "Location Name", "reqd": 1, "unique": 1},
    # e.g., "Main Lab", "School Branch A", "Community Center Lab"
    
    {"fieldname": "address", "fieldtype": "Text"},
    {"fieldname": "contact_phone", "fieldtype": "Data"},
    {"fieldname": "contact_email", "fieldtype": "Data"},
    
    {"fieldname": "operating_hours", "fieldtype": "Table", "label": "Operating Hours", "childtable": "Location Hours"},
    {"fieldname": "max_simultaneous_bookings", "fieldtype": "Int", "label": "Max Concurrent Learner Sessions"},
    
    {"fieldname": "lat_long", "fieldtype": "Data", "label": "Coordinates (lat,long)"},
  ]
}
```

**Child Table: `Location Hours`**
```python
{
  "doctype": "Location Hours",
  "fields": [
    {"fieldname": "day_of_week", "fieldtype": "Select", "options": "Monday\nTuesday\nWednesday\nThursday\nFriday\nSaturday\nSunday"},
    {"fieldname": "open_time", "fieldtype": "Time"},
    {"fieldname": "close_time", "fieldtype": "Time"},
    {"fieldname": "is_closed", "fieldtype": "Check"},
  ]
}
```

---

### 2.3 Instructor Doctype: `Instructor`

**Purpose:** Manage lab instructors/facilitators for booking.

```python
{
  "doctype": "Instructor",
  "fields": [
    # Basic Info
    {"fieldname": "user_link", "fieldtype": "Link", "options": "User", "reqd": 1, "unique": 1},
    {"fieldname": "full_name", "fieldtype": "Data", "label": "Full Name", "fetch_from": "user_link.full_name"},
    {"fieldname": "email", "fieldtype": "Data", "fetch_from": "user_link.email", "read_only": 1},
    
    # Expertise
    {"fieldname": "expertise_section", "fieldtype": "Section Break", "label": "Expertise"},
    {"fieldname": "skill_domains", "fieldtype": "Table", "label": "Certified Domains", "childtable": "Instructor Skill"},
    {"fieldname": "equipment_certifications", "fieldtype": "Table", "label": "Equipment Certifications", "childtable": "Instructor Certification"},
    
    # Availability
    {"fieldname": "availability_section", "fieldtype": "Section Break", "label": "Availability"},
    {"fieldname": "primary_location", "fieldtype": "Link", "options": "Lab Location"},
    {"fieldname": "available_hours", "fieldtype": "Table", "label": "Availability Slots", "childtable": "Instructor Hours"},
    
    # Rates
    {"fieldname": "billing_section", "fieldtype": "Section Break", "label": "Billing"},
    {"fieldname": "hourly_rate", "fieldtype": "Currency", "label": "Consulting Rate/Hour"},
    {"fieldname": "flat_rate_per_session", "fieldtype": "Currency", "label": "Flat Rate/Session (optional)"},
    
    # Status
    {"fieldname": "status", "fieldtype": "Select", "options": "Active\nInactive\nOnLeave"},
  ]
}
```

**Child Table: `Instructor Skill`**
```python
{
  "doctype": "Instructor Skill",
  "fields": [
    {"fieldname": "skill_domain", "fieldtype": "Link", "options": "Skill Domain"},
    {"fieldname": "experience_level", "fieldtype": "Select", "options": "Beginner\nIntermediate\nAdvanced\nExpert"},
    {"fieldname": "years_experience", "fieldtype": "Int"},
  ]
}
```

**Child Table: `Instructor Certification`**
```python
{
  "doctype": "Instructor Certification",
  "fields": [
    {"fieldname": "equipment_item", "fieldtype": "Link", "options": "Lab Equipment"},
    {"fieldname": "certified_date", "fieldtype": "Date"},
    {"fieldname": "expiry_date", "fieldtype": "Date"},
    {"fieldname": "can_certify_others", "fieldtype": "Check", "label": "Can Certify Other Instructors"},
  ]
}
```

**Child Table: `Instructor Hours`**
```python
{
  "doctype": "Instructor Hours",
  "fields": [
    {"fieldname": "day_of_week", "fieldtype": "Select", "options": "Monday\nTuesday\nWednesday\nThursday\nFriday\nSaturday\nSunday"},
    {"fieldname": "start_time", "fieldtype": "Time"},
    {"fieldname": "end_time", "fieldtype": "Time"},
    {"fieldname": "location", "fieldtype": "Link", "options": "Lab Location"},
  ]
}
```

---

## Part 3: Inventory & Asset Management

### 3.1 Consumable Materials Inventory

**Leverage Frappe's native `Item` + `Warehouse` system:**

```python
# Create Items in Frappe's standard Item doctype
Items:
  - Arduino Uno (Electronics)
  - Soldering Wire (Electronics)
  - Plywood 3mm Sheet (Fabrication)
  - Acrylic Cast 3mm (Fabrication)
  - etc.

Warehouses:
  - Main Lab Storage
  - Fabrication Lab
  - Electronics Bench
  - School Branch A Storage
```

**Track stock via Stock Entry & Bin doctypes** (Frappe standard).

---

### 3.2 Asset Management Extension: `Asset Lifecycle`

**Purpose:** Track maintenance, depreciation, and lifecycle of expensive equipment.

```python
{
  "doctype": "Asset Lifecycle",
  "fields": [
    {"fieldname": "equipment", "fieldtype": "Link", "options": "Lab Equipment", "reqd": 1, "unique": 1},
    
    {"fieldname": "maintenance_section", "fieldtype": "Section Break", "label": "Maintenance"},
    {"fieldname": "maintenance_logs", "fieldtype": "Table", "label": "Maintenance History", "childtable": "Maintenance Log"},
    
    {"fieldname": "usage_section", "fieldtype": "Section Break", "label": "Usage Tracking"},
    {"fieldname": "total_hours_used", "fieldtype": "Float", "read_only": 1},
    {"fieldname": "total_sessions", "fieldtype": "Int", "read_only": 1},
    
    {"fieldname": "cost_section", "fieldtype": "Section Break", "label": "Financial"},
    {"fieldname": "original_cost", "fieldtype": "Currency"},
    {"fieldname": "depreciation_rate", "fieldtype": "Percent"},
    {"fieldname": "current_book_value", "fieldtype": "Currency", "read_only": 1},
  ]
}
```

**Child Table: `Maintenance Log`**
```python
{
  "doctype": "Maintenance Log",
  "fields": [
    {"fieldname": "maintenance_date", "fieldtype": "Date"},
    {"fieldname": "type", "fieldtype": "Select", "options": "Preventive\nCorrective\nCalibration"},
    {"fieldname": "performed_by", "fieldtype": "Link", "options": "Instructor"},
    {"fieldname": "cost", "fieldtype": "Currency"},
    {"fieldname": "notes", "fieldtype": "Text"},
    {"fieldname": "next_maintenance_due", "fieldtype": "Date"},
  ]
}
```

---

## Part 4: Booking & Reservation System

### 4.1 Resource Booking Doctype: `Resource Booking`

**Purpose:** Handle both individual and school bookings for equipment, lab time, and instructor sessions.

```python
{
  "doctype": "Resource Booking",
  "fields": [
    # Booking Identity
    {"fieldname": "booking_code", "fieldtype": "Data", "label": "Booking Reference", "read_only": 1, "unique": 1},
    # Auto-generated: RB-2026-08-001
    
    # Bookee Info
    {"fieldname": "bookee_section", "fieldtype": "Section Break", "label": "Who is Booking"},
    {"fieldname": "booking_type", "fieldtype": "Select", "options": "Individual\nSchool", "reqd": 1},
    
    {"fieldname": "individual_user", "fieldtype": "Link", "options": "User", "label": "Individual Learner", "depends_on": "eval: doc.booking_type == 'Individual'"},
    {"fieldname": "school", "fieldtype": "Link", "options": "School", "label": "School/Organization", "depends_on": "eval: doc.booking_type == 'School'"},
    {"fieldname": "school_contact_person", "fieldtype": "Data", "label": "Teacher/Coordinator Name", "depends_on": "eval: doc.booking_type == 'School'"},
    {"fieldname": "school_group_size", "fieldtype": "Int", "label": "Number of Students", "depends_on": "eval: doc.booking_type == 'School'"},
    
    # What is Being Booked
    {"fieldname": "resource_section", "fieldtype": "Section Break", "label": "Resources Booked"},
    {"fieldname": "booking_purpose", "fieldtype": "Select", "options": "Course Outcome\nFree Exploration\nAdvanced Project\nMaintenance", "reqd": 1},
    {"fieldname": "course_outcome", "fieldtype": "Link", "options": "Course Outcome", "depends_on": "eval: doc.booking_purpose == 'Course Outcome'"},
    
    {"fieldname": "resources", "fieldtype": "Table", "label": "Equipment & Time Booked", "childtable": "Booking Resource"},
    
    # Instructor
    {"fieldname": "instructor_section", "fieldtype": "Section Break", "label": "Instructor Assignment"},
    {"fieldname": "instructor", "fieldtype": "Link", "options": "Instructor", "label": "Assigned Instructor"},
    {"fieldname": "instructor_hours", "fieldtype": "Float", "label": "Instructor Time (hours)", "read_only": 1},
    # Auto-calculated from resources
    
    # Timing
    {"fieldname": "timing_section", "fieldtype": "Section Break", "label": "Schedule"},
    {"fieldname": "location", "fieldtype": "Link", "options": "Lab Location", "reqd": 1},
    {"fieldname": "start_datetime", "fieldtype": "Datetime", "reqd": 1},
    {"fieldname": "end_datetime", "fieldtype": "Datetime", "reqd": 1},
    {"fieldname": "recurring", "fieldtype": "Check", "label": "Recurring Booking"},
    {"fieldname": "recurrence_pattern", "fieldtype": "Select", "options": "Weekly\nBi-Weekly\nMonthly", "depends_on": "eval: doc.recurring == 1"},
    {"fieldname": "recurrence_end_date", "fieldtype": "Date", "depends_on": "eval: doc.recurring == 1"},
    
    # Pricing & Payment
    {"fieldname": "pricing_section", "fieldtype": "Section Break", "label": "Pricing"},
    {"fieldname": "equipment_cost", "fieldtype": "Currency", "read_only": 1},
    # Sum of equipment hourly rates
    
    {"fieldname": "instructor_cost", "fieldtype": "Currency", "read_only": 1},
    # instructor.hourly_rate × instructor_hours
    
    {"fieldname": "material_cost", "fieldtype": "Currency", "label": "Materials/Consumables Cost"},
    {"fieldname": "discount_percent", "fieldtype": "Percent", "label": "Discount (if any)"},
    {"fieldname": "total_cost", "fieldtype": "Currency", "read_only": 1},
    
    # Payment & Status
    {"fieldname": "status_section", "fieldtype": "Section Break", "label": "Status & Payment"},
    {"fieldname": "status", "fieldtype": "Select", "options": "Pending\nConfirmed\nCompleted\nCancelled", "default": "Pending"},
    {"fieldname": "payment_status", "fieldtype": "Select", "options": "Unpaid\nPartial\nPaid\nRefunded", "default": "Unpaid"},
    {"fieldname": "payment_method", "fieldtype": "Select", "options": "Cash\nCard\nBank Transfer\nSchool Invoice"},
    
    {"fieldname": "notes", "fieldtype": "Text", "label": "Special Requirements / Notes"},
    
    # Tracking
    {"fieldname": "meta_section", "fieldtype": "Section Break", "label": "Meta"},
    {"fieldname": "created_date", "fieldtype": "Date", "read_only": 1},
    {"fieldname": "booking_made_by", "fieldtype": "Link", "options": "User", "read_only": 1},
  ]
}
```

**Child Table: `Booking Resource`**
```python
{
  "doctype": "Booking Resource",
  "fields": [
    {"fieldname": "equipment_item", "fieldtype": "Link", "options": "Lab Equipment"},
    {"fieldname": "qty", "fieldtype": "Float", "default": 1},
    {"fieldname": "hours_needed", "fieldtype": "Float", "label": "Machine Time (hours)"},
    {"fieldname": "hourly_rate", "fieldtype": "Currency", "fetch_from": "equipment_item.hourly_rate"},
    {"fieldname": "subtotal", "fieldtype": "Currency", "read_only": 1},
  ]
}
```

---

### 4.2 Instructor Booking Doctype: `Instructor Session`

**Purpose:** Book instructor time separately from equipment (for tutoring, guidance, certification).

```python
{
  "doctype": "Instructor Session",
  "fields": [
    {"fieldname": "session_code", "fieldtype": "Data", "unique": 1, "read_only": 1},
    
    # Learner Info
    {"fieldname": "learner_type", "fieldtype": "Select", "options": "Individual\nSchool Group"},
    {"fieldname": "individual_learner", "fieldtype": "Link", "options": "User"},
    {"fieldname": "school_booking", "fieldtype": "Link", "options": "Resource Booking"},
    
    # Instructor & Timing
    {"fieldname": "instructor", "fieldtype": "Link", "options": "Instructor", "reqd": 1},
    {"fieldname": "start_time", "fieldtype": "Datetime", "reqd": 1},
    {"fieldname": "end_time", "fieldtype": "Datetime", "reqd": 1},
    {"fieldname": "duration_hours", "fieldtype": "Float", "read_only": 1},
    
    # Session Details
    {"fieldname": "session_type", "fieldtype": "Select", "options": "Lesson Delivery\nHands-On Guidance\nCertification\nTroubleshooting\nAdvanced Mentoring"},
    {"fieldname": "lesson_covered", "fieldtype": "Link", "options": "Lesson"},
    {"fieldname": "location", "fieldtype": "Link", "options": "Lab Location"},
    
    # Notes & Outcomes
    {"fieldname": "notes", "fieldtype": "Text"},
    {"fieldname": "learner_feedback", "fieldtype": "Text"},
    {"fieldname": "attendance", "fieldtype": "Select", "options": "Present\nAbsent\nLate"},
    
    # Billing
    {"fieldname": "cost", "fieldtype": "Currency", "read_only": 1},
    # = instructor.hourly_rate × duration_hours
  ]
}
```

---

### 4.3 School/Organization Doctype: `School`

**Purpose:** Manage institutional bookings, group memberships, and invoicing.

```python
{
  "doctype": "School",
  "fields": [
    {"fieldname": "school_name", "fieldtype": "Data", "label": "School/Organization Name", "reqd": 1, "unique": 1},
    {"fieldname": "school_code", "fieldtype": "Data", "unique": 1},
    
    # Contact & Admin
    {"fieldname": "admin_section", "fieldtype": "Section Break", "label": "Administration"},
    {"fieldname": "primary_contact", "fieldtype": "Data", "label": "Admin Name"},
    {"fieldname": "email", "fieldtype": "Data"},
    {"fieldname": "phone", "fieldtype": "Data"},
    {"fieldname": "address", "fieldtype": "Text"},
    
    # Membership & Access
    {"fieldname": "membership_section", "fieldtype": "Section Break", "label": "Membership"},
    {"fieldname": "membership_type", "fieldtype": "Select", "options": "Partner School\nCommunity Organization\nCorporate", "default": "Partner School"},
    {"fieldname": "membership_status", "fieldtype": "Select", "options": "Active\nInactive\nSuspended", "default": "Active"},
    {"fieldname": "start_date", "fieldtype": "Date"},
    {"fieldname": "end_date", "fieldtype": "Date"},
    
    {"fieldname": "max_concurrent_groups", "fieldtype": "Int", "label": "Max Concurrent Student Groups"},
    {"fieldname": "monthly_lab_budget", "fieldtype": "Currency", "label": "Monthly Budget (optional)"},
    
    # Teachers / Coordinators
    {"fieldname": "staff_section", "fieldtype": "Section Break", "label": "Staff"},
    {"fieldname": "coordinators", "fieldtype": "Table", "label": "Lab Coordinators/Teachers", "childtable": "School Coordinator"},
    
    # Bookings History (Read-Only)
    {"fieldname": "bookings_section", "fieldtype": "Section Break", "label": "Booking Summary"},
    {"fieldname": "total_bookings", "fieldtype": "Int", "read_only": 1},
    {"fieldname": "total_students_served", "fieldtype": "Int", "read_only": 1},
    {"fieldname": "total_spent", "fieldtype": "Currency", "read_only": 1},
    
    # Invoicing
    {"fieldname": "billing_section", "fieldtype": "Section Break", "label": "Billing"},
    {"fieldname": "invoice_frequency", "fieldtype": "Select", "options": "Per Booking\nWeekly\nMonthly\nQuarterly"},
    {"fieldname": "payment_terms", "fieldtype": "Data", "label": "Payment Terms (e.g., Net 30)"},
  ]
}
```

**Child Table: `School Coordinator`**
```python
{
  "doctype": "School Coordinator",
  "fields": [
    {"fieldname": "user", "fieldtype": "Link", "options": "User"},
    {"fieldname": "name", "fieldtype": "Data", "fetch_from": "user.full_name"},
    {"fieldname": "role", "fieldtype": "Select", "options": "Primary Admin\nTeacher\nLab Technician"},
    {"fieldname": "email", "fieldtype": "Data", "fetch_from": "user.email"},
  ]
}
```

---

## Part 5: Learner Progress & Skill Tracking

### 5.1 Learner Progress Doctype: `Learner Progress`

**Purpose:** Track individual learner progress through lessons and course outcomes.

```python
{
  "doctype": "Learner Progress",
  "fields": [
    {"fieldname": "learner", "fieldtype": "Link", "options": "User", "reqd": 1},
    {"fieldname": "full_name", "fieldtype": "Data", "fetch_from": "learner.full_name", "read_only": 1},
    
    # Learning Path
    {"fieldname": "path_section", "fieldtype": "Section Break", "label": "Learning Path"},
    {"fieldname": "course_outcome", "fieldtype": "Link", "options": "Course Outcome", "reqd": 1},
    {"fieldname": "enrollment_date", "fieldtype": "Date"},
    {"fieldname": "target_completion_date", "fieldtype": "Date"},
    {"fieldname": "status", "fieldtype": "Select", "options": "Enrolled\nIn Progress\nCompleted\nDropped", "default": "Enrolled"},
    
    # Lesson Progress
    {"fieldname": "lesson_progress_section", "fieldtype": "Section Break", "label": "Lesson Progress"},
    {"fieldname": "lesson_completions", "fieldtype": "Table", "label": "Lessons", "childtable": "Lesson Completion"},
    
    # Overall Score
    {"fieldname": "score_section", "fieldtype": "Section Break", "label": "Performance"},
    {"fieldname": "overall_progress", "fieldtype": "Percent", "read_only": 1},
    {"fieldname": "overall_score", "fieldtype": "Percent", "read_only": 1},
    {"fieldname": "eligible_for_completion", "fieldtype": "Check", "read_only": 1},
    # True if all prerequisites mastered
    
    # Skills Demonstrated
    {"fieldname": "skills_section", "fieldtype": "Section Break", "label": "Skills Demonstrated"},
    {"fieldname": "skills_acquired", "fieldtype": "Table", "label": "Skills", "childtable": "Learner Skill"},
  ]
}
```

**Child Table: `Lesson Completion`**
```python
{
  "doctype": "Lesson Completion",
  "fields": [
    {"fieldname": "lesson", "fieldtype": "Link", "options": "Lesson"},
    {"fieldname": "status", "fieldtype": "Select", "options": "Not Started\nIn Progress\nCompleted\nMastered"},
    {"fieldname": "start_date", "fieldtype": "Date"},
    {"fieldname": "completion_date", "fieldtype": "Date"},
    {"fieldname": "quiz_score", "fieldtype": "Percent"},
    {"fieldname": "project_submission", "fieldtype": "Attach", "label": "Project Deliverable"},
    {"fieldname": "instructor_feedback", "fieldtype": "Text"},
    {"fieldname": "mastery_achieved", "fieldtype": "Check"},
  ]
}
```

**Child Table: `Learner Skill`**
```python
{
  "doctype": "Learner Skill",
  "fields": [
    {"fieldname": "skill_domain", "fieldtype": "Link", "options": "Skill Domain"},
    {"fieldname": "proficiency_level", "fieldtype": "Select", "options": "Novice\nBeginner\nIntermediate\nAdvanced\nExpert"},
    {"fieldname": "date_achieved", "fieldtype": "Date"},
    {"fieldname": "verified_by", "fieldtype": "Link", "options": "Instructor"},
  ]
}
```

---

## Part 6: Automation & Workflow

### 6.1 Key Automations to Implement

**Lesson Unlocking:**
```
Trigger: When Learner Completion.mastery_achieved = True for a lesson
Action: Find all lessons that have this lesson as prerequisite
        Set status to "Ready to Start" for learner
```

**Resource Availability:**
```
Trigger: On Resource Booking creation
Action: Check Lab Equipment availability for time window
        If conflict, suggest alternative times/equipment
        If available, reserve the equipment
```

**Instructor Assignment Recommendation:**
```
Trigger: On Resource Booking creation
Action: Find instructors certified for:
        - Skill domains of the course outcome
        - Equipment in the booking
        - Available during the booking time
        - Recommend top 3 by rating/availability
```

**Inventory Alert:**
```
Trigger: Stock level falls below reorder point
Action: Create Purchase Request
        Notify lab manager
```

**Completion Certificate:**
```
Trigger: Learner Progress.status = "Completed" & all prerequisites met
Action: Generate digital certificate
        Email to learner
```

---

## Part 7: Reporting & Analytics

### 7.1 Key Reports to Build

```
Reports needed:
1. Lab Utilization Rate (equipment usage % over time)
2. Instructor Availability & Billing
3. Student Progress Dashboard (by individual/school)
4. Revenue Analysis (bookings, materials, instructor time)
5. Equipment Maintenance Schedule
6. Skills Distribution (which skills taught most)
7. Course Outcome Completion Rate
8. School Partnership Performance
```

---

## Part 8: ER Diagram (Relationships)

```
┌─────────────────────────────────────────────────────────────┐
│                    LEARNING LAYER                            │
└─────────────────────────────────────────────────────────────┘

Skill Domain
  ├── Outcome Skill (many) [defined in outcomes]
  └── Instructor Skill (many) [certifies]

Atomic Learning (Lesson Wrapper)
  └── Course Lesson (LMS) [wraps]

Course Outcome
  ├── Outcome Skill (many) [requires Skill Domains]
  └── Learner Progress (many) [tracks progress]

Learner Progress
  ├── Lesson Completion (many) [tracks each lesson]
  └── Learner Skill (many) [demonstrates mastery]

┌─────────────────────────────────────────────────────────────┐
│                   RESOURCE LAYER                             │
└─────────────────────────────────────────────────────────────┘

Lab Location
  ├── Lab Equipment (many) [located at]
  ├── Location Hours (hours of operation)
  └── Resource Booking (many) [booked at]

Lab Equipment
  ├── Asset Lifecycle (1-1) [tracked by]
  ├── Resource Booking (booked in)
  └── Instructor Certification (trains on)

Instructor
  ├── Instructor Skill (many) [certified in]
  ├── Instructor Certification (many) [equipment]
  ├── Instructor Hours (availability)
  ├── Resource Booking (1-1) [assigned to]
  └── Instructor Session (many) [conducts]

Instructor Session
  ├── Learner / School Booking
  └── Instructor (1-1)

┌─────────────────────────────────────────────────────────────┐
│                   BOOKING LAYER                              │
└─────────────────────────────────────────────────────────────┘

Resource Booking
  ├── Individual User OR School [polymorphic]
  ├── Course Outcome [optional]
  ├── Booking Resource (many) [equipment items]
  ├── Lab Location
  └── Instructor

School
  ├── School Coordinator (many)
  └── Resource Booking (many)
```

---

## Part 9: Implementation Roadmap

### Phase 1: Core Learning (Week 1-2)  [COMPLETED - SIMPLIFIED Aug 2026]
- [x] Create `Skill Domain` (master data)
- [x] Create `Atomic Learning` (LMS wrapper)
- [x] Create `Course Outcome` with Outcome Skill child table
- [x] Build Frappe LMS integration (Course Lesson → Atomic Learning)

### Phase 2: Resources (Week 3)
- [ ] Create `Lab Equipment` & `Lab Location`
- [ ] Create `Instructor` + certifications
- [ ] Setup consumables via native Item/Warehouse

### Phase 3: Booking System (Week 4-5)
- [ ] Create `Resource Booking` + workflow
- [ ] Create `Instructor Session`
- [ ] Create `School` doctype
- [ ] Build availability checking logic

### Phase 4: Learner Tracking (Week 6)
- [ ] Create `Learner Progress` & `Lesson Completion`
- [ ] Implement prerequisite unlocking automation
- [ ] Build progress dashboard

### Phase 5: Polish & Reports (Week 7-8)
- [ ] Build key reports
- [ ] Optimize performance
- [ ] User testing with pilot group

---

## Part 10: Security & Permissions

```
Role: Learner
  - Can view: Own Learner Progress, enrolled Course Outcomes
  - Can book: Resources (with approval workflow)
  - Cannot: Edit lesson content, admin functions

Role: Instructor
  - Can view: All Lab Equipment, Students in their sessions
  - Can edit: Lesson Completion feedback
  - Can manage: Instructor Session records

Role: Lab Manager
  - Can view: All bookings, equipment, inventory
  - Can edit: Lab Equipment, Location settings
  - Can manage: Equipment maintenance, instructor assignments

Role: School Admin
  - Can view: Own school's bookings, student progress
  - Can manage: School group bookings, coordinator access
  - Cannot: Edit lessons or other school data

Role: Admin
  - Full access to all data
```

---

## Part 11: API Integrations Needed

```
1. Payment Gateway (Stripe/PayPal)
   - Process payments for bookings
   - Generate invoices for schools

2. Email Service (SendGrid/Postmark)
   - Booking confirmations
   - Completion certificates
   - Reminder emails

3. Calendar Sync (Google Calendar/Outlook)
   - Sync instructor availability
   - Share booking calendars

4. Analytics (Mixpanel/Amplitude)
   - Track learner engagement
   - Monitor course outcomes effectiveness

5. File Storage (AWS S3/GCS)
   - Store project submissions
   - Store certificates
```

---

## Summary

This schema supports:
✅ **Modularity:** Mix lessons into any course outcome  
✅ **FabInABox Model:** Individual + school bookings  
✅ **Inventory:** Track consumables & equipment  
✅ **Skill Tracking:** Prerequisites unlock based on mastery  
✅ **Multi-Location:** Support distributed labs  
✅ **Instructor Management:** Certifications, availability, billing  
✅ **Revenue Tracking:** Equipment + instructor + materials costs  
✅ **Scalability:** Designed for growth (1-1000+ learners)  

**Next Step:** Start building Phase 1 in Frappe.
