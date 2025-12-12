# Course Scheduling System

## Overview

The Course Scheduling System is an automated solution for assigning university courses to classrooms and time slots. It uses Integer Linear Programming (ILP) to find optimal room and time assignments while respecting a comprehensive set of hard and soft constraints.

## System Architecture

### Core Components

| Component | File | Description |
|-----------|------|-------------|
| Main Entry Point | `main.py` | CLI interface and orchestration |
| ILP Scheduler | `scheduler.py` | Core optimization engine using PuLP |
| Template Scheduler | `template_scheduler.py` | Historical data-based scheduling |
| Phased Scheduler | `phased_scheduler.py` | Three-phase scheduling approach |
| Data Parser | `parser_faculty.py` | CSV parsing for courses, rooms, patterns |
| Constraint Weights | `constraint_weights.py` | Configurable constraint priorities |

### Scheduling Modes

1. **ILP-Only Mode** (default): Uses pure optimization to find assignments
2. **Template-Based Mode** (`--use-template`): Uses historical enrollment data as templates
3. **Phased Mode** (`--use-phased`): Three-phase approach:
   - Phase 1: Template-based scheduling
   - Phase 2: ILP with Priority 1-2 rooms only
   - Phase 3: ILP with Priority 3-4 rooms only
4. **Equal Sections Mode** (`--equal-sections`): Distributes enrollment equally across sections

## Data Structures

### Course
```
Course:
  - name: str (e.g., "110", "235E", "191/291T")
  - semester: str (e.g., "Fall 2026")
  - instructors: List[str] (one per section)
  - total_enrollment: int
  - meeting_pattern: str (e.g., "MW", "TTh", "MWF")
  - is_online: List[bool] (per section)
  - prefers_large: List[bool] (per section)
  - enrollments: Optional[List[int]] (fixed enrollment per section)
```

### Room
```
Room:
  - building: str (e.g., "TMCB", "JKB")
  - room_number: str (e.g., "1170", "3108")
  - capacity: int
  - priority: int (1 = highest, 2-4 = lower)
  - available_days: Set[str]
  - available_start: time
  - available_end: time
```

### MeetingPattern
```
MeetingPattern:
  - pattern_name: str (e.g., "MWF", "MW", "TTh")
  - days: List[str] (e.g., ["M", "W", "F"])
  - start_time: time
  - end_time: time
  - duration_min: int (50, 75, 110, or 150 minutes)
```

## Input Files

| File | Description |
|------|-------------|
| `inputs/Teaching Assignments True 2015-2026 - 2026-2027 Faculty.csv` | Faculty assignments and course sections |
| `inputs/Teaching Assignments True 2015-2026 - 2026-2027.csv` | Course enrollments |
| `inputs/Teaching Assignments True 2015-2026 - Priority 1 Classrooms.csv` | Available classrooms by semester |
| `inputs/Teaching Assignments True 2015-2026 - Meeting Patterns.csv` | Time slot definitions |
| `inputs/class conflicts - Sheet1.csv` | Course conflict pairs |
| `inputs/constraint_weights.csv` | Constraint priorities and weights |
| `inputs/global_constraints.csv` | System-wide scheduling rules |
| `inputs/faculty_constraints.csv` | Faculty-specific constraints |
| `inputs/course_constraints.csv` | Course-specific constraints |
| `inputs/Fall Enrollment.csv` | Historical enrollment template (Fall) |
| `inputs/Winter Enrollment.csv` | Historical enrollment template (Winter) |
| `inputs/Spring Enrollment.csv` | Historical enrollment template (Spring) |
| `inputs/Summer Enrollment.csv` | Historical enrollment template (Summer) |

## Constraints

### Hard Constraints (Must Be Satisfied)

| Constraint | Weight | Description |
|------------|--------|-------------|
| Each Section Scheduled Once | 100 | Every section assigned to exactly one room-pattern |
| Room Conflicts | 100 | Prevent room double-booking |
| Instructor Conflicts | 100 | Prevent instructor double-booking |
| Enrollment Conservation | 100 | Sum of section enrollments equals course total |
| Linearized Capacity | 95 | Enrollment must fit in room capacity |
| Fixed Enrollment | 85 | Equal-sections mode enforcement |
| Same-Course Section Conflicts | 80 | Sections of same course at different times |
| Equal Enrollment Same Instructor | 75 | Same instructor sections have similar enrollment (+/-20) |
| Instructor Building Restrictions | 70 | Prevent instructors from certain buildings |
| Same-Course Same-Day Pattern | 40 | Same day pattern for instructor's sections |
| Same-Course Same-Room | 35 | Same room for course sections |
| Large Section Preference | 25 | Large-preference instructors get higher enrollment |

### Soft Constraints (Optimized in Objective)

