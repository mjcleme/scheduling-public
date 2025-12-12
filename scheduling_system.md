# Course Scheduling System

*Generated: 2025-12-11 21:14:35*

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

## Constraints

### Hard Constraints (Must Be Satisfied)

| Constraint | Weight | Description |
|------------|--------|-------------|
| Each Section Scheduled Once | 100 | Every section must be assigned to exactly one room-pattern combination |
| Room Conflicts | 100 | Prevent room double-booking at same day/time |
| Instructor Conflicts | 100 | Prevent instructor double-booking |
| Enrollment Conservation | 100 | Sum of section enrollments must equal course total |
| Linearized Capacity | 95 | Enrollment must fit in assigned room capacity |
| Fixed Enrollment | 85 | Equal-sections mode: enforce specific enrollment values per section |
| Same-Course Section Conflicts | 80 | Ensure sections of same course are at different times |
| Equal Enrollment Same Instructor | 75 | Sections by same instructor must have similar enrollment (±20) |
| Instructor Building Restrictions | 70 | Prevent specific instructors from teaching in certain buildings |
| Same-Course Same-Day Pattern | 40 | Force same day pattern for multiple sections by same instructor |
| Same-Course Same-Room | 35 | Force same room for multiple sections of same course |
| Large Section Preference | 25 | Force large-preference instructors to get higher enrollment |

### Soft Constraints (Optimized in Objective)

| Constraint | Weight | Description |
|------------|--------|-------------|
| Template Room/Time Preference | 95 | PREFER matching template room/time for courses (positive bonus) |
| Template Instructor Preference | 90 | PREFER matching template time/room for instructors (positive bonus) |
| Priority 1 Room Slot Coverage | 60 | Encourage filling Priority 1 room slots |
| Faculty Teaching Preferences | 50 | Link preference variables to actual room and pattern assignments |
| Adjacent Time Slots Preference | 45 | Encourage consecutive time slots for same-course sections |
| Course Conflicts | 20 | Ensure conflict-free path exists between conflicting courses |

### Disabled Constraints

| Constraint | Weight | Description |
|------------|--------|-------------|
| Priority Room Restrictions | 30 | Labs → P3/P4 only; Regular → P1/P2 only |

## Global Constraints

| Constraint | Value | Description |
|------------|-------|-------------|
| `earliest_start_time` | 09:00 | No classes start before 9:00 AM |
| `latest_end_time` | 20:00 | No classes end after 8:00 PM |
| `daytime_end_by` | 16:30 | Daytime classes (non-E suffix) must finish by 4:30 PM |
| `evening_start_at` | 17:00 | Evening classes (E suffix) must start at or after 5:00 PM |
| `lab_patterns_only` | MW|TTh | Lab classes (L suffix) can only use MW or TTh patterns |
| `pattern_match_strict` | TRUE | TTh courses only use 75-min TTh pattern (not TTH-L 110-min) |

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
| Deccio | 9:00-12:00 |
| Clift | 14:00-16:00 |
| Seamons | 9:00-15:00 |
| Wilkerson | 9:00-15:15 |

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
| 191/291T | required_time_range | 14:00-16:30 |
| 235E | required_time_range | 17:00-18:15 |

## Meeting Patterns

### 50-Minute Patterns (MWF)

- 8:00 AM - 8:50 AM, 9:00 AM - 9:50 AM, 10:00 AM - 10:50 AM, 11:00 AM - 11:50 AM, 12:00 PM - 12:50 PM...

### 75-Minute Patterns (MW/TTh)

| Pattern | Time Slots |
|---------|------------|
| MW | 8:00 AM-9:15AM, 9:30 AM-10:45AM, 11:00 AM-12:15PM, 12:30 PM-1:45PM, 2:00 PM-3:15PM, 3:30 PM-4:45PM, 5:00 PM-6:15PM, 6:30 PM-7:45PM, 8:00 PM-9:15PM |
| TTh | 8:00 AM-9:15AM, 9:30 AM-10:45AM, 12:30 PM-1:45PM, 2:00 PM-3:15PM, 3:30 PM-4:45PM, 5:00 PM-6:15PM, 6:30 PM-7:45PM, 8:00 PM-9:15PM |

### 110-Minute Lab Patterns (MW-L/TTh-L)

- 8:00 AM - 4:50 PM

### 150-Minute Patterns

| Pattern | Time |
|---------|------|
| F-LONG | 2:00 PM - 4:30 PM |
| F-LONG2 | 9:00 AM - 12:30 PM |

## Available Rooms

### Fall 2026 Rooms

| Building | Room | Capacity | Priority |
|----------|------|----------|----------|
| CB | 377 | 342 | 1 |
| JKB | 3108 | 306 | 2 |
| JKB | 1102 | 285 | 2 |
| MARB | 222 | 269 | 2 |
| TMCB | 1170 | 203 | 1 |
| TMCB | 1170 | 203 | 2 |
| ESC | C215 | 170 | 2 |
| JFSB | B092 | 125 | 2 |
| JFSB | B037 | 120 | 2 |
| JKB | 3106 | 92 | 2 |
| JKB | 2113 | 85 | 1 |
| TMCB | 111 | 77 | 2 |
| MARB | 130 | 72 | 1 |
| TMCB | 135 | 69 | 2 |
| HBLL | 3718 | 59 | 1 |
| ESC | C285 | 50 | 2 |
| BRMB | 240 | 48 | 2 |
| ESC | C247 | 48 | 2 |
| TMCB | 134 | 42 | 1 |
| TMCB | 104 | 42 | 2 |
| TMCB | 108 | 42 | 2 |
| TMCB | 112 | 42 | 2 |
| TMCB | 116 | 42 | 2 |
| TMCB | 121 | 42 | 2 |
| TMCB | 134 | 42 | 2 |
| TMCB | 120 | 41 | 1 |
| TMCB | 120 | 41 | 2 |
| SFH | 37 | 34 | 2 |
| MCKB | 220 | 32 | 2 |
| MCKB | 280 | 32 | 2 |
| MCKB | 331 | 32 | 2 |
| MCKB | 26 | 30 | 2 |

