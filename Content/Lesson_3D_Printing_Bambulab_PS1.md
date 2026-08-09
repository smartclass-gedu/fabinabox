# 3D Printing Fundamentals with Bambulab P1S + AMS

**Course Outcome:** Learn to design, prepare, and execute 3D prints using the Bambulab P1S with Automatic Material System (AMS)  
**Target Audience:** Individual makers / Students  
**Difficulty Level:** Beginner  
**Total Duration:** 3 sessions (1.5–2 hours each, ~5 hours total)  
**Location:** BitandBrick HQ  
**Skill Domain:** Digital Fabrication

---

## Overview

This three-session course teaches the fundamentals of 3D printing on the Bambulab P1S 3D printer with AMS (Automatic Material System). Students will learn to prepare 3D models, configure slice settings, load multiple filaments using the AMS, troubleshoot common issues, and safely operate the printer. By the end, learners will be able to confidently print multi-material parts from concept to finished object.

---

## Prerequisites

- **General BitandBrick HQ Maker Safety Orientation** (must complete before Session 1)
- No prior 3D printing or CAD experience required; beginner-friendly
- Basic computer skills (file management, web browsing)

---

## Instructor Requirements

- **Skill Level:** Beginner-friendly facilitator
- **Experience Needed:** Hands-on familiarity with Bambulab P1S and AMS system (6+ months preferred)
- **Must be able to:**
  - Demonstrate printer operation, nozzle heating, filament loading into AMS
  - Identify and resolve common AMS jams and sensor issues
  - Explain slicer settings (layer height, nozzle temperature, bed temperature, print speed)
  - Troubleshoot print failures (bed adhesion, warping, stringing)

---

## Session 1: Introduction to 3D Printing & Printer Setup

**Lesson Code:** `LAB-3DPRINT-01`  
**Lesson Title (for LMS Course Lesson link):** "3D Printing Session 1 — Intro & Printer Setup"  
**Duration:** 1.5 hours (90 minutes)  
**Lesson Type:** Hands-On (theory + demo + supervised practice)

### Learning Objectives

By the end of this session, students will be able to:

1. **Recall** the key components of the Bambulab P1S printer and AMS system
2. **Understand** the basics of FDM (Fused Deposition Modeling) technology
3. **Apply** correct startup and safety procedures
4. **Understand** filament loading procedures (single and AMS multi-material)
5. **Recall** common printer parameters (nozzle temp, bed temp, print speed)

### Equipment Required

| Equipment | Qty/Learner | Machine Time (hours) |
|-----------|------------|----------------------|
| Bambulab P1S 3D Printer | 1 | 0.25 |
| AMS (Automatic Material System) | 1 (shared) | 0.25 |

**Notes:**  
- One printer per 2 learners is typical for a session (hands-on supervision)
- AMS is shared; focus on filament-loading demonstration in groups
- Filament pre-loaded by instructor; learners practice swap and reload

### Materials / Consumables

| Material | Qty/Learner | Unit |
|----------|------------|------|
| PLA Filament (assorted colors for demo) | 20 | grams |
| Test print sample (pre-sliced STL) | 1 | file |

**Notes:**  
- Small test prints (~10-15g each) to demonstrate print quality
- Filament provided; students won't consume significant amounts in this session

### Content & Activities

#### Lecture: FDM Printing Basics (20 min)

- What is 3D printing? FDM technology overview
- Key components: Hotend, nozzle, bed, extruder, stepper motors
- Why Bambulab P1S? Speed, reliability, AMS advantage
- Safety reminders: hot surfaces, nozzle burns, sharp objects

#### Demonstration: Printer Tour & Startup (15 min)

- Physical walkthrough of Bambulab P1S: cover each major part
- How to safely power on and home axes
- Displaying bed temperature, nozzle temperature on control panel
- Show the AMS unit: slots, filament path, sensor locations

#### Hands-On: Filament Loading (30 min)