| Constraint | Weight | Description |
|------------|--------|-------------|
| Template Room/Time Preference | 95 | Prefer matching historical room/time |
| Template Instructor Preference | 90 | Prefer matching historical instructor patterns |
| Priority 1 Room Slot Coverage | 60 | Encourage filling Priority 1 rooms |
| Faculty Teaching Preferences | 50 | Honor faculty room/time preferences |
| Adjacent Time Slots Preference | 45 | Consecutive times for same-course sections |
| Course Conflicts | 20 | Conflict-free path between conflicting courses |

### Disabled Constraints

| Constraint | Weight | Description |
|------------|--------|-------------|
| Priority Room Restrictions | 30 | Labs to P3/P4, Regular to P1/P2 |

## Global Constraints

| Constraint | Value | Description |
|------------|-------|-------------|
| `earliest_start_time` | 09:00 | No classes before 9:00 AM |
| `latest_end_time` | 20:00 | No classes after 8:00 PM |
| `daytime_end_by` | 16:30 | Daytime classes finish by 4:30 PM |
| `evening_start_at` | 17:00 | Evening classes (E suffix) start at 5:00 PM |
| `lab_patterns_only` | MW\|TTh | Lab classes use only MW or TTh patterns |
| `pattern_match_strict` | TRUE | Strict pattern matching (MW to MW only, etc.) |

## Faculty Constraints

### Building Restrictions
- Mercer: Cannot teach in MARB

### Required Time Ranges
| Instructor | Time Range |
|------------|------------|
| Page | 09:00-12:30 |
| Angela | 09:00-12:15 |
| Goodrich | 09:00-15:00 |
| Mercer | 09:00-17:00 |
| Deccio | 09:00-12:00 |
| Clift | 14:00-16:00 |
| Seamons | 09:00-15:00 |
| Wilkerson | 09:00-15:15 |

### Time Block (Avoid)
| Instructor | Blocked Time |
|------------|--------------|
| Stephens | 08:00-09:00 |

### Time Proximity (Classes within N hours)
| Instructor | Hours |
|------------|-------|
| Crandall | 1 |
| Ng | 1 |

### Prefer Same Room
- Mercer, Crandall, Ng

## Course Constraints

| Course | Constraint | Value |
|--------|------------|-------|
| 191/291T | room_requirement | TMCB 1170 |
| 191/291T | required_time_range | 14:00-16:30 (Friday) |
| 235E | required_time_range | 17:00-18:15 |

## Meeting Patterns

### 50-Minute Patterns (MWF)
- 8:00 AM - 8:50 AM through 9:00 PM - 9:50 PM

### 75-Minute Patterns (MW/TTh)
| Pattern | Time Slots |
|---------|------------|
| MW | 8:00-9:15, 9:30-10:45, 11:00-12:15, 12:30-1:45, 2:00-3:15, 3:30-4:45, 5:00-6:15, 6:30-7:45, 8:00-9:15 |
| TTh | 8:00-9:15, 9:30-10:45, 12:30-1:45, 2:00-3:15, 3:30-4:45, 5:00-6:15, 6:30-7:45, 8:00-9:15 |

### 110-Minute Lab Patterns (MW-L/TTh-L)
- 8:00-9:50 through 3:00-4:50

### 150-Minute Patterns
| Pattern | Time |
|---------|------|
| F-LONG | 2:00 PM - 4:30 PM |
| F-LONG2 | 9:00 AM - 12:30 PM |

## Available Rooms

### Fall 2026 Rooms
| Building | Room | Capacity | Priority |
|----------|------|----------|----------|
| TMCB | 1170 | 203 | 1 |
| MARB | 130 | 72 | 1 |
| HBLL | 3718 | 59 | 1 |
| TMCB | 134 | 42 | 1 |
| TMCB | 120 | 41 | 1 |
| JKB | 3108 | 306 | 2 |
| JKB | 1102 | 285 | 2 |
| ESC | C215 | 170 | 2 |
| JFSB | B092 | 125 | 2 |
| JFSB | B037 | 120 | 2 |

### Winter/Spring 2027 Rooms
| Building | Room | Capacity | Priority |
|----------|------|----------|----------|
| TMCB | 1170 | 203 | 1 |
| MARB | 130 | 72 | 1 |
| HBLL | 3718 | 59 | 1 |
| TMCB | 134 | 42 | 1 |
| TMCB | 120 | 41 | 1 |
| JKB | 3108 | 306 | 2 |
| JKB | 1102 | 285 | 2 |
| JFSB | B092 | 125 | 2 |

## Output Files

### Per-Semester Schedule Files
- `results/schedule_{Semester}.csv` - Main schedule sorted by course
- `results/schedule_{Semester}_by_instructor.csv` - Schedule sorted by instructor
- `results/conflicts_{Semester}.csv` - Conflict report
- `results/room_schedule_{Semester}.csv` - Room utilization grid

