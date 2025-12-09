# Scheduling for Computer Science
### What Faculty should look at
- [Fall Schedule by instructor](schedule_Fall_2026_by_instructor.csv)
- [Winter Schedule by instructor](schedule_Winter_2027_by_instructor.csv)
- [Spring Schedule by instructor](schedule_Spring_2027_by_instructor.csv)
- [Summer Schedule by instructor](schedule_Spring_2027_by_instructor.csv)

- See if the classes you are scheduled to teach are what you expected.
= See if the time and location will work for you.

### Notation
The room schedule files show when rooms are used ([Fall](room_schedule_Fall_2026.csv), [Winter](room_schedule_Winter_2027.csv), [Spring](room_schedule_Spring_2027.csv), [Summer](room_schedule_Summer_2027.csv))
- An X in the schedule file indicates that this time has a conflict with another time that is currently scheduled (For example, if there is a MW class scheduled in a room, then MWF would have an X)
- A 0 in the schedule file indicates that the room does not have priority 1 scheduling for the Computer Science Department.

### Conflicts  
Please check the conflicts files for [Fall](conflicts_Fall_2026.csv), [Winter](conflicts_Winter_2026.csv), [Spring](conflicts_Spring_2026.csv), [Summer](conflicts_Summer_2026.csv) to see if there are classes that students would need to take together that are offered at the same time.

# Course Scheduling Solution - Fall 2026

# Course Scheduling System Documentation

## Overview

This system schedules university courses to rooms and time slots using Integer Linear Programming (ILP) with the PuLP library and CBC solver. It handles complex constraints including room capacities, instructor availability, student enrollment, and various faculty preferences.

---

## Constraint Handling

The system categorizes constraints into **Hard** (must be satisfied) and **Soft** (preferences that influence the objective function).

### Hard Constraints

Hard constraints must be satisfied for a valid schedule. If any hard constraint cannot be satisfied, the solver reports the schedule as infeasible.

| Constraint | Weight | Description |
|------------|--------|-------------|
| **Each Section Scheduled Once** | 100 | Every in-person section must be assigned to exactly one room-pattern combination |
| **Room Conflicts** | 100 | No room can be double-booked at the same day/time |
| **Instructor Conflicts** | 100 | No instructor can teach multiple overlapping sections |
| **Enrollment Conservation** | 100 | Sum of section enrollments must equal the course total enrollment |
| **Linearized Capacity** | 95 | Enrollment in a section cannot exceed the assigned room's capacity |
| **Fixed Enrollment** | 85 | When `--equal-sections` mode is enabled, enforce specific enrollment values per section |
| **Same-Course Section Conflicts** | 80 | Different sections of the same course must be at different times |
| **Equal Enrollment (Same Instructor)** | 75 | Sections taught by the same instructor must have similar enrollment (within ±20 students) |
| **Instructor Building Restrictions** | 70 | Specific instructors cannot teach in certain buildings |
| **Same-Course Same-Day Pattern** | 40 | Multiple sections by the same instructor must use the same day pattern (MW, TTh, etc.) |
| **Same-Course Same-Room** | 35 | Sections of the same course should use the same room when preferences indicate |
| **Large Section Preference** | 25 | Instructors with `prefers_large=TRUE` get higher enrollment allocation |
| **Evening Time Restrictions** | Hard | Evening classes (ending with 'E') scheduled only at/after 5pm; daytime classes before 5pm |

### Individual Instructor Constraints

Specific constraints are hardcoded for certain instructors:

- **Angela Jones**: MW or TTh pattern only, exactly 9:30 AM start
- **Fred Clift**: 2:00 PM or 3:00 PM start time
- **Deccio**: 9:00 AM or 10:00 AM start
- **Crandall (Course 235)**: MW pattern only
- **Goodrich**: Must end by 3:00 PM
- **Seamons**: Must end by 3:00 PM
- **Page**: 9:00 AM - 12:30 PM time window (except Course 601RB)
- **Mercer (Course 236)**: MWF 1:00 PM - 4:00 PM only
- **Courses 191/291**: 200+ capacity room, 3:00 PM or 4:00 PM start

### Soft Constraints

Soft constraints are incorporated into the objective function as bonuses or penalties:

| Constraint | Weight | Bonus | Description |
|------------|--------|-------|-------------|
| **Template Room/Time Preference** | 95 | Positive | Prefer matching historical template assignments |
| **Template Instructor Preference** | 90 | Positive | Prefer matching template for instructor schedules |
| **Priority 1 Room Slot Coverage** | 60 | +8,000 | Encourage filling high-priority room slots |
| **Faculty Teaching Preferences** | 50 | +50,000 | Same room preference bonus |
| **Same Pattern Preference** | 50 | +25,000 | Same day pattern bonus |
| **Adjacent Time Slots** | 45 | +15,000 | Encourage consecutive time slots for same-course sections |
| **Course Conflicts** | 20 | Variable | Ensure non-overlapping times for courses with student conflicts |