**Activity 1: Load single filament into extruder**
- Preheat nozzle to 200°C (instructor demonstrates, then students repeat under supervision)
- Insert filament into extruder and feed to nozzle
- Feel the resistance when filament reaches nozzle
- Remove filament and return to room temperature

**Activity 2: Load filament into AMS (groups of 2–3)**
- Understand AMS slot numbering (Slot 1, 2, 3, 4)
- Insert sample PLA into Slot 1 (instructor guides first, then students follow)
- Observe sensor light confirmation
- Trigger AMS load sequence on printer display
- Watch filament feed through AMS tubes to extruder

#### Group Discussion: Common Issues (10 min)

- What happens if filament isn't fully inserted? (jam risk)
- Sensor malfunction? (how to detect)
- Quick reset: how to eject filament from AMS

#### Closing & Safety Check (5 min)

- Recap: hotend is hot, never touch during operation
- Confirm all learners understand "hands-off" rule once print starts
- Q&A

### Assessment

- **Knowledge Check:** Verbal quiz (3–5 questions on components and safety)
  - E.g., "What temperature does the nozzle heat to for PLA?" (Answer: 200–210°C)
  - E.g., "Name two parts of the Bambulab P1S." (Answer: nozzle, bed, extruder, AMS, etc.)
- **Practical Observation:** Instructor observes each learner successfully loading filament into AMS without assistance
- **Mastery Threshold:** 80% (must answer ≥2/3 knowledge questions correctly + demonstrate filament loading)

### Project Deliverable

N/A (observational assessment only)

### Prerequisite for Next Session

Students must achieve mastery in Session 1 before proceeding to Session 2.

---

## Session 2: Model Preparation & Slicing

**Lesson Code:** `LAB-3DPRINT-02`  
**Lesson Title (for LMS Course Lesson link):** "3D Printing Session 2 — Model Prep & Slicing"  
**Duration:** 1.5 hours (90 minutes)  
**Lesson Type:** Hands-On (lecture + computer lab + supervised practice)  
**Prerequisite Lesson:** Session 1 (mastery required)

### Learning Objectives

By the end of this session, students will be able to:

1. **Understand** what a 3D model file is (STL, 3MF formats)
2. **Apply** Bambu Studio slicer software to prepare models
3. **Analyze** layer height and wall thickness trade-offs
4. **Apply** basic print settings: layer height, nozzle temp, bed temp, print speed
5. **Recall** how to generate and export a G-code file to the printer

### Equipment Required

| Equipment | Qty/Learner | Machine Time (hours) |
|-----------|------------|----------------------|
| Computer (with Bambu Studio installed) | 1 | 1.0 |
| Bambulab P1S 3D Printer (preview only) | 1 | 0.25 |
| AMS (Automatic Material System) | 1 (shared) | 0 |

**Notes:**  
- Students use lab computers (Windows/Mac) with pre-installed Bambu Studio slicer
- Printer stays idle during this session (demo at end only)
- No active printing to conserve material/time

### Materials / Consumables

| Material | Qty/Learner | Unit |
|----------|------------|------|
| Sample 3D models (STL files) | 3 | files |
| PLA Filament (for preview only) | 5 | grams |

**Notes:**  
- Pre-downloaded model library (benchy boat, calibration cube, articulated dragon)
- Students slice models but don't print during this session

### Content & Activities

#### Lecture: 3D File Formats & Slicing (15 min)

- What is STL (Stereolithography)? Why it's the industry standard
- What is 3MF? Advantages (color, supports baked in)
- What is slicing? Converting 3D model → G-code (printer instructions)
- Bambu Studio overview: UI walkthrough

#### Computer Lab: Guided Slicing (50 min)

**Activity 1: Load a model into Bambu Studio (15 min)**
- Open Bambu Studio on lab computer
- File → Open → select "benchy_boat.stl"
- Rotate, pan, zoom the model in the 3D view
- Observe model dimensions and estimated weight