### Analysis Files
- `results/scheduling_report.txt` - Summary report (text)
- `results/scheduling_report.md` - Summary report (markdown)
- `results/hourly_utilization_analysis.txt` - Hourly utilization data
- `results/classroom_utilization_{Semester}.csv` - Room utilization details
- `results/classroom_availability_{Semester}.csv` - Room availability

### Phased Scheduling Files
- `results/phase1_schedule_{Semester}.csv` - Phase 1 results
- `results/phase1_unscheduled_{Semester}.csv` - Phase 1 unscheduled
- `results/phase2_schedule_{Semester}.csv` - Phase 2 results
- `results/phase2_unscheduled_{Semester}.csv` - Phase 2 unscheduled
- `results/phase3_schedule_{Semester}.csv` - Phase 3 results
- `results/phase3_unscheduled_{Semester}.csv` - Phase 3 unscheduled

## Usage

### Basic Usage
```bash
# Schedule Fall 2026 (default)
python main.py

# Schedule specific semester
python main.py "Winter 2027"
python main.py "Spring 2027"
python main.py "Summer 2027"
```

### Advanced Options
```bash
# Use template-based scheduling
python main.py "Fall 2026" --use-template

# Use phased scheduling
python main.py "Fall 2026" --use-phased

# Equal section distribution
python main.py "Fall 2026" --equal-sections

# Combine options
python main.py "Fall 2026" --use-phased --equal-sections
```

## Optimization Objective

The ILP solver maximizes an objective function that rewards:

1. **Assigning all sections to rooms** (primary goal)
2. **Using Priority 1 rooms** over Priority 2/3/4 rooms
3. **Prime-time slots** (9 AM - 4 PM) in Priority 1 rooms
4. **Good capacity utilization** (not too empty, not overfilled)
5. **Same room for instructor's sections** (preference)
6. **Adjacent time slots** for same-course sections
7. **Template matching** when using historical data

## Current Schedule Statistics

Based on the most recent scheduling run:

| Semester | Sections | Students | Utilization |
|----------|----------|----------|-------------|
| Fall 2026 | 84 | 3,796 | 38.0% |
| Winter 2027 | 77 | 3,433 | 33.8% |
| Spring 2027 | 10 | 354 | 37.7% |
| Summer 2027 | 5 | 59 | 12.5% |
| **Total** | **176** | **7,642** | **35.4%** |

### Room Utilization by Priority
| Priority | Sections | Students | Avg Utilization |
|----------|----------|----------|-----------------|
| 1 | 130 | 4,492 | 35.5% |
| 2 | 46 | 3,150 | 35.2% |

### Meeting Pattern Distribution
| Pattern | Sections | Percentage |
|---------|----------|------------|
| M,W | 89 | 50.6% |
| T,Th | 57 | 32.4% |
| M,W,F | 26 | 14.8% |
| F | 4 | 2.3% |

### Top Instructor Loads
| Instructor | Sections | Students |
|------------|----------|----------|
| Bean | 9 | 565 |
| Reynolds | 9 | 529 |
| Barker | 9 | 470 |
| Dougal, Duane | 8 | 180 |
| Stephens | 7 | 645 |
| Jensen | 7 | 319 |
| Wilkerson | 7 | 255 |
| MJ | 7 | 240 |
| Gates, Darin | 6 | 187 |
| Richardson | 4 | 452 |

## Enrollment Optimization

The scheduler uses flexible enrollment distribution to maximize room utilization. When a course has multiple sections, students are distributed optimally rather than equally, creating larger sections that fill rooms efficiently and smaller sections with at least 30 students (when possible).

### Example: CS 110 (Fall 2026)
- **Total**: 490 students across 5 sections
- **Equal distribution would be**: 98 per section
- **Optimized distribution**:
  - Section 1: 200 students (98% utilization)
  - Section 2: 200 students (98% utilization)
  - Section 3: 30 students (15% utilization)
  - Section 4: 30 students (15% utilization)
  - Section 6: 30 students (15% utilization)

## Validation

The scheduler performs automatic validation after solving:

1. **Instructor Conflicts**: Ensures no instructor teaches two classes at the same time
2. **Room Conflicts**: Ensures no room is double-booked
3. **Capacity Violations**: Ensures enrollment doesn't exceed room capacity

## Course Conflicts

The system handles course conflicts defined in the conflicts matrix. Courses that should not be scheduled at the same time include:

- 180 and 270 (X in matrix)
- 256 and 340 (X in matrix)
- 470-479 series (mutual conflicts)
- 670-676 series (mutual conflicts)
- 513 and 677 (X in matrix)

## Technical Details

### Solver
- **Library**: PuLP (Python Linear Programming)
- **Default Solver**: CBC (COIN-OR Branch and Cut)
- **Time Limit**: 300 seconds (5 minutes)

### Performance
- Typical solve time: 30-120 seconds for a full semester
- Explores millions of possible assignments to find optimal solution

### Parameters
- **Minimum Section Size**: 30 students (configurable)
- **Enrollment Tolerance**: +/- 20 students for same-instructor sections