### Constraint Configuration

Constraints are configured via `inputs/constraint_weights.csv`:

```csv
Constraint Name,Weight,Type,Description,Currently Active
Room Conflicts,100,Hard,Prevent room double-booking,Yes
Priority Room Restrictions,30,Hard,Labs → P3/P4 only; Regular → P1/P2 only,No
```

Constraints can be enabled/disabled by setting the "Currently Active" column to "Yes" or "No".

---

# Course Scheduling System Documentation

## Overview

This system schedules university courses to rooms and time slots using **Integer Linear Programming (ILP)** with the PuLP library and CBC solver. The default execution uses pure ILP optimization without any template-based matching.

---

## How the System Actually Works

### Default Mode (ILP-Only)

When you run `python main.py "Fall 2026"` without any flags, the system:

1. **Parses input data**: Courses, rooms, meeting patterns, and conflicts
2. **Creates an ILP model** with:
   - Binary assignment variables `x[course, section, room, pattern]`
   - Integer enrollment variables `enrollment[course, section]`
   - Auxiliary preference tracking variables
3. **Optimizes** using CBC solver (up to 3600 seconds)
4. **Outputs** a CSV schedule with room assignments

**There is no template matching in default mode.** The ILP solver makes all room/time assignments from scratch based on constraints and objective function weights.

### Alternative Modes (Optional)

- `--use-template`: Template-based scheduling with historical data (not used by default)
- `--use-phased`: Three-phase approach combining templates and ILP (not used by default)
- `--equal-sections`: Forces equal enrollment distribution across sections

---

## Constraint Handling

### Hard Constraints

Hard constraints are enforced by the ILP solver - if any cannot be satisfied, the model is infeasible.

| Constraint | Weight | Description |
|------------|--------|-------------|
| **Each Section Scheduled Once** | 100 | Every in-person section assigned to at most one room-pattern combination |
| **Room Conflicts** | 100 | No room double-booked at same day/time (via constraint grouping) |
| **Instructor Conflicts** | 100 | No instructor teaching multiple overlapping sections |
| **Enrollment Conservation** | 100 | Sum of section enrollments ≤ course total enrollment |
| **Linearized Capacity** | 95 | Enrollment ≤ assigned room's capacity (Big-M linearization) |
| **Maximum Class Size** | - | No section exceeds 200 students |
| **Same-Course Section Conflicts** | 80 | Different sections of same course at different times |
| **Equal Enrollment (Same Instructor)** | 75 | Sections by same instructor within ±20 students |
| **Instructor Building Restrictions** | 70 | Specific instructors banned from specific buildings |

### Time-Based Constraints (Applied During Variable Creation)

These constraints filter which room-pattern combinations are even considered:

| Constraint | Effect |
|------------|--------|
| **No 8am classes** | Patterns starting before 9am excluded |
| **No classes after 8pm** | Patterns ending after 20:00 excluded |
| **Evening classes (E suffix)** | Only scheduled at/after 5pm |
| **Daytime classes** | Only scheduled before 5pm |
| **Labs (L suffix)** | Only MW or TTh patterns allowed |

### Individual Instructor Constraints (Hardcoded)

| Instructor | Constraint |
|------------|------------|
| **Angela Jones** | MW or TTh at exactly 9:30 AM only |
| **Fred Clift** | 2:00 PM or 3:00 PM start only |
| **Deccio** | 9:00 AM or 10:00 AM start only |
| **Crandall (235)** | MW pattern only |
| **Goodrich** | Must end by 3:00 PM |
| **Seamons** | Must end by 3:00 PM |
| **Page** | 9:00 AM - 12:30 PM window (exception: 601RB can use F-LONG2) |
| **Mercer (236)** | MWF 1:00 PM - 4:00 PM only |
| **191/291** | 200+ capacity room, 3:00 PM or 4:00 PM start |

### CSV-Configurable Constraints

Additional constraints loaded from CSV files:

- `inputs/faculty_constraints.csv`: Per-instructor building restrictions, time ranges, preferences
- `inputs/course_constraints.csv`: Per-course time ranges, room requirements
- `inputs/constraint_weights.csv`: Enable/disable constraints, adjust weights

---

## Scheduling Algorithm

### ILP Model Structure