**Activity 2: Adjust print settings (20 min)**
- Show default profile: 0.2 mm layer height, 210°C nozzle
- Explain: layer height ↓ = finer detail but slower; layer height ↑ = faster but rougher
- Set layer height to 0.1 mm (fine detail) — observe print time increase (demo)
- Set nozzle temp to 220°C (help adhesion) — explain viscosity concept
- Set bed temp to 60°C (PLA standard)
- Set print speed to 100 mm/s (default for quality)
- Observe updated preview: print time, filament weight, estimated cost

**Activity 3: Slice and preview (10 min)**
- Click "Slice" button
- Bambu Studio generates G-code layer preview
- Zoom through first 5 layers — understand visual preview of print path
- Observe nozzle travel moves (arrows show non-printing movements)

**Activity 4: Export G-code to USB (5 min)**
- Save as "benchy_0.1mm.gcode" to USB drive
- Show file browser: .gcode file is now on USB
- Explain: this file goes to printer USB input or via Wi-Fi

#### Demonstration: Printer Preview (10 min)

- Open saved G-code on Bamlab P1S display via USB
- Show "Preview" function: printer displays first layer path on bed
- Explain: this helps check for print placement before printing

#### Activity: Student Practice (10 min)

- Each student loads a second model (calibration cube) into Bambu Studio
- Adjusts layer height and temperature (with instructor guidance)
- Exports G-code to USB
- Instructor spot-checks 1–2 exports for correctness

#### Closing Discussion: Print Failure Prevention (5 min)

- Common slicing mistakes: model floating in air, supports missing, wall thickness too thin
- Preview is your friend: always use preview before hitting print
- Save multiple versions (benchy_0.2mm, benchy_0.1mm) for comparison

### Assessment

- **Knowledge Check:** Practical quiz (4–6 questions)
  - E.g., "What layer height gives finer detail?" (Answer: 0.1 mm vs 0.2 mm)
  - E.g., "What is G-code?" (Answer: printer instructions generated by slicer)
- **Practical Observation:** Each student successfully slices and exports one model without instructor intervention
- **Deliverable:** USB with exported .gcode file (proof of slicing)
- **Mastery Threshold:** 80% (must answer ≥4/5 knowledge questions correctly + demonstrate export)

### Project Deliverable

"benchy_0.1mm.gcode" file exported to USB — submitted to instructor for verification

### Prerequisite for Next Session

Students must achieve mastery in Session 2 before proceeding to Session 3 (printing).

---

## Session 3: Multi-Material Printing & Troubleshooting

**Lesson Code:** `LAB-3DPRINT-03`  
**Lesson Title (for LMS Course Lesson link):** "3D Printing Session 3 — Multi-Material Printing & Troubleshooting"  
**Duration:** 2 hours (120 minutes)  
**Lesson Type:** Hands-On (demo + supervised operation + troubleshooting)  
**Prerequisite Lesson:** Session 2 (mastery required)

### Learning Objectives

By the end of this session, students will be able to:

1. **Understand** multi-material printing workflow (AMS tool changes)
2. **Apply** multi-material settings in Bambu Studio (color assignments per model part)
3. **Analyze** common print failures: bed adhesion, warping, AMS jams, stringing
4. **Apply** corrective actions: re-level bed, adjust temp, clear jams, resume prints
5. **Evaluate** print quality and decide on rework vs. reprint

### Equipment Required

| Equipment | Qty/Learner | Machine Time (hours) |
|-----------|------------|----------------------|
| Bambulab P1S 3D Printer | 1 | 1.5 |
| AMS (Automatic Material System) | 1 | 1.5 |
| Printer USB drive (with pre-sliced multi-color model) | 1 | 0.5 |

**Notes:**  
- Active printing occurs this session
- One printer per 3–4 learners (supervised print monitoring)
- AMS will perform automatic material swaps during print

### Materials / Consumables

| Material | Qty/Learner | Unit |
|----------|------------|------|
| PLA Filament — Black | 50 | grams |
| PLA Filament — White | 30 | grams |
| PLA Filament — Red | 20 | grams |
| Isopropyl Alcohol (bed cleaning) | 10 | mL |

