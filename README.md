# Scheduling for Computer Science
### What Faculty should look at
- [Fall Schedule by instructor](schedule_Fall_2026_by_instructor.csv)
- [Winter Schedule by instructor](schedule_Winter_2027_by_instructor.csv)
- [Spring Schedule by instructor](schedule_Spring_2027_by_instructor.csv)
- [Summer Schedule by instructor](schedule_Spring_2027_by_instructor.csv)

### Notation
- An X in the schedule file indicates that this time has a conflict with another time that is currently scheduled (For example, if there is a MW class scheduled in a room, then MWF would have an X)
- A 0 in the schedule file indicates that the room does not have priority 1 scheduling for the Computer Science Department.
### Algorithm
Integer Linear Programming (ILP) with:
- ~5,000-6,500 binary variables
- ~2,500-3,900 constraints
- Branch-and-cut optimization
- Optimal solution guaranteed

### Constraints
1. **Assignment**: Each section scheduled exactly once
2. **Capacity**: Sections only assigned to rooms large enough
3. **Room conflicts**: Day-time granularity with overlap detection
4. **Instructor conflicts**: No double-booking across all patterns
5. **Pattern matching**: Only compatible timeslots considered

### Key Design Decision
Uses simplified capacity constraints with equal-distribution enrollment assumption, avoiding Big-M linearization complexity while maintaining optimality.

### Implementation
The phased CSV files (schedule_phased_*.csv) represent an alternative scheduling approach
  that uses a two-phase strategy based on room priorities:

  Key Differences:

  Phased Scheduling (schedule_phased_*.csv):
  - Uses Priority 1 and Priority 2 rooms from the "Priority 1 Classrooms" input file
  - Includes a "Priority" column indicating which tier the room belongs to
  - Two-Phase Approach:
    - Phase 1: Schedules courses in Priority 1 rooms only (smaller, preferred rooms)
    - Phase 2: Schedules remaining courses in Priority 2 rooms only
  - Generally results in better room utilization - e.g., CS 110-1 in JFSB B037 (120 capacity)
  at 83.7% utilization
  - Focuses on filling smaller, preferred classrooms efficiently

  Regular Scheduling (schedule_*.csv):
  - Uses all available rooms from the comprehensive classrooms file
  - No priority column
  - Single-Phase Approach: All rooms considered simultaneously
  - May result in lower utilization - e.g., CS 110-1 in JSB 140 (858 capacity) at only 11.7%
  utilization
  - More flexible but can waste larger rooms on small classes

  Room Priority Examples:

  - Priority 1: TMCB 120, TMCB 134, TMCB 136, HBLL 3718, JKB 2111 (preferred)
  - Priority 2: JKB 1102, larger auditoriums (fallback options)

