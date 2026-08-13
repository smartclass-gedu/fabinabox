# FabInABox: Everyone Can Make
Building a Frappe-based learning platform with resource booking, skill tracking, and school support.

## Overview
- **Frappe LMS integration**: Build courses natively in LMS (Course → Chapter → Lesson)
- **FabInABox layer**: Extends LMS with equipment booking, instructors, skill domains, and resource management
- **Two repos**: `fabinabox` (config/docs), `smartclass-gedu/ecanmake` (custom app code)

## Building Courses in Frappe LMS

### Core LMS Doctypes (DO NOT modify)
- **Course**: Main course container
- **Course Chapter**: Groups lessons within a course
- **Course Lesson**: Atomic lesson unit with `title`, `body` (markdown), `youtube`, `quiz_id`
- **LMS Quiz**: Assessments
- **LMS Batch**: Cohorts for enrollment
- **LMS Certificate**: Completion certificates

### Build Course Flow
1. Create **Course** in LMS (set title, description, image)
2. Add **Chapters** to organize lessons
3. Create **Course Lessons** with content (markdown, video, text)
4. Attach **Quizzes** to lessons for assessment
5. Create **Batch** and enroll learners
6. Learners progress through chapters → lessons → quizzes → certificate

## FabInABox Extension Layer
Wrap LMS courses with FabInABox features via `ecanmake` custom app:

### Key Custom Doctypes
- **Skill Domain**: Master categories (Electronics, Fabrication, Design, Software)
- **Lab Equipment**: Bookable equipment with location, hourly rates, training requirements
- **Lab Location**: Physical labs/makerspaces with hours
- **Instructor**: Staff with skills, certifications, availability
- **Resource Booking**: Central reservation engine for equipment + instructor + location
- **School**: Multi-tenant booking support

## Critical Principle
**Use LMS entities directly.** Never create FabInABox versions of LMS doctypes:
- ✅ Link directly to LMS `Course Lesson` (e.g., `training_lesson`, `lesson_covered` fields)
- ❌ Don't create your own `Course Lesson`, `Course Chapter`, `Quiz`, `Batch`, `Certificate`, `skills` doctypes
- ❌ Don't create wrapper/extension doctypes for LMS entities

## Repo Boundaries
- **fabinabox repo**: Bench config, documentation, site settings
- **ecanmake repo**: All custom app code (`apps/ecanmake/`) — commit/push there, not here

## Quick Start
1. Create a **Course** in Frappe LMS web interface
2. Add **Chapters** and **Lessons** with content
3. Set up `Lab Equipment`, `Lab Location`, `Instructor` for booking
4. Link equipment training requirements and instructor sessions directly to LMS `Course Lesson` records
5. Create `Resource Booking` to handle student/school reservations

## Reference
- **Full schema**: `frappe_schema_design.md`
- **Custom app**: `apps/ecanmake/ecanmake/ecanmake/doctype/`
- **GitHub**: `https://github.com/smartclass-gedu/ecanmake`

---
*FabInABox: Frappe + LMS + Resource Management + Skills*
