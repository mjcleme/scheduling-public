# Course Scheduling System Documentation

## Overview

This scheduling system uses a **hybrid multi-phase architecture** combining template-based scheduling with Integer Linear Programming (ILP) optimization to assign courses to rooms and time slots while respecting various constraints.

---

## Scheduling Modes

### 1. ILP-Only Mode (Default)
Pure optimization using PULP solver (supports Gurobi/CBC backends).

### 2. Template-Based Mode
Uses historical enrollment data to seed assignments, then ILP for remaining courses.

### 3. Phased Mode (Most Sophisticated)
Three sequential phases with different room priority levels:

| Phase | Description | Rooms Used |
|-------|-------------|------------|
| Phase 1 | Template-based scheduling from historical data | All rooms |
| Phase 2 | ILP optimization for unscheduled courses | Priority 1-2 only |
| Phase 3 | ILP optimization for remaining courses | Priority 3-4 only |

---

## Constraints

### Hard Constraints (Must Be Satisfied)

#### 1. Room Conflicts
- No double-booking of rooms at same day/time
- Room available days must include pattern days
- Pattern times must fit within room's available hours

#### 2. Instructor Conflicts
- No instructor can teach two courses at overlapping times
- Prevents faculty double-booking

#### 3. Capacity Constraints
- Section enrollment cannot exceed room capacity
- No section can exceed 200 students
- Uses Big-M linearization for optimization

#### 4. Meeting Pattern Matching
- Course pattern must match available meeting patterns
- Lab courses (suffix 'L') restricted to MW or TTh patterns
- Evening classes (suffix 'E') must start at 5:00 PM or later
- Regular daytime classes must end by 4:30 PM

#### 5. Time Window Constraints
- Earliest start: 9:00 AM (global)
- Latest end: 8:00 PM (global)
- Evening classes: 5:00 PM - 8:00 PM
- Daytime classes: 9:00 AM - 4:30 PM

#### 6. Same-Course Section Conflicts
- Different sections of the same course cannot meet at the same time
- Ensures students can choose between sections

### Soft Constraints (Preferences with Penalties)

| Constraint | Weight | Description |
|------------|--------|-------------|
| Priority Room Usage | 60 | Fill Priority 1 rooms before Priority 2/3 |
| Capacity Matching | varies | Match class size to appropriate room size |
| Same Room Preference | 50 | Same instructor's courses in same room |
| Same Day Pattern | 25 | Instructor's courses on same days |
| Time Proximity | 30 | Instructor's courses within N hours |
| Adjacent Slots | 15 | Same-course sections in adjacent times |

---

## Room Priority System

| Priority | Description | Usage |
|----------|-------------|-------|
| 1 | Department primary rooms | Scheduled first, highest preference |
| 2 | Secondary/shared rooms | Used when Priority 1 full |
| 3-4 | Labs and overflow | Used for remaining courses |
| 99 | TBD (To Be Determined) | Placeholder for unassigned rooms |
| 100 | Virtual/Not Available | Excluded from scheduling |

---

## Meeting Patterns

Standard meeting patterns available:

### 75-Minute Patterns (MW, TTh)
| Pattern | Days | Time Examples |
|---------|------|---------------|
| MW | Monday, Wednesday | 9:30-10:45, 11:00-12:15, 12:30-1:45, 2:00-3:15, 3:30-4:45 |
| TTh | Tuesday, Thursday | 9:30-10:45, 11:00-12:15, 12:30-1:45, 2:00-3:15, 3:30-4:45 |

### 50-Minute Patterns (MWF)
| Pattern | Days | Time Examples |
|---------|------|---------------|
| MWF | Mon, Wed, Fri | 9:00-9:50, 10:00-10:50, 11:00-11:50, 12:00-12:50, 1:00-1:50, 2:00-2:50, 3:00-3:50 |

### Extended Patterns
| Pattern | Days | Duration | Use Case |
|---------|------|----------|----------|
| MW-L | Mon, Wed | 110 min | Labs |
| TTh-L | Tue, Thu | 110 min | Labs |
| F-LONG | Friday | 150 min | Seminars |

### Evening Patterns
| Pattern | Days | Time |
|---------|------|------|
| MW-E | Mon, Wed | 5:00-6:15 PM, 6:30-7:45 PM |
| TTh-E | Tue, Thu | 5:00-6:15 PM, 6:30-7:45 PM |

---

## ILP Optimization Model

### Decision Variables

```
x[course, section, room, pattern]  : Binary - assignment decision
enrollment[course, section]        : Integer (0-200) - students in section
slot_used[room, pattern]           : Binary - room-time slot occupied
same_room[instructor, pair]        : Binary - preference indicator
same_pattern[instructor, pair]     : Binary - preference indicator
```

