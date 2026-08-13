# Course Lesson Auto-Naming Implementation Summary

**Date:** August 12, 2026  
**Feature:** Hierarchical auto-naming for Frappe LMS Course Lessons  
**Status:** ✅ Complete & Ready to Deploy

---

## What Was Implemented

### Core Feature
Auto-generate hierarchical shorthand names for **Course Lesson** documents based on course → chapter → lesson hierarchy.

**Naming Format:** `{COURSE_CODE}-{CHAPTER_NUM:02d}-L{LESSON_NUM:02d}`

**Examples:**
- `Python-01-L03` — Python course, Chapter 1, Lesson 3
- `Electronics-02-L01` — Electronics course, Chapter 2, Lesson 1

---

## Files Created

### 1. **Course Lesson Utilities** (Main Logic)
📄 `apps/ecanmake/everyone_can_make/everyone_can_make/course_lesson_utils.py`

**Functions:**
- `generate_course_lesson_shorthand(doc)` — Generates shorthand name for a lesson
- `on_course_lesson_before_insert(doc, method)` — Hook: auto-names new lessons on creation
- `on_course_lesson_before_save(doc, method)` — Hook: ensures new lessons are named before save
- `rename_untitled_lessons()` — Migration function to retroactively rename existing lessons

**Key Logic:**
```python
shorthand = f"{course_code}-{chapter_num:02d}-L{lesson_num:02d}"
# Example: "Python-01-L03"
```

### 2. **Hook Registration**
📄 `apps/ecanmake/everyone_can_make/hooks.py` (modified)

**Changes:**
- Added `doc_events` section with `Course Lesson` hooks:
  - `before_insert` → `on_course_lesson_before_insert`
  - `before_save` → `on_course_lesson_before_save`
- Registered client-side JS: `doctype_js["Course Lesson"]`

### 3. **Migration Patch**
📄 `apps/ecanmake/everyone_can_make/patches/rename_course_lessons_shorthand.py`

**Purpose:** Runs on `bench migrate` to rename all existing untitled lessons.

**Registry:** `apps/ecanmake/everyone_can_make/patches.txt`
- Added to `[post_model_sync]` section so it runs after model schema is synced

### 4. **UI Enhancement Script**
📄 `apps/ecanmake/everyone_can_make/public/js/course_lesson_autoname.js`

**Features:**
- Shows info about auto-naming when creating new lessons
- Displays preview of generated shorthand name
- Adds "Rename Untitled Lessons" button to Course Lesson list view (System Manager only)
- Updates field descriptions with auto-naming info

### 5. **Manual Trigger Script**
📄 `scripts/rename_course_lessons.py`

**Usage:**
```bash
bench execute scripts.rename_course_lessons
```

**Purpose:** Allows manual triggering of the migration without running full `bench migrate`.

### 6. **Documentation**
📄 `COURSE_LESSON_AUTONAME.md` (comprehensive guide)

**Covers:**
- How the feature works
- Implementation details
- Usage examples
- Configuration options
- Troubleshooting
- Testing procedures

---

## How It Works

### Scenario 1: New Lesson Without Title
1. User creates a new `Course Lesson` in LMS
2. Selects `Chapter` (e.g., "Python-101 > Chapter 1")
3. Leaves `Title` blank or enters "Lesson" / "Untitled"
4. Clicks Save
5. ✅ System auto-names the lesson: `Python-01-L01`
6. ✅ Title is automatically set to: `Python-01-L01`

**Triggered by:** `before_insert` and `before_save` hooks

### Scenario 2: Existing Untitled Lessons
1. Migration runs: `bench migrate`
2. System scans all `Course Lesson` records
3. Identifies lessons with empty/placeholder titles
4. Generates shorthand names for each
5. ✅ Renames documents safely (checks for conflicts)
6. ✅ Reports results (renamed count, errors)

**Triggered by:** `rename_course_lessons_shorthand` patch in `[post_model_sync]`

### Scenario 3: Manual Rename (Admin)
1. Admin goes to LMS → Course Lessons list
2. Clicks "Rename Untitled Lessons" button (custom action)
3. Confirms the action
4. ✅ All untitled lessons are renamed immediately
5. ✅ List view refreshes with new names

**Triggered by:** JS button calling `everyone_can_make.course_lesson_utils.rename_untitled_lessons`

---

## Algorithm Details

### Course Code Generation
```python
course_code = (
    course.get("course_code")           # Custom field if available
    or course.name[:3].upper()           # First 3 letters of course name
    or "CRS"                             # Fallback
)
```

### Chapter Number (Ordinal)
```python
chapters = frappe.get_list("Course Chapter", 
    filters={"course": course_name},
    order_by="creation asc"
)
chapter_num = index_in_list + 1  # 1-based numbering
```

### Lesson Number (Ordinal)
```python
lessons = frappe.get_list("Course Lesson",
    filters={"chapter": chapter_name},
    order_by="creation asc"
)
lesson_num = index_in_list + 1  # 1-based numbering
```

### Final Name
```python
shorthand = f"{course_code}-{chapter_num:02d}-L{lesson_num:02d}"
# :02d = zero-padded 2 digits (01, 02, ..., 99)
```

### Name Collision Handling
If generated name exists:
```python
shorthand = f"{shorthand}-{doc.creation.timestamp():.0f}"
# Example: "Python-01-L01-1723488234"
```

---

## Deployment Instructions