### Winter 2027 Rooms

| Building | Room | Capacity | Priority |
|----------|------|----------|----------|
| CB | 377 | 342 | 1 |
| JKB | 3108 | 306 | 2 |
| JKB | 1102 | 285 | 2 |
| MARB | 222 | 269 | 2 |
| TMCB | 1170 | 203 | 1 |
| TMCB | 1170 | 203 | 2 |
| JFSB | B092 | 125 | 2 |
| JKB | 3106 | 92 | 2 |
| JKB | 2113 | 85 | 1 |
| TMCB | 111 | 77 | 2 |
| MARB | 130 | 72 | 1 |
| TMCB | 135 | 69 | 2 |
| HBLL | 3718 | 59 | 1 |
| ESC | C285 | 50 | 2 |
| BRMB | 240 | 48 | 2 |
| ESC | C247 | 48 | 2 |
| TMCB | 134 | 42 | 1 |
| TMCB | 104 | 42 | 2 |
| TMCB | 108 | 42 | 2 |
| TMCB | 112 | 42 | 2 |
| TMCB | 116 | 42 | 2 |
| TMCB | 121 | 42 | 2 |
| TMCB | 134 | 42 | 2 |
| TMCB | 120 | 41 | 1 |
| TMCB | 120 | 41 | 2 |
| TMCB | 136 | 40 | 2 |
| SFH | 37 | 34 | 2 |
| MCKB | 220 | 32 | 2 |
| MCKB | 280 | 32 | 2 |
| MCKB | 26 | 30 | 2 |
| TMCB | 197 | 30 | 2 |
| MCKB | 331 | 28 | 2 |

### Spring 2027 Rooms

| Building | Room | Capacity | Priority |
|----------|------|----------|----------|
| JKB | 1102 | 285 | 2 |
| JFSB | B037 | 120 | 1 |
| JKB | 3104 | 94 | 1 |
| JFSB | B106 | 87 | 1 |
| JKB | 2111 | 85 | 1 |
| HBLL | 3718 | 59 | 1 |
| TMCB | 134 | 42 | 1 |
| TMCB | 120 | 41 | 1 |
| TMCB | 136 | 40 | 1 |

### Summer 2027 Rooms

| Building | Room | Capacity | Priority |
|----------|------|----------|----------|
| JKB | 1102 | 285 | 2 |
| JFSB | B037 | 120 | 1 |
| JKB | 3104 | 94 | 1 |
| JFSB | B106 | 87 | 1 |
| JKB | 2111 | 85 | 1 |
| HBLL | 3718 | 59 | 1 |
| TMCB | 134 | 42 | 1 |
| TMCB | 120 | 41 | 1 |
| TMCB | 136 | 40 | 1 |

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
```

## Current Schedule Statistics

| Semester | Sections | Students |
|----------|----------|----------|
| Fall 2026 | 84 | 3,753 |
| Winter 2027 | 77 | 3,403 |
| Spring 2027 | 10 | 354 |
| Summer 2027 | 5 | 59 |
| **Total** | **176** | **7,569** |

### Meeting Pattern Distribution

| Pattern | Sections | Percentage |
|---------|----------|------------|
| M,W | 74 | 42.0% |
| T,Th | 72 | 40.9% |
| M,W,F | 25 | 14.2% |
| F | 5 | 2.8% |

### Top Instructor Loads

| Instructor | Sections | Students |
|------------|----------|----------|
| Bean | 9 | 571 |
| Reynolds | 9 | 529 |
| Barker | 9 | 470 |
| Dougal, Duane | 8 | 180 |
| Stephens | 7 | 701 |
| Jensen | 7 | 319 |
| Wilkerson | 7 | 420 |
| MJ | 7 | 204 |
| Gates, Darin | 6 | 187 |
| Richardson | 4 | 452 |

## Output Files

### Per-Semester Schedule Files
- `results/schedule_{Semester}.csv` - Main schedule sorted by course
- `results/schedule_{Semester}_by_instructor.csv` - Schedule sorted by instructor
- `results/conflicts_{Semester}.csv` - Conflict report
- `results/room_schedule_{Semester}.csv` - Room utilization grid

### Analysis Files
- `results/scheduling_report.txt` - Summary report (text)
- `results/scheduling_report.md` - Summary report (markdown)
- `results/scheduling_system.md` - System documentation (this file)

## Technical Details

### Solver
- **Library**: PuLP (Python Linear Programming)
- **Default Solver**: CBC (COIN-OR Branch and Cut)
- **Time Limit**: 300 seconds (5 minutes)

### Parameters
- **Minimum Section Size**: 30 students (configurable)
- **Enrollment Tolerance**: +/- 20 students for same-instructor sections