### Objective Function

Maximize the weighted sum of:

```
= capacity_efficiency    (prefer appropriately-sized rooms)
+ priority_bonus         (P1: 10,000; P2: 5,000; P3: 2,000)
+ prime_time_bonus       (P1 during 9 AM-4 PM: +15,000)
+ capacity_match_bonus   (large classes → large rooms: +100,000)
+ faculty_preferences    (same room, pattern, proximity bonuses)
```

### Time Limits
- Phase 2 ILP: 3600 seconds (1 hour)
- Phase 3 ILP: 3600 seconds (1 hour)
- Regular ILP: 300 seconds (5 minutes)

---

## Individual Faculty Constraints

The system supports individual faculty constraints:

| Constraint Type | Description | Example |
|-----------------|-------------|---------|
| `required_time_range` | Must teach within time window | 9:00 AM - 12:30 PM |
| `building_restriction` | Cannot use specific building | Not MARB |
| `prefer_same_room` | All courses in same room | Ng |
| `prefer_same_pattern` | All courses same days | Crandall |
| `time_proximity` | Courses within N hours | 1 hour gap max |

---

## Conflict Detection

### Course Time Conflicts
The system generates conflict reports showing all pairs of courses offered at overlapping times. This helps identify:
- Courses students cannot take together
- Scheduling bottlenecks in popular time slots
- Potential issues for degree planning

### Conflict Report Format
```csv
Semester, Course 1, Section 1, Days 1, Time 1, Room 1, Course 2, Section 2, Days 2, Time 2, Room 2
```

---

## Input Files

| File | Purpose |
|------|---------|
| `Teaching Assignments*.csv` | Faculty, courses, enrollments by semester |
| `Priority 1 Classrooms.csv` | Room inventory with capacity and priority |
| `Meeting Patterns.csv` | Available time slots and patterns |
| `Enrollment Templates*.csv` | Historical data for template scheduling |
| `Faculty Constraints.csv` | Individual time/building restrictions |
| `Course Constraints.csv` | Per-course requirements |

---

## Output Files

| File | Description |
|------|-------------|
| `schedule_[Semester].csv` | Final course schedule |
| `conflicts_[Semester].csv` | Course time conflicts |
| `room_schedule_[Semester].csv` | Room-centric view |
| `faculty_schedule_pivot.csv` | Faculty-centric view |
| `faculty_teaching_summary.csv` | Teaching load summary |

---

## Algorithms Summary

### Template Scheduling Algorithm
1. Load historical enrollment template data
2. Match current courses to templates by course number
3. Detect room/instructor conflicts
4. Filter invalid matches (pattern violations, capacity issues)
5. Output scheduled sections + unscheduled list

### ILP Scheduling Algorithm
1. Build constraint matrix from inputs
2. Define decision variables and bounds
3. Add hard constraints (room conflicts, instructor conflicts, capacity)
4. Add soft constraint bonuses to objective
5. Solve using PULP/Gurobi optimizer
6. Extract assignments from solution

### Phased Scheduling Algorithm
1. **Phase 1**: Run template scheduler → scheduled + unscheduled
2. **Phase 2**: Run ILP on unscheduled with Priority 1-2 rooms only
3. **Phase 3**: Run ILP on remaining with Priority 3-4 rooms only
4. **Combine**: Merge all phases into final schedule

---

## Special Handling

### Online Courses
- Suffix `-O` indicates online course
- Completely excluded from room scheduling
- Do not consume classroom resources

### Lab Courses
- Suffix `L` indicates lab section
- Restricted to lab-appropriate patterns (MW-L, TTh-L)
- Often assigned to Priority 3-4 rooms

### Evening Courses
- Suffix `E` indicates evening section
- Must use patterns starting at 5:00 PM or later
- Cannot be scheduled during daytime hours

### TBD Room Assignment
- Building: TBD, Room: TBD
- Priority 99
- Used when room is to be determined later
- Placeholder that doesn't block actual rooms

---

## Usage

### Move a Class
```bash
python3 move_class.py "Fall 2026" 235 1 MW 9:30am "[TMCB, JKB]" 50
python3 move_class.py "Fall 2026" 401R 1 MW 9:30am "[TBD]" 60  # Room TBD
```

### Regenerate Reports
```bash
python3 -c "from move_class import regenerate_reports; regenerate_reports('Fall 2026')"
```

### Generate Conflict Report
```bash
python3 create_conflict_report.py
```

---

## Performance Considerations

- ILP solver time increases exponentially with problem size
- Template scheduling provides warm start for better solutions
- Phased approach reduces problem complexity at each stage
- Priority stratification ensures important rooms filled first
