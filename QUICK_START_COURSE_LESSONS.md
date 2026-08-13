# Quick Start: Course Lesson Auto-Naming

**TL;DR** — Leave the Course Lesson title blank, and the system will auto-generate a shorthand name based on the course-chapter-lesson hierarchy.

---

## Quick Summary

| Aspect | Details |
|--------|---------|
| **Feature** | Auto-name Course Lessons with hierarchical shorthand |
| **Format** | `COURSE-CHAPTER-LESSON` (e.g., `Python-01-L03`) |
| **When it works** | When creating new lessons OR running migration |
| **Title field** | Can be left blank, "Untitled", or "Lesson" |
| **What changes** | Lesson name + title both set to shorthand |

---

## For End Users

### Creating a Lesson
1. Go to **LMS → Courses → Course Lessons → New**
2. Select the **Chapter**
3. **Leave Title blank** (or type "Lesson")
4. Add lesson content as usual
5. Click **Save**
6. ✅ System generates name: `Python-01-L03` (etc.)

### Example Names
- `Python-01-L01` = Python course, Chapter 1, Lesson 1
- `Electronics-02-L03` = Electronics course, Chapter 2, Lesson 3
- `Design-03-L05` = Design course, Chapter 3, Lesson 5

---

## For Administrators

### First Time Setup (after code update)
```bash
cd /path/to/bench
bench migrate
```
This renames all existing untitled lessons.

### Manual Rename (if needed)
**Option A: From UI**
1. Go to **LMS → Course Lessons** (list view)
2. Click **Rename Untitled Lessons** button
3. Confirm

**Option B: From Terminal**
```bash
bench execute everyone_can_make.course_lesson_utils.rename_untitled_lessons
```

**Option C: From Console**
```python
frappe.call({
    'method': 'everyone_can_make.course_lesson_utils.rename_untitled_lessons'
})
```

---

## How Names Are Generated

```
Format: {COURSE_CODE}-{CHAPTER_NUM}-L{LESSON_NUM}

Example for "Python 101" course:
├─ Chapter 1: Intro to Python
│  ├─ Lesson 1: Welcome             → Python-01-L01
│  ├─ Lesson 2: Variables           → Python-01-L02
│  └─ Lesson 3: Data Types          → Python-01-L03
└─ Chapter 2: Functions
   ├─ Lesson 1: Function Basics     → Python-02-L01
   └─ Lesson 2: Parameters & Args   → Python-02-L02
```

### Details
- **Course Code**: First 3 letters of course name (or custom if set)
- **Chapter Number**: 01, 02, 03... (ordinal in course)
- **Lesson Number**: 01, 02, 03... (ordinal in chapter)

---

## Customization

### Use Custom Course Codes
Add a `course_code` field to the LMS Course doctype:
- Value: `PYTHON`, `ELEC`, `FABLAB`, etc.
- The system will use this instead of auto-deriving from course name

### Change Naming Format
Edit `apps/ecanmake/everyone_can_make/everyone_can_make/course_lesson_utils.py`:
```python
# Current (line ~45):
shorthand = f"{course_code}-{chapter_num:02d}-L{lesson_num:02d}"

# Remove "L" prefix if desired:
shorthand = f"{course_code}-{chapter_num:02d}-{lesson_num:02d}"

# Use full course name:
shorthand = f"{course_name}-{chapter_num:02d}-L{lesson_num:02d}"
```

---

## FAQ

**Q: What if I want a custom title?**  
A: Enter any title other than "Lesson", "Untitled", or blank. The auto-naming only triggers for empty/placeholder titles.

**Q: Can I rename a lesson after it's created?**  
A: Yes, use the **Rename** button in the Course Lesson form (top-right menu).

**Q: What if two lessons end up with the same generated name?**  
A: The system appends a timestamp: `Python-01-L01-1723488234`

**Q: Do I need to run migrate to enable this?**  
A: Yes, run `bench migrate` once to activate hooks and rename existing lessons.

**Q: Can I disable this feature?**  
A: Yes, comment out the `doc_events` section in `hooks.py` and run `bench clear-cache`.

**Q: Will this affect existing lessons?**  
A: Only if they have empty/placeholder titles. Lessons with real titles are unchanged.

**Q: Can I batch rename multiple lessons?**  
A: Yes, use the "Rename Untitled Lessons" button in the Course Lessons list view.

---

## Files & Documentation

| File | Purpose |
|------|---------|
| `COURSE_LESSON_AUTONAME.md` | Complete technical documentation |
| `IMPLEMENTATION_SUMMARY.md` | Deployment & testing guide |
| `course_lesson_utils.py` | Core auto-naming logic |
| `hooks.py` | Hook registration |
| `rename_course_lessons_shorthand.py` | Migration patch |
| `course_lesson_autoname.js` | UI enhancements |

---

## Support

For issues or questions:
1. Check `COURSE_LESSON_AUTONAME.md` → **Troubleshooting** section
2. Review `IMPLEMENTATION_SUMMARY.md` → **Troubleshooting** section
3. Run tests from `IMPLEMENTATION_SUMMARY.md` → **Testing** section

---

**Last Updated:** August 12, 2026  
**Status:** ✅ Ready to Deploy
