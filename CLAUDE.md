# FabInABox: Everyone Can Make

**A Frappe-based learning & resource-booking system supporting individual learners and school bookings.**

---

## Project Overview

**FabInABox** (branded as "Everyone Can Make") is a modular learning platform built on Frappe that combines:
- **Learning design:** Lessons → Skill Domains → Course Outcomes (hierarchical skill development)
- **Resource management:** Lab equipment, instructor availability, locations, booking/reservations
- **School support:** Multi-tenant bookings for schools + individual learners
- **Asset tracking:** Equipment maintenance logs, depreciation, lifecycle management
- **Skill progression:** Tracking learner mastery, prerequisites, unlocking based on achievement

The system is built **on top of the native Frappe LMS**, not as a replacement. It extends LMS's lesson/course/batch/quiz infrastructure with FabInABox-specific resource booking, instructor certification, equipment management, and skill-tracking workflows.

---

## App & Site Topology

### Bench-Managed Apps (not tracked by fabinabox repo)
The main `fabinabox` repo tracks only **sites/** configuration and documentation, following standard Frappe Bench convention. All app source code is cloned/managed independently:

- **Frappe framework** (`apps/frappe/`): Upstream app, installed/updated via bench CLI; remote: `frappe/frappe`
- **Frappe LMS** (`apps/lms/`): Upstream app, installed/updated via bench; includes `Course`, `Course Lesson`, `Chapter`, `Batch`, `Quiz`, `Assignment`, `Certificate`, `skills`, `user_skill` doctypes
- **Custom app** (`apps/ecanmake/`): Your FabInABox custom app, managed as a **separate git repository**
  - GitHub: `smartclass-gedu/ecanmake`
  - Frappe module name: "Everyone Can Make"
  - Path to doctypes: `apps/ecanmake/ecanmake/ecanmake/doctype/`
  - 23 doctypes implemented (9 standalone + 14 child tables)
  - Cloned into `apps/` via bench; edits are committed/pushed independently to `smartclass-gedu/ecanmake`

---

## Design Documentation

**Source of truth:** `frappe_schema_design.md` (root directory)
- **Title:** "Frappe LMS Schema Design: Everyone Can Make + FabInABox"
- **Structure:** 11 Parts (not literally "phases")
- **Implementation roadmap:** Part 9, with phases:
  - **Phase 1** (Weeks 1–2): Skill Domain, Lesson/Atomic Learning, Course Outcome
  - **Phase 2** (Week 3): Lab Equipment, Lab Location, Instructor
  - **Phase 3** (Weeks 4–5): Resource Booking, Instructor Session, School
  - **Phase 4+:** Learner tracking, Reports, Security, API integrations (not yet implemented)

---

## Critical: LMS vs. Custom-App Doctype Boundary

### Frappe LMS owns these doctypes — DO NOT redefine them in `everyone_can_make`:
- **`Course Lesson`** — Frappe LMS's atomic lesson unit (lives inside Course → Chapter → Lesson hierarchy)
  - Fields: `title`, `chapter` (Link → Course Chapter), `body`/`content` (Markdown), `youtube`, `quiz_id`, etc.
- **`LMS Course`** — a course in Frappe LMS (different from `Course Outcome` in FabInABox)
- **`Course Chapter`**, **`LMS Batch`**, **`LMS Quiz`**, **`LMS Assignment`**, **`LMS Certificate`**
- **`skills`**, **`user_skill`** — LMS's native skill tracking (note: conceptually adjacent to `Skill Domain` in FabInABox but not unified yet)

### FabInABox custom app (ecanmake) owns these doctypes:
- **`Atomic Learning`** (design doc calls this "Lesson") — wraps LMS's `Course Lesson` with FabInABox metadata
  - Links to LMS via: `lesson_title` (Link → "Course Lesson", required)
  - Adds FabInABox fields: `lesson_code`, `duration_hours`, `duration_minutes`, `difficulty_level`, `learning_objectives` (child table), `materials_list`, `equipment_required`, `prerequisites`, etc.
  - **Purpose:** Extend LMS's lessons with FabInABox-specific attributes (equipment, prerequisites, material tracking) without duplicating the core lesson entity
  - **Why not called "Course Lesson"?** To avoid name collision with LMS's existing doctype and make the wrapping relationship explicit

**Key principle:** If you're tempted to create a doctype in the `ecanmake` app with a name LMS already uses (like `Course Lesson`, `Course Chapter`, `Quiz`), stop — instead, create a wrapper/linker doctype (like `Atomic Learning`) that references the LMS doctype and layers on FabInABox-specific fields.

---

## Doctype Inventory (Phase 1–3 Implementation)

### Part 1: Learning Architecture
| DocType | Module | Key Fields | Child Tables | Purpose |
|---------|--------|------------|--------------|---------|
| **Skill Domain** | everyone_can_make | domain_name (unique), description, icon | — | Master data for skill domains (Electronics, Digital Fabrication, Design, Software, etc.) |
| **Atomic Learning** | everyone_can_make | lesson_title (Link → LMS Course Lesson, reqd), lesson_code (unique), duration_hours, duration_minutes, difficulty_level, status | Lesson Objective, Lesson Prerequisite, Lesson Equipment, Lesson Material | FabInABox wrapper extending LMS Course Lesson with resource/prerequisite metadata |
| **Course Outcome** | everyone_can_make | outcome_title, outcome_code (unique), description, learning_outcomes_text, difficulty_level, target_audience, status, created_by (Link → User) | Outcome Lesson (→ Atomic Learning), Outcome Skill (→ Skill Domain), Outcome Equipment | Aggregates lessons into a learnable course outcome with skill domains and equipment requirements |

### Part 2: Resource Management
| DocType | Module | Key Fields | Child Tables | Purpose |
|---------|--------|------------|--------------|---------|
| **Lab Equipment** | everyone_can_make | equipment_name, equipment_code (unique), category, location (Link → Lab Location, reqd), manufacturer, model, serial_number, acquisition_date, warranty_expiry, is_bookable, requires_operator_supervision, max_concurrent_users, hourly_rate, requires_training, training_lesson (Link → Atomic Learning), status, last_maintenance_date | — | Inventory of lab/makerspace equipment; tracks maintenance, usage rules, booking rates |
| **Lab Location** | everyone_can_make | location_name (unique, reqd), address, contact_phone, contact_email, max_simultaneous_bookings, lat_long | Location Hours (day_of_week, open_time, close_time, is_closed) | Physical lab/makerspace location with operating hours and booking limits |
| **Instructor** | everyone_can_make | user_link (Link → User, unique, reqd), full_name (fetched), email (fetched), primary_location (Link → Lab Location), hourly_rate, flat_rate_per_session, status | Instructor Skill (→ Skill Domain, experience_level, years_experience), Instructor Certification (→ Lab Equipment, certified_date, expiry_date, can_certify_others), Instructor Hours (day_of_week, start_time, end_time, location) | Master record for instructors; tracks skills, equipment certifications, availability |

### Part 3: Inventory, Assets & Booking (Phase 3)
| DocType | Module | Key Fields | Child Tables | Purpose |
|---------|--------|------------|--------------|---------|
| **Asset Lifecycle** | everyone_can_make | equipment (Link → Lab Equipment, unique, reqd), total_hours_used (read-only), total_sessions (read-only), original_cost, depreciation_rate, current_book_value (read-only, computed) | Maintenance Log (maintenance_date, type Select, performed_by → Instructor, cost, notes, next_maintenance_due) | Tracks equipment maintenance history, depreciation, and cost basis |
| **Resource Booking** | everyone_can_make | course_outcome (Link → Course Outcome), booked_for_school (Link → School) or individual_learner (Link → User), booked_by (Link → User), instructor_assigned (Link → Instructor), lab_location (Link → Lab Location), booking_date, start_date, end_date, start_time, end_time, status, total_cost, instructor_cost, created_date | Booking Resource (equipment_item → Lab Equipment, qty_required) | Central booking doctype; handles individual & school reservations; enforces availability checks (no equipment overbooking, no instructor conflicts); calculates costs |
| **Instructor Session** | everyone_can_make | resource_booking (Link → Resource Booking, reqd), instructor (Link → Instructor, reqd), lesson_covered (Link → Atomic Learning), duration_hours (computed), cost (computed), status | — | Records a teaching session tied to a booking; auto-computes cost based on instructor rate |
| **School** | everyone_can_make | school_name (unique, reqd), school_code, address, contact_email, contact_phone, status, total_bookings (read-only), total_students_served (read-only), total_spent (read-only) | School Coordinator (coordinator_user → User, role Select) | Represents a school entity for multi-school bookings; rollup fields auto-updated from Resource Booking records |

### Note: Native Frappe Reuse
**Consumables inventory:** The design reuses Frappe's native **`Item`** (for consumable materials) and **`Warehouse`** (for inventory locations) + **`Stock Entry`** / **`Bin`** (for stock transactions) — no custom doctype created for this.

---

## Relationship Graph (Data Model)

```
LMS (Native Frappe LMS)
  └─ Course → Chapter → Course Lesson (LMS's own entity)
  └─ LMS Batch, LMS Quiz, LMS Certificate, skills, user_skill

FabInABox Custom App
  ├─ Skill Domain (master)
  │
  ├─ Atomic Learning ──links──> Course Lesson (LMS)
  │   ├─ Lesson Objective (child)
  │   ├─ Lesson Prerequisite ──> Atomic Learning (self-link: prerequisites)
  │   ├─ Lesson Equipment ──> Lab Equipment
  │   └─ Lesson Material ──> Item (Frappe native)
  │
  ├─ Course Outcome (aggregates lessons & skills)
  │   ├─ Outcome Lesson ──> Atomic Learning
  │   ├─ Outcome Skill ──> Skill Domain
  │   └─ Outcome Equipment ──> Lab Equipment
  │
  ├─ Lab Equipment (inventory, bookable)
  │   └─ location ──> Lab Location
  │
  ├─ Lab Location (physical lab/makerspace)
  │   └─ Location Hours (child table)
  │
  ├─ Instructor (master)
  │   ├─ Instructor Skill ──> Skill Domain
  │   ├─ Instructor Certification ──> Lab Equipment
  │   ├─ Instructor Hours (child table)
  │   └─ primary_location ──> Lab Location
  │
  ├─ Asset Lifecycle (equipment maintenance & depreciation)
  │   ├─ equipment ──> Lab Equipment (1:1)
  │   └─ Maintenance Log (child table, performed_by ──> Instructor)
  │
  ├─ Resource Booking (core reservation engine; Phase 3 business logic)
  │   ├─ course_outcome ──> Course Outcome
  │   ├─ booked_for_school ──> School
  │   ├─ instructor_assigned ──> Instructor
  │   ├─ lab_location ──> Lab Location
  │   ├─ Booking Resource (child table, equipment_item ──> Lab Equipment)
  │   └─ created_date, calculated costs
  │
  ├─ Instructor Session (ties teaching to a booking)
  │   ├─ resource_booking ──> Resource Booking
  │   ├─ instructor ──> Instructor
  │   └─ lesson_covered ──> Atomic Learning
  │
  └─ School (multi-tenant booking entity)
      ├─ School Coordinator (child table, coordinator_user ──> User)
      └─ (rollup fields: total_bookings, total_students_served, total_spent — updated by Resource Booking)
```

---

## Known Discrepancies: Design Doc vs. Implementation

### 1. `Lesson` → `Atomic Learning`
- **Design spec** (Part 1, line ~28–52): Generic `Lesson` doctype with fields:
  - `skill_domain` (Link → Skill Domain) — **NOT implemented**
  - `content_url` — **NOT implemented**
  - `has_quiz`, `quiz_link` (Link → Quiz) — **NOT implemented** (LMS's Course Lesson handles this)
  - `mastery_threshold` (Percent, default 80) — **NOT implemented**
  - `instructor_time_hours` — **NOT implemented**
  - `created_by_instructor`, `last_updated` — **NOT implemented** (meta fields only)
  - `estimated_duration_minutes` — **CHANGED** to split: `duration_hours` + `duration_minutes`
- **Implementation:** `Atomic Learning` doctype links to LMS's `Course Lesson` and adds only: `lesson_code`, `duration_hours`, `duration_minutes`, `difficulty_level`, objectives/materials/equipment/prerequisites child tables
- **Gap:** No direct `skill_domain` link on a lesson, so "which skill does this lesson teach?" is not queryable at the lesson level — it's only visible through parent Course Outcome's `Outcome Skill` table
- **Implication:** Phase 4+ features (Learner Progress, skill unlocking logic) will need to traverse Course Outcome ← Outcome Lesson ← Atomic Learning to determine prerequisites & skill requirements; direct lesson→skill link would simplify queries

### 2. `lab_equipment.py` Stale Code Comment
- **File:** `apps/ecanmake/ecanmake/ecanmake/doctype/lab_equipment/lab_equipment.py`
- **Current state:** Just `pass` with a docstring: "Stub for Phase 1... Phase 2 will add full fields"
- **Reality:** `lab_equipment.json` already has all Phase 2 fields (category, location, is_bookable, hourly_rate, requires_training, etc.)
- **Action:** Update the `.py` controller docstring or add validation/computed fields if needed (currently no business logic)

### 3. Minor Field Label Discrepancies
- **Outcome Skill `mastery_level`:** Design doc typo (`"Foundational\nIntermediatePractitioner\nAdvanced"` — run-on word) vs. Implementation (`"Foundational\nIntermediate\nAdvanced"` — fixed)
- **Lesson Objective Bloom's taxonomy:** Design stops at "Evaluate" (`Recall\nUnderstand\nApply\nAnalyze\nEvaluate`); Implementation adds "Create" (full 6-level taxonomy)
  - This is actually an improvement, not a bug

### 4. LMS `skills`/`user_skill` vs. FabInABox `Skill Domain`
- **LMS's `skills` doctype:** A skill entity with mastery levels
- **FabInABox's `Skill Domain` doctype:** A domain/category of skills (e.g., "Electronics", "Digital Fabrication")
- **Status:** Conceptually adjacent but not yet unified. If Phase 4+ needs to link user mastery (Learner Skill) to LMS's `user_skill`, or if `Skill Domain` should map to LMS's `skills`, that work is not yet planned.

### 5. Phase 4 Not Yet Implemented
Per the design doc (Part 9, Phase 4 weeks):
- **`Learner Progress`** doctype — **not yet implemented**
- **`Lesson Completion`** doctype — **not yet implemented**
- **`Learner Skill`** doctype — **not yet implemented**
These are needed for tracking user mastery, prerequisite unlocking, and learner journeys. Expected in Phase 4 work.

---

## Guidance for Future Changes

### DO
✅ Extend LMS doctypes via wrapper/linker doctypes (like `Atomic Learning` wraps `Course Lesson`)  
✅ Reuse existing child tables (e.g., add more Lesson fields via Lesson Material child table rather than creating a new "Lesson Resource" table)  
✅ Keep `frappe_schema_design.md` and code in sync — update docstring comments when schema changes  
✅ Follow Part 9's implementation roadmap phases when planning Phase 4+ features (Learner Tracking, Reports, Security, APIs)  
✅ When adding a field, check first if it belongs in LMS (Course Lesson) or in FabInABox's wrapper (Atomic Learning)  

### DON'T
❌ **Never** create a new doctype named `Course Lesson`, `Course Chapter`, `LMS Course`, `Quiz`, `Batch`, `Certificate`, `skills`, or other LMS-reserved names in the `ecanmake` app  
❌ Don't duplicate LMS entity definitions — extend via Links and child tables instead  
❌ Don't leave stale "Phase X stub" comments in controller code — either implement the phase or remove the comment  
❌ Don't commit app source changes to the **fabinabox** repo; all `ecanmake` edits belong in the **smartclass-gedu/ecanmake** repo  

---

## Quick Links

- **Design doc:** `frappe_schema_design.md` (root)
- **Custom app home (ecanmake):** `apps/ecanmake/`
- **Doctype folder:** `apps/ecanmake/ecanmake/ecanmake/doctype/`
- **Custom app repo:** `https://github.com/smartclass-gedu/ecanmake`
- **Frappe LMS (native):** `apps/lms/` (do not modify for FabInABox features — extend instead)

---

*Last updated: August 2026*  
*FabInABox is built on Frappe & extends Frappe LMS*
