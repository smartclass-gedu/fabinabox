# Course Lesson Auto-Naming

**Status:** ✅ Implemented  
**Feature:** Automatically generate hierarchical shorthand names for Course Lessons based on course → chapter → lesson hierarchy.

## Overview

The Frappe LMS `Course Lesson` doctype uses a naming format `{####}-{title}`, which requires a title. If lessons are created without titles (or with placeholder titles like "Untitled", "Lesson", etc.), they either fail to create or end up with unhelpful names.

This feature implements **automatic shorthand naming** that:
- Generates names based on the course-chapter-lesson hierarchy
- Applies retroactively to existing untitled/placeholder lessons
- Auto-names all future new lessons with empty titles
- Uses format: `{COURSE_CODE}-{CHAPTER_NUM:02d}-L{LESSON_NUM:02d}`

### Example Names
- `Python-01-L01` — Python course, Chapter 1, Lesson 1
- `Electronics-02-L03` — Electronics course, Chapter 2, Lesson 3
- `Design-03-L05` — Design course, Chapter 3, Lesson 5

## How It Works

### 1. Auto-Naming on New Lesson Creation
When a new `Course Lesson` is created:
- If the `title` field is empty or contains placeholder text ("Untitled", "Lesson", etc.)
- The system generates a shorthand name: `{COURSE_CODE}-{CHAPTER_NUM}-L{LESSON_NUM}`
- The title is also set to this shorthand

**Triggers:** `before_insert`, `before_save` hooks on `Course Lesson` doctype

### 2. Retroactive Renaming (Migration)
A migration patch (`rename_course_lessons_shorthand.py`) runs on `bench migrate`:
- Identifies all existing `Course Lesson` records with untitled/placeholder titles
- Generates shorthand names for each
- Safely renames documents (checks for conflicts)
- Updates titles to match names

**Migration Path:** `everyone_can_make/patches/rename_course_lessons_shorthand.py`

### 3. Manual Renaming
You can also manually rename all untitled lessons by running:

**From Frappe Console:**
```python
frappe.call({
    'method': 'everyone_can_make.course_lesson_utils.rename_untitled_lessons'
})
```

**From CLI:**
```bash
bench execute everyone_can_make.course_lesson_utils.rename_untitled_lessons
```

## Implementation Details

### Shorthand Algorithm
```
Course Code:    {COURSE_CODE}          # First 3 chars of course name, or custom code
Chapter Number: {CHAPTER_NUM:02d}      # 01, 02, 03... (ordinal in course)
Lesson Number:  {LESSON_NUM:02d}       # 01, 02, 03... (ordinal in chapter)

Result:         {COURSE_CODE}-{CHAPTER_NUM}-L{LESSON_NUM}
```

### Name Collision Handling
If the generated shorthand conflicts with an existing document name:
- Appends a timestamp-based suffix: `{SHORTHAND}-{TIMESTAMP}`
- Ensures unique names without data loss

### Code Location
- **Main logic:** `apps/ecanmake/everyone_can_make/course_lesson_utils.py`
  - `generate_course_lesson_shorthand()` — Generates shorthand for a lesson
  - `on_course_lesson_before_insert()` — Hooks new lessons
  - `on_course_lesson_before_save()` — Ensures new lessons are named
  - `rename_untitled_lessons()` — Migrates existing lessons

- **Hook registration:** `apps/ecanmake/everyone_can_make/hooks.py`
  - `doc_events["Course Lesson"]` — Registers before_insert/before_save hooks

- **Migration patch:** `apps/ecanmake/everyone_can_make/patches/rename_course_lessons_shorthand.py`
  - Calls `rename_untitled_lessons()` during `bench migrate`

- **Patch registry:** `apps/ecanmake/everyone_can_make/patches.txt`
  - Registers patch in `[post_model_sync]` section

## Usage

### Creating a New Lesson
1. Go to **LMS > Courses > Course Lessons > New**
2. Select the **Course Chapter**
3. Leave **Title** blank (or enter placeholder text)
4. Click **Save**
5. ✅ The system auto-generates a shorthand name and title

### Applying to Existing Lessons
Run the migration:
```bash
cd /path/to/frappe-bench
bench migrate
```

Or manually trigger:
```bash
bench execute everyone_can_make.course_lesson_utils.rename_untitled_lessons
```

## Configuration

### Customize Course Code
By default, the system uses the first 3 characters of the course name as the course code.  
To use a custom course code, add a **`course_code`** field to the `LMS Course` doctype:
- The system will use this field if available
- Falls back to first 3 characters if empty

### Adjust Naming Format
To change the naming format (e.g., remove "L" prefix for lessons), edit:
```python
# In course_lesson_utils.py, line ~45
shorthand = f"{course_code}-{chapter_num:02d}-L{lesson_num:02d}"
# Change to: shorthand = f"{course_code}-{chapter_num:02d}-{lesson_num:02d}"
```

## Testing

### Check Auto-Naming
1. Create a new `Course Lesson` without a title
2. Save it
3. Verify the name changed to shorthand format

### Check Migration
```bash
bench console
frappe.db.get_list("Course Lesson", filters={"title": ""}, fields=["name", "title"])
# Should show all renamed lessons
```

### Verify No Duplicates
```bash
bench console
from collections import Counter
names = frappe.get_list("Course Lesson", fields=["name"])
name_list = [n['name'] for n in names]
duplicates = [name for name, count in Counter(name_list).items() if count > 1]
print(f"Duplicates: {duplicates}")  # Should be empty
```

## Troubleshooting

### Migration Not Running
- Ensure `patches.txt` has the patch listed in `[post_model_sync]`
- Check Frappe console for errors: `bench console` → `frappe.db.get_value("Patch Log", {"name": "rename_course_lessons_shorthand"})`

### Name Conflicts
- The system auto-appends a timestamp if the shorthand already exists
- Manual renames can be done via the Frappe UI: **Rename** button on the document

### Lessons Not Auto-Named
- The hook only triggers if the title is empty or is one of: `["", "Untitled", "Lesson", "untitled"]`
- If a lesson has a non-empty title, it won't be auto-renamed
- Manually set title to empty and re-save, or use the migration function

## Future Enhancements

- Add a custom **Lesson Code** field for manual overrides (e.g., `Python-Intro`, `CNC-Basics`)
- Create a UI button to "Auto-name all lessons" in the Course Lesson list view
- Add support for custom formatting via a DocType setting

---

**Last Updated:** August 12, 2026  
**Related:** Frappe LMS integration, Course structure, Lesson hierarchy
