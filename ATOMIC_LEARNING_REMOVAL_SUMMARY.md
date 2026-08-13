# Atomic Learning Doctype Removal — August 13, 2026

## Summary
Removed the `Atomic Learning` wrapper doctype for simplification. Lessons now link directly to native Frappe LMS `Course Lesson`.

## Files Deleted
1. `/apps/ecanmake/everyone_can_make/everyone_can_make/doctype/atomic_learning/` (entire folder)
2. `/apps/ecanmake/everyone_can_make/everyone_can_make/course_lesson_utils.py`
3. `/apps/ecanmake/everyone_can_make/patches/rename_course_lessons_shorthand.py` (orphaned patch file)
4. `/apps/ecanmake/everyone_can_make/patches/v1_0/rename_lesson_title_to_course.py` (already applied on server)
5. `/apps/ecanmake/everyone_can_make/public/js/course_lesson_autoname.js`
6. `/scripts/rename_course_lessons.py`

## Files Modified

### Doctype JSON Files
- **`instructor_session.json`**: Changed `lesson_covered` field options from `"Atomic Learning"` → `"Course Lesson"`
- **`lab_equipment.json`**: Changed `training_lesson` field options from `"Atomic Learning"` → `"Course Lesson"`

### Configuration Files
- **`patches.txt`**:
  - Removed `everyone_can_make.patches.v1_0.rename_lesson_title_to_course` (already applied on server)
  - Removed `everyone_can_make.patches.remove_course_outcome_doctypes` (was never implemented)
  - Added notes documenting why these were removed

### Documentation Files
- **`CLAUDE.md`**:
  - Removed `Atomic Learning` from "Key Custom Doctypes"
  - Updated "Critical Principle" section to emphasize direct use of LMS entities
  - Updated "Quick Start" workflow
  
- **`frappe_schema_design.md`**:
  - Replaced Part 1.1 (Atomic Learning doctype definition) with "Core Learning: Frappe LMS Course Lesson" section
  - Updated Executive Summary
  - Updated all Lab Equipment and Instructor Session field references to link to `Course Lesson`
  - Updated Phase 1 implementation checklist to mark Atomic Learning as removed
  - Updated diagram to remove Atomic Learning reference
  - Updated final status checklist (✅ Modular Lessons line)

- **`Content/Lesson_3D_Printing_Bambulab_PS1.md`**:
  - Changed all "Atomic Learning Record" headers to "Course Lesson Record"
  - Updated integration notes section to explain direct Course Lesson usage
  - Removed references to Lesson Prerequisite, Lesson Equipment, Lesson Material child tables
  - Updated to indicate prerequisites are handled via Course structure, not separate child tables

## Design Changes

### Before (Atomic Learning Wrapper Model)
```
Course Lesson (LMS)
     ↑
     └── Atomic Learning (FabInABox wrapper)
          ├── lesson_code
          ├── duration
          ├── difficulty_level
          └── description
```

### After (Direct Course Lesson Model)
```
Course Lesson (LMS)
  ├── title
  ├── body (markdown)
  ├── youtube
  ├── quiz_id
  └── [duration/difficulty/etc stored here]

FabInABox links directly:
  ├── Lab Equipment.training_lesson → Course Lesson
  └── Instructor Session.lesson_covered → Course Lesson
```

## Implications
- ✅ Simplified data model: No wrapper layer needed
- ✅ Reduced redundancy: All lesson metadata in one place (LMS)
- ✅ Easier queries: Direct links to LMS Course Lesson
- ⚠️ Loss of `lesson_code` as unique identifier (use LMS lesson title/ID instead)
- ⏳ Phase 4+ will need to handle skill tracking differently (no longer via Course Outcome → Atomic Learning)

## Migration Fix Applied
- ✅ Removed orphaned patch references from `patches.txt`
- ✅ Resolved "ModuleNotFoundError: No module named 'everyone_can_make.patches.remove_course_outcome_doctypes'"
- Migration should now complete successfully

## Next Steps on Frappe Instance
1. Re-run migration: `bench migrate` (should complete without patch errors now)
2. Delete Atomic Learning doctype from Frappe instance (via Web UI: Customization → Doctype)
3. Verify instructor session and lab equipment booking workflows work with Course Lesson links
4. (Optional) Delete any existing Atomic Learning records from database if they exist
5. Update Phase 4 implementation plan for learner progress/skill tracking (TBD)

---
**Date:** August 13, 2026  
**Performed by:** Claude Code  
**Reason:** Simplification — eliminate unnecessary wrapper layer