**Notes:**  
- Three-color benchy boat print: black hull, white cabin, red details
- Filament in AMS Slots 1–3 pre-loaded by instructor
- Alcohol for bed cleaning between prints

### Content & Activities

#### Lecture: Multi-Material Printing (15 min)

- Concept: AMS holds up to 4 different filaments
- Printer pauses, swaps tool (AMS motor), resumes print
- Why multi-material? Design freedom, assembly reduction
- Bambu Studio: model color assignment workflow

#### Demonstration: AMS Tool Change Sequence (10 min)

- Show AMS state: Slots 1–3 loaded with black, white, red
- Manually trigger a tool change from Slot 1 → Slot 2 on printer display
- Observe: AMS retracts old filament, feeds new filament through PTFE tube to extruder
- Discuss: time cost (~30 sec per tool change), minimal impact on multi-color parts

#### Supervised Print Activity (60 min)

**Activity 1: Prepare multi-color model (10 min)**
- Instructor demonstrates: load pre-sliced "benchy_multicolor.gcode" onto printer USB
- Show preview on printer display: highlight color transitions (Slot 1 → Slot 2 → Slot 1 layers)
- Explain: slicer already told printer where to change materials

**Activity 2: Bed prep (5 min)**
- Inspect Bambalab P1S heated bed: must be clean
- If needed: wipe with isopropyl alcohol-soaked cloth (cooled printer only)
- Let dry 1 min
- Explain: dust, fingerprints = failed prints, poor adhesion

**Activity 3: Start print (5 min)**
- Preheat printer: nozzle to 220°C, bed to 60°C (slicer settings applied automatically)
- Load USB file: "benchy_multicolor.gcode"
- Confirm AMS has filament in all three slots (visual check + sensor lights)
- Instructor starts print; students observe first layer deposit
- Explain: first layer is critical — watch for adhesion, gaps, over-extrusion

**Activity 4: Monitor print & AMS tool changes (30 min)**
- Students stand by and monitor print for ~30 min (active printing time)
- Instructor narrates when AMS tool change will occur (e.g., "At 15% complete, tool change to white")
- Students observe tool change in real-time: printer pauses, retraction, AMS motor spin, feed, resume
- Discuss observations: How smooth was the tool change? Any visible seam?
- If any issues arise (jam, adhesion failure), instructor demonstrates troubleshooting (see below)

**Activity 5: Print completion & inspection (10 min)**
- Print completes (~1.5 hours wall-clock time; students witness final 10 min)
- Cooled part removed from bed
- Group inspection: compare expected multi-color result to actual
- Discuss: layer quality, color transitions, dimensional accuracy

#### Troubleshooting Scenarios (demo; apply if issues occur during print) (20 min)

**Scenario A: AMS Jam**
- **Symptom:** Printer pauses indefinitely during tool change; error message on display
- **Diagnosis:** Filament caught in AMS tube or sensor misdetected
- **Fix (Instructor demonstrates):**
  - Open AMS unit (tool-free design — just pop open)
  - Visually inspect for tangled filament
  - If jammed: carefully pull stuck filament out
  - Re-insert into slot; close AMS
  - Printer resumes print automatically

**Scenario B: Bed Adhesion Failure**
- **Symptom:** First layer lifts off bed; printed lines are inconsistent
- **Diagnosis:** Bed may be tilted (uneven), or nozzle too high
- **Fix (Instructor demonstrates):**
  - Pause print on display
  - Let nozzle cool (safety)
  - Visually check: is bed level? Is nozzle spacing even?
  - If bed tilt: raise affected corner slightly (bed adjustment screws, gentle)
  - If nozzle too high: Bambu Studio offers "babystep" (tiny adjustment during print)
  - Resume print