```
MAXIMIZE:
  Σ (capacity_weight + priority_bonus + prime_time_bonus +
     course_type_bonus + large_section_bonus + capacity_match_bonus) * x[c,s,r,p]
  + slot_coverage_weight * slot_used[r,p]
  + large_enrollment_weight * enrollment[c,s]  (for prefers_large instructors)
  + same_room_bonus * same_room[i,c1,c2]
  + same_pattern_bonus * same_pattern[i,c1,c2]
  + adjacent_slots_bonus * adjacent[i,c,s1,s2]

SUBJECT TO:
  1. Each section scheduled at most once
  2. Enrollment ≤ room capacity (linearized)
  3. Total enrollment ≤ course enrollment
  4. No room conflicts (same room, overlapping times)
  5. No instructor conflicts (same instructor, overlapping times)
  6. Same-course sections at different times
  7. Equal enrollment for same-instructor sections
  8. Building restrictions
  9. Time proximity constraints
  10. Faculty preference linking constraints
```

### Objective Function Weights

The objective function prioritizes assignments using these weights:

| Component | Weight Range | Purpose |
|-----------|--------------|---------|
| **Capacity Match** | -200,000 to +100,000 | Match class size to room size |
| **Priority 1 Room** | +10,000 | Prefer Priority 1 rooms |
| **Priority 2 Room** | +5,000 | Allow Priority 2 when needed |
| **Prime Time (P1)** | +15,000 | Fill P1 rooms during 9am-4pm |
| **Slot Coverage** | +8,000 | Encourage using P1 slots |
| **Same Room** | +50,000 | Reward instructor same-room preference |
| **Same Pattern** | +25,000 | Reward instructor same-pattern preference |
| **Adjacent Slots** | +15,000 | Encourage consecutive sections |

### Capacity Matching Logic

```
Large class (≥150 students):
  - Large room (≥200): +100,000
  - Medium room (100-199): -20,000
  - Small room (<100): -50,000

Medium class (80-149):
  - Large room: -10,000
  - Medium room: +80,000
  - Small room: -30,000

Small class (<80):
  - Large room: -200,000 (heavy penalty!)
  - Medium room: -50,000
  - Small room: +50,000
```

### Algorithm Flow

```
1. PARSE DATA
   ├── Load courses (name, sections, instructors, enrollment)
   ├── Load rooms (building, capacity, priority, availability)
   ├── Load meeting patterns (days, times)
   └── Load conflict pairs

2. CREATE VARIABLES
   ├── x[c,s,r,p] for each valid (course, section, room, pattern)
   │   └── Filter out: invalid patterns, time violations, instructor constraints
   ├── enrollment[c,s] for each in-person section
   ├── slot_used[r,p] for Priority 1 room slots
   └── Preference tracking variables (same_room, same_pattern, adjacent)

3. BUILD MODEL
   ├── Set objective function (maximize weighted assignments)
   └── Add constraints

4. SOLVE
   ├── Use PuLP + CBC solver
   ├── Time limit: 3600 seconds
   └── Find optimal or best feasible solution

5. EXTRACT SOLUTION
   ├── For each x[c,s,r,p] = 1: record assignment
   ├── Get enrollment[c,s] values
   └── Calculate utilization

6. OUTPUT
   └── Write results/schedule_<semester>.csv
```

---

## Results Analysis (Fall 2026)

### Schedule Statistics

Based on `results/schedule_Fall_2026.csv`:

| Metric | Value |
|--------|-------|
| **Total Sections Scheduled** | 78 |
| **Total Students** | 3,830 |
| **Average Room Utilization** | 44.6% |
| **Rooms Used** | 12 |

### Room Utilization by Building

| Building | Room | Capacity | Priority | Classes | Avg Utilization |
|----------|------|----------|----------|---------|-----------------|
| TMCB | 1170 | 203 | 1 | 13 | 47.5% |
| TMCB | 120 | 41 | 1 | 8 | 35.4% |
| TMCB | 134 | 42 | 1 | 9 | 55.7% |
| JKB | 3108 | 306 | 2 | 9 | 21.4% |
| JKB | 1102 | 285 | 2 | 5 | 15.5% |
| ESC | C215 | 170 | 2 | 10 | 31.3% |
| MARB | 130 | 72 | 1 | 11 | 62.2% |
| HBLL | 3718 | 59 | 1 | 8 | 57.0% |

### Key Observations

1. **High Utilization Rooms**: MARB 130 (62.2%) and HBLL 3718 (57.0%) are efficiently used
2. **Underutilized Large Rooms**: JKB 3108 (306 seats) at 21.4% - many small classes placed here
3. **Priority 1 Usage**: Most Priority 1 rooms are well-utilized except TMCB 120
4. **Capacity Mismatch**: Several small classes (30 students) in 200+ seat rooms

### Sample Assignments