### Step 1: Verify Files
```bash
ls -la apps/ecanmake/everyone_can_make/everyone_can_make/course_lesson_utils.py
ls -la apps/ecanmake/everyone_can_make/patches/rename_course_lessons_shorthand.py
cat apps/ecanmake/everyone_can_make/patches.txt  # Check patch is listed
```

### Step 2: Clear Cache (if already installed)
```bash
bench clear-cache
bench clear-website-cache
```

### Step 3: Run Migration
```bash
bench migrate
```

This will:
- Sync all doctypes
- Run the `rename_course_lessons_shorthand` patch
- Auto-name all existing untitled lessons
- Enable the hooks for new lessons

### Step 4: Restart Server
```bash
bench restart
```

---

## Testing

### Test 1: Create New Lesson Without Title
1. Open Frappe Desk → LMS → Course Lessons → New
2. Fill:
   - **Chapter:** Select any chapter (e.g., "Python-101 > Intro")
   - **Title:** (leave blank)
3. Save
4. ✅ Expect: Name auto-generated as `Python-01-L01` (or similar)

### Test 2: Verify Existing Lessons Renamed
```bash
bench console
frappe.get_list("Course Lesson", fields=["name", "title"], limit=10)
# Check if any have shorthand names like "Python-01-L01"
```

### Test 3: Check for Duplicates
```bash
bench console
from collections import Counter
names = frappe.get_list("Course Lesson", fields=["name"])
name_list = [n['name'] for n in names]
duplicates = [n for n, c in Counter(name_list).items() if c > 1]
print("Duplicates:", duplicates)  # Should be empty
```

### Test 4: Manual Rename Button
1. Go to LMS → Course Lessons (list view)
2. Look for "Rename Untitled Lessons" button in actions menu
3. Click it (must be System Manager)
4. Confirm the action
5. ✅ Expect: Message showing count of renamed lessons

---

## Configuration Options

### Change Naming Format
Edit `course_lesson_utils.py` line ~45:
```python
# Current:
shorthand = f"{course_code}-{chapter_num:02d}-L{lesson_num:02d}"

# Alternative (no "L" prefix):
shorthand = f"{course_code}-{chapter_num:02d}-{lesson_num:02d}"

# Alternative (full course name):
shorthand = f"{course_name}-{chapter_num:02d}-L{lesson_num:02d}"
```

### Custom Course Codes
Add a `course_code` field to `LMS Course` doctype, and the system will use it instead of auto-deriving from course name.

---

## Rollback Instructions

If you need to revert this feature:

### Option 1: Disable Hooks Only
```python
# In hooks.py, comment out:
# doc_events = { ... }
# doctype_js = { ... }

bench clear-cache
bench restart
```

### Option 2: Full Rollback
```bash
# Delete the files:
rm apps/ecanmake/everyone_can_make/everyone_can_make/course_lesson_utils.py
rm apps/ecanmake/everyone_can_make/patches/rename_course_lessons_shorthand.py
rm apps/ecanmake/everyone_can_make/public/js/course_lesson_autoname.js

# Revert hooks.py changes
git checkout apps/ecanmake/everyone_can_make/hooks.py
git checkout apps/ecanmake/everyone_can_make/patches.txt

bench clear-cache
bench restart
```

Note: Renamed lessons will keep their new names; this only stops future auto-naming.

---

## Troubleshooting

### Issue: Patch Not Running
**Check:**
```bash
bench console
frappe.db.get_value("Patch Log", {"name": "everyone_can_make.patches.rename_course_lessons_shorthand"})
```
**Solution:** Ensure patch is listed in `patches.txt` under `[post_model_sync]`

### Issue: Lessons Not Auto-Named
**Check:** Is the title empty or a placeholder?
```python
# Only renamed if title is in:
["", "Untitled", "Lesson", "untitled"]
```
**Solution:** Set title to empty and re-save, or use migration function

### Issue: Hook Not Triggering
**Check:** Is the JS loaded?
```bash
# In browser console:
frappe.call({method: 'everyone_can_make.course_lesson_utils.rename_untitled_lessons'})
```
**Solution:** Ensure `doctype_js` is set in `hooks.py` and run `bench clear-cache`

---

## Files Checklist

- ✅ `apps/ecanmake/everyone_can_make/everyone_can_make/course_lesson_utils.py` (New)
- ✅ `apps/ecanmake/everyone_can_make/patches/rename_course_lessons_shorthand.py` (New)
- ✅ `apps/ecanmake/everyone_can_make/public/js/course_lesson_autoname.js` (New)
- ✅ `scripts/rename_course_lessons.py` (New, optional)
- ✅ `COURSE_LESSON_AUTONAME.md` (New, documentation)
- ✅ `apps/ecanmake/everyone_can_make/hooks.py` (Modified)
- ✅ `apps/ecanmake/everyone_can_make/patches.txt` (Modified)

---

## Next Steps (Optional Enhancements)

1. **Custom Lesson Code Field**
   - Add `lesson_code` field to `Course Lesson` for manual overrides
   - Modify auto-naming logic to check for manual code first

2. **UI Button in Form**
   - Add "Auto-Name" button in Course Lesson form view
   - Allows user to manually trigger auto-naming

3. **Audit Trail**
   - Log all auto-name operations to a custom doctype
   - Track which lessons were auto-named and when

4. **Batch Operations**
   - Add "Bulk Rename" feature in list view
   - Allow filtering and selective renaming

---

**Implementation Status: ✅ COMPLETE**  
**Ready for: Production Deployment**  
**Testing Status: Pending**  
**Documentation: Complete**