**Scenario C: Stringing (Filament Wisps)**
- **Symptom:** Thin filament strands between disconnected areas (cosmetic issue)
- **Cause:** Nozzle ooze; retractionToo weak
- **Fix:** Higher retraction speed (tweak in Slicer for next print)
- **Immediate action:** Cosmetic only; print is viable

#### Group Debrief (10 min)

- What went well? (successful tool changes, print quality?)
- What surprised you? (speed of AMS, tool change seamlessness?)
- Questions about print failures we didn't encounter today?

### Assessment

- **Knowledge Check:** Verbal quiz (4 questions, asked during/after printing)
  - E.g., "Why did the printer pause mid-print?" (Answer: AMS tool change)
  - E.g., "How do you fix an AMS jam?" (Answer: open unit, remove filament, re-insert)
  - E.g., "What causes bed adhesion failure?" (Answer: uneven bed, nozzle too high)
  - E.g., "Name one way to prevent stringing." (Answer: higher retraction speed)
- **Practical Observation:** Instructor observes each student successfully troubleshooting one of the scenarios (or observes calm behavior during a real issue)
- **Print Artifact:** Multi-color benchy boat print (community showcase item)
- **Mastery Threshold:** 80% (must answer ≥3/4 knowledge questions correctly + demonstrate troubleshooting logic)

### Project Deliverable

**Final Project:** Successfully completed multi-color 3D print (benchy boat with black, white, red layers). Print is kept by student or donated to maker-space showcase/gift.

---

## Overall Assessment & Completion Criteria

To **complete** the "3D Printing Fundamentals with Bambulab P1S + AMS" course:

1. ✅ Pass Session 1 knowledge check (≥80% on safety/components quiz)
2. ✅ Demonstrate Session 1 filament loading without instructor intervention
3. ✅ Pass Session 2 knowledge check (≥80% on slicing/settings quiz)
4. ✅ Successfully export at least one .gcode file in Session 2
5. ✅ Pass Session 3 knowledge check (≥80% on troubleshooting quiz)
6. ✅ Successfully complete multi-color print in Session 3
7. ✅ Demonstrate understanding of one troubleshooting scenario

**Certificate/Badge Earned:** "3D Printing Operator — Bambulab P1S + AMS Certified"  
**Valid for:** Use of Bambulab P1S and AMS at BitandBrick HQ with supervisor sign-off

---

## Instructor Notes & Resources

### Preparation Checklist

- [ ] Bambulab P1S powered on, heated bed cleaned, nozzle clear
- [ ] AMS loaded with PLA (black, white, red) in Slots 1–3
- [ ] USB drive with pre-sliced test models (benchy_0.1mm.gcode, benchy_multicolor.gcode)
- [ ] Isopropyl alcohol and lint-free cloth available
- [ ] Spare filament (1 spool each color) for backup
- [ ] Lab computers with Bambu Studio installed and tested
- [ ] Sample 3D model files downloaded (benchy, calibration cube, articulated dragon)
- [ ] Printer manual + AMS manual available for reference

### Time Allocation by Session

| Session | Lecture | Demo | Hands-On | Assessment | Total |
|---------|---------|------|----------|-----------|-------|
| 1       | 20 min  | 15 min | 30 min   | 15 min    | 90 min |
| 2       | 15 min  | 10 min | 50 min   | 15 min    | 90 min |
| 3       | 15 min  | 10 min | 60 min   | 20 min    | 120 min |
| **Total** | **50 min** | **35 min** | **140 min** | **50 min** | **5 hrs** |

### Risk Mitigation

| Risk | Mitigation |
|------|-----------|
| Student burns nozzle | Safety review at start; "hot surface" stickers; instructor always present during heating |
| Print failure wastes material | Pre-slice models; use small test prints; have backup models ready |
| AMS jam mid-session | Keep spare filament; know jam-clearing steps; have manual eject option available |
| Time overrun | Session 3 print can run in background; adjust debrief length if needed |
| Student inattention during long printing | Structured monitoring (assign roles: "watch extruder," "watch AMS," "watch bed adhesion") |

### Recommended Readings & Links