```
Course  Section  Instructor       Enroll  Room         Capacity  Util%
110     1        Bean             200     TMCB 1170    203       98.5%
110     2        Page             200     JKB 3108     306       65.4%
111     3        Richardson       200     TMCB 1170    203       98.5%
236     4        Mercer           169     TMCB 1170    203       83.3%
329     1        Jensen           69      MARB 130     72        95.8%
401R    1        Bean             42      TMCB 134     42        100.0%
```

---

## Input/Output Files

### Input Files

| File | Purpose |
|------|---------|
| `inputs/Teaching Assignments True 2015-2026 - 2026-2027 Faculty.csv` | Instructor-course assignments |
| `inputs/Teaching Assignments True 2015-2026 - 2026-2027.csv` | Course sections and enrollment |
| `inputs/Teaching Assignments True 2015-2026 - Priority 1 Classrooms.csv` | Room information |
| `inputs/Teaching Assignments True 2015-2026 - Meeting Patterns Extended.csv` | Time slot patterns |
| `inputs/class conflicts - Sheet1.csv` | Course conflict pairs |
| `inputs/constraint_weights.csv` | Constraint configuration |
| `inputs/faculty_constraints.csv` | Per-instructor constraints |
| `inputs/course_constraints.csv` | Per-course constraints |

### Output Files

| File | Purpose |
|------|---------|
| `results/schedule_<semester>.csv` | Final schedule with all assignments |
| `results/schedule_<semester>_by_instructor.csv` | Schedule sorted by instructor |
| `results/equal_schedule_<semester>.csv` | Output when using `--equal-sections` |

---

## Usage

```bash
# Default: Pure ILP optimization (recommended)
python main.py "Fall 2026"

# With equal enrollment distribution
python main.py "Fall 2026" --equal-sections

# Template-based (uses historical data)
python main.py "Fall 2026" --use-template

# Phased (template + ILP for remaining)
python main.py "Fall 2026" --use-phased
```

---

## Technical Details

### Solver Configuration

- **Solver**: COIN-OR Branch and Cut (CBC) via PuLP
- **Time Limit**: 3600 seconds (1 hour)
- **Model Size** (typical): ~5,000 rows, ~17,000 columns, ~230,000 elements

### Key Algorithms

**Time Overlap Detection:**
```python
def times_overlap(start1, end1, start2, end2):
    s1, e1 = to_minutes(start1), to_minutes(end1)
    s2, e2 = to_minutes(start2), to_minutes(end2)
    return s1 < e2 and s2 < e1
```

**Pattern Matching:**
- MW courses match MW patterns
- TTh courses match TTh patterns
- MWF courses match MWF patterns
- Single-day courses match only single-day patterns
- Special patterns (F-LONG, F-LONG2) require exact match

**Big-M Linearization:**
```
enrollment[c,s] <= Σ(capacity[r] * x[c,s,r,p])
```
Ensures enrollment fits in whichever room is assigned.

---

## Constraint Weights Configuration

From `inputs/constraint_weights.csv`:

| Constraint | Weight | Type | Active |
|------------|--------|------|--------|
| Each Section Scheduled Once | 100 | Hard | Yes |
| Room Conflicts | 100 | Hard | Yes |
| Instructor Conflicts | 100 | Hard | Yes |
| Enrollment Conservation | 100 | Hard | Yes |
| Linearized Capacity | 95 | Hard | Yes |
| Template Room/Time Preference | 95 | Soft | Yes |
| Template Instructor Preference | 90 | Soft | Yes |
| Fixed Enrollment | 85 | Hard | Yes |
| Same-Course Section Conflicts | 80 | Hard | Yes |
| Equal Enrollment Same Instructor | 75 | Hard | Yes |
| Instructor Building Restrictions | 70 | Hard | Yes |
| Priority 1 Room Slot Coverage | 60 | Soft | Yes |
| Faculty Teaching Preferences | 50 | Soft | Yes |
| Adjacent Time Slots Preference | 45 | Soft | Yes |
| Same-Course Same-Day Pattern | 40 | Hard | Yes |
| Same-Course Same-Room | 35 | Hard | Yes |
| Priority Room Restrictions | 30 | Hard | **No** |
| Large Section Preference | 25 | Hard | Yes |
| Course Conflicts | 20 | Soft | Yes |

---

## Summary

The scheduling system uses **pure Integer Linear Programming** in default mode to:

1. Assign each course section to exactly one room and time slot
2. Distribute enrollment across sections to fit room capacities
3. Respect all hard constraints (no conflicts, capacity limits)
4. Optimize for preferences (same room for instructor, good capacity fit)

The solver finds optimal or near-optimal solutions within the 1-hour time limit, producing schedules that balance room utilization with instructor preferences and student needs.