1. **Bambulab Official Resources:**
   - [Bambulab Knowledge Base](https://wiki.bambulab.com) — troubleshooting, maintenance
   - [Bambu Studio Docs](https://wiki.bambulab.com/en/software/bambu-studio/overview) — slicer guide

2. **3D Printing Best Practices:**
   - "The Complete 3D Printing Handbook" (PDF; find via maker forums)
   - [Prusa 3D Printing Handbook](https://shop.prusa3d.com/en/prusahandbook.html) — general FDM theory applies to P1S

3. **AMS-Specific:**
   - Bambulab P1S + AMS Assembly Manual (included with printer)
   - Sensor calibration guide (on wiki.bambulab.com)

---

## Integration with FabInABox Data Model

### Mapping to "Atomic Learning" Doctype

This content set maps to three **Atomic Learning** doctype records (one per session):

#### Session 1: Atomic Learning Record

```
lesson_title: (Link to Course Lesson) "3D Printing Session 1 — Intro & Printer Setup"
lesson_code: LAB-3DPRINT-01
description: "Introduction to FDM printing, Bambulab P1S components, safety, and filament loading with AMS."
difficulty_level: Beginner
duration_hours: 1
duration_minutes: 30

objectives (Lesson Objective child table):
  - Objective 1: Explain FDM technology and Bambalab P1S architecture [level: Recall]
  - Objective 2: Demonstrate correct startup and safety procedures [level: Apply]
  - Objective 3: Load filament into extruder and AMS system [level: Apply]

equipment (Lesson Equipment child table):
  - Equipment Item: Bambulab P1S 3D Printer
    Qty per Learner: 0.5
    Total Session Hours: 0.25
  - Equipment Item: AMS (Automatic Material System)
    Qty per Learner: 0.5 (shared)
    Total Session Hours: 0.25

materials (Lesson Material child table):
  - Material Item: PLA Filament
    Qty per Learner: 0.02 kg
    Unit: kg

prerequisites (Lesson Prerequisite child table):
  - Prerequisite Lesson: (Name of "General BitandBrick HQ Safety Orientation" Atomic Learning record, once created)
    Mastery Required: Yes

status: Draft (or "Published" once tested in production)
```

#### Session 2: Atomic Learning Record

```
lesson_title: (Link to Course Lesson) "3D Printing Session 2 — Model Prep & Slicing"
lesson_code: LAB-3DPRINT-02
description: "Model file formats, slicing software (Bambu Studio), print settings, and G-code export workflow."
difficulty_level: Beginner
duration_hours: 1
duration_minutes: 30

objectives (Lesson Objective child table):
  - Objective 1: Explain 3D file formats (STL, 3MF) and slicing concept [level: Understand]
  - Objective 2: Configure Bambu Studio print settings (layer height, temp, speed) [level: Apply]
  - Objective 3: Generate and export G-code file [level: Apply]

equipment (Lesson Equipment child table):
  - Equipment Item: Lab Computer (with Bambu Studio)
    Qty per Learner: 1
    Total Session Hours: 1.0
  - Equipment Item: Bambulab P1S (preview only)
    Qty per Learner: 0.25
    Total Session Hours: 0.25

materials (Lesson Material child table):
  - Material Item: PLA Filament (for preview)
    Qty per Learner: 0.005 kg
    Unit: kg

prerequisites (Lesson Prerequisite child table):
  - Prerequisite Lesson: LAB-3DPRINT-01
    Mastery Required: Yes

status: Draft
```

#### Session 3: Atomic Learning Record

```
lesson_title: (Link to Course Lesson) "3D Printing Session 3 — Multi-Material Printing & Troubleshooting"
lesson_code: LAB-3DPRINT-03
description: "Multi-material AMS printing, print monitoring, common failures, and troubleshooting strategies."
difficulty_level: Beginner
duration_hours: 2
duration_minutes: 0

objectives (Lesson Objective child table):
  - Objective 1: Explain multi-material AMS workflow and tool changes [level: Understand]
  - Objective 2: Monitor active print and AMS operations [level: Apply]
  - Objective 3: Diagnose and fix bed adhesion, AMS jams, and stringing issues [level: Analyze]

equipment (Lesson Equipment child table):
  - Equipment Item: Bambulab P1S 3D Printer
    Qty per Learner: 0.33 (1 per 3 learners)
    Total Session Hours: 1.5
  - Equipment Item: AMS (Automatic Material System)
    Qty per Learner: 0.33 (1 per 3 learners)
    Total Session Hours: 1.5

materials (Lesson Material child table):
  - Material Item: PLA Filament — Black
    Qty per Learner: 0.05 kg
    Unit: kg
  - Material Item: PLA Filament — White
    Qty per Learner: 0.03 kg
    Unit: kg
  - Material Item: PLA Filament — Red
    Qty per Learner: 0.02 kg
    Unit: kg
  - Material Item: Isopropyl Alcohol (bed cleaning)
    Qty per Learner: 0.01 liters
    Unit: liters

prerequisites (Lesson Prerequisite child table):
  - Prerequisite Lesson: LAB-3DPRINT-02
    Mastery Required: Yes

status: Draft
```

### Next Steps for Implementation

1. **Create native LMS Course Lesson records** (in Frappe LMS app) with titles matching the `lesson_title` links above
   - E.g., create Course Lesson with title = "3D Printing Session 1 — Intro & Printer Setup" in the "Bambulab 3D Printing" course chapter
   - This provides the LMS-managed content (video, body markdown, instructor notes, quiz) that Atomic Learning records link to

2. **Create Lab Equipment record** for Bambulab P1S with AMS
   - Name: "Bambulab P1S 3D Printer with AMS"
   - Category: Fabrication
   - Location: BitandBrick HQ
   - Hourly Rate: (set per your pricing model)
   - Requires Training: Yes
   - Training Lesson: LAB-3DPRINT-01 (once created as Atomic Learning)

3. **Create Item records** for consumables
   - "PLA Filament — Black" (Item), unit: kg, warehouse: BitandBrick HQ storage
   - "PLA Filament — White" (Item)
   - "PLA Filament — Red" (Item)
   - "Isopropyl Alcohol" (Item)

4. **Create Instructor record** (or update existing facilitator) with:
   - Skill Domain: Digital Fabrication (Beginner-friendly)
   - Equipment Certification: Bambulab P1S (certified, non-expiring or 1-year)
   - Primary Location: BitandBrick HQ

5. **Create Skill Domain** (if not already created)
   - Domain Name: "Digital Fabrication"
   - Description: "3D printing, laser cutting, CNC machining, and other subtractive/additive tools"

6. **Test the full booking flow:**
   - Create a Resource Booking for an individual learner (or school group)
   - Booking Purpose: "Course Outcome" → link to a Course Outcome that includes these three Atomic Learning sessions
   - Verify equipment/materials/instructor availability check works
   - Confirm cost calculation includes equipment hours + instructor time + materials

---

## Notes for FabInABox Schema Evolution

**Atomic Learning (renamed from custom Course Lesson)** now serves as the maker-space-specific extension layer on top of native Frappe LMS Course Lesson. This structure allows:

- **LMS Content:** Native Course Lesson holds video, body markdown, instructor notes, and quiz (managed by LMS app)
- **Maker-space Metadata:** Atomic Learning holds equipment, materials, duration, objectives, prerequisites, and status (managed by Everyone Can Make app)
- **Linking:** Atomic Learning.lesson_title → Course Lesson (1-to-many; one native lesson can be referenced by multiple Atomic Learning records, e.g., if same content offered in different contexts)

This separation prevents data duplication and enables clean integration between Frappe LMS and the custom booking/resource-management layer.

---

**Document Version:** 1.0  
**Last Updated:** 2026-08-10  
**Author:** FabInABox Content Team  
**Status:** Sample Content (Draft)
