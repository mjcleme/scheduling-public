# Scheduling for Computer Science
### What Faculty should look at
- [Fall Schedule by instructor](schedule_Fall_2026_by_instructor.csv)
- [Winter Schedule by instructor](schedule_Winter_2027_by_instructor.csv)
- [Spring Schedule by instructor](schedule_Spring_2027_by_instructor.csv)
- [Summer Schedule by instructor](schedule_Spring_2027_by_instructor.csv)

### Notation
The room schedule files show when rooms are used ([Fall](room_schedule_Fall_2026.csv), [Winter](room_schedule_Winter_2027.csv), [Spring](room_schedule_Spring_2027.csv), [Summer](room_schedule_Summer_2027.csv))
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

# Course Scheduling via Integer Linear Programming (ILP)

## Goal
Optimize room assignments and enrollment distribution to maximize capacity utilization while respecting all scheduling constraints.

## Implementation Steps

### 1. Data Parsing Module
- Parse course data: extract sections per course (count of instructors), individual instructors, total in-person enrollment, meeting pattern preferences
- Load rooms: building, room number, capacity, priority level, availability windows
- Parse conflict matrix: identify course pairs that conflict
- Parse meeting patterns: expand each pattern into (day, start_time, end_time) tuples
  - MWF 9:00-9:50 → [(Mon,9:00,9:50), (Wed,9:00,9:50), (Fri,9:00,9:50)]
  - MW 9:30-10:45 → [(Mon,9:30,10:45), (Wed,9:30,10:45)]

### 2. ILP Model Construction (using PuLP or OR-Tools)

#### Decision Variables
- `x[course, section, room, timeslot]` ∈ {0,1}: binary assignment indicator
- `enrollment[course, section]` ∈ ℤ⁺: students assigned to each section

#### Objective Function
Maximize Σ (enrollment[i,s] / capacity[r]) × x[i,s,r,t] across all assignments

#### Hard Constraints

1. **Each section scheduled exactly once:**
   - Σ(r,t) x[i,s,r,t] = 1 for all courses i, sections s

2. **Room capacity sufficient:**
   - enrollment[i,s] ≤ capacity[r] when x[i,s,r,t] = 1

3. **Enrollment distribution:**
   - Σ(s) enrollment[i,s] = total_in_person_enrollment[i]
   - enrollment[i,s] ≥ 1 for all sections

4. **Room conflicts (day-time granularity):**
   - For each room r, day d, time pairs (t1,t2) where time ranges overlap:
     - Σ(i,s) x[i,s,r,t1] + Σ(j,s) x[j,s,r,t2] ≤ 1
   - Overlap check: start1 < end2 AND start2 < end1
   - Example: MW 9:30-10:45 blocks MWF 9:00-9:50 and MWF 10:00-10:50 on Mon/Wed

5. **Instructor conflicts (no double-booking):**
   - For each instructor p, timeslot t (including day-time overlaps):
     - Σ(i,s,r where instructor[i,s]=p) x[i,s,r,t] ≤ 1
   - **CRITICAL VALIDATION**: No instructor teaching multiple sections simultaneously

6. **Meeting pattern matching:**
   - x[i,s,r,t] can only be 1 if timeslot t matches course i's preferred pattern

7. **Course conflicts (student path):**
   - For conflicting courses i,j: ensure at least one non-overlapping section pair exists
   - Courses in conflict CAN be scheduled at same time if alternative sections exist

8. **Online sections excluded:**
   - Sections marked -O don't get room assignments (x = 0 for all r,t)

### 3. Solver Execution
- Build and solve ILP model with timeout (e.g., 10 minutes)
- Handle infeasibility with constraint relaxation if needed

### 4. Output Generation
- CSV: Semester, Course, Section, Instructor, Enrollment, Building, Room, Days, Start Time, End Time, Capacity, Utilization%
- Summary: total utilization, unscheduled courses, constraint violations

### 5. Validation
- Verify no instructor double-bookings
- Check room capacities not exceeded
- Confirm conflict-free paths exist for students
- Validate day-time room conflicts resolved

## Tools & Libraries
- Python with PuLP or OR-Tools for ILP optimization
- pandas for data parsing and output generation

## Key Input Files
- `Teaching Assignments True 2015-2026 - 2026-2027.csv` - courses, instructors, enrollments (columns D,F,H,J)
- `Teaching Assignments True 2015-2026 - Priority 1 Classrooms.csv` - available rooms
- `Teaching Assignments True 2015-2026 - Meeting Patterns.csv` - time slots
- `class conflicts - Sheet1.csv` - course conflict matrix

## Important Notes
- Number of faculty teaching = number of sections desired
- Student counts are for in-person sections only (exclude online -O sections)
- Enrollment distribution across sections does NOT need to be uniform
- Goal is to maximize room capacity utilization
- Instructor conflicts are the primary validation constraint


## Big-M Linearization Explained

### The Problem

Big-M is a technique to convert nonlinear products of **binary × continuous** variables into linear constraints.

### What We Have

We have:
- `x[c,s,r,p]` ∈ {0,1} - binary variable (is this assignment made?)
- `enrollment[c,s]` ∈ ℤ⁺ - integer variable (how many students?)

We want: `u = enrollment × x` in our objective

But we **can't** multiply variables in ILP (Integer Linear Programming)!

### Why It's Nonlinear

In ILP, we can only have **linear** expressions:
- Variable × constant ✓ (e.g., `3x`)
- Variable + variable ✓ (e.g., `x + y`)
- Variable × variable ✗ (e.g., `x × y`) - **NONLINEAR!**

## The Big-M Solution

Create a new auxiliary variable `u[c,s,r,p]` that represents the product.

We want `u` to behave like:
- When `x = 0`: `u = 0` (no assignment → no students in that room)
- When `x = 1`: `u = enrollment[c,s]` (assignment → students go to that room)

## The Constraints

Let `M` = a large constant (e.g., max possible enrollment = 500)

Add these **linear** constraints:

```
1. u ≤ M × x
   # If x=0, then u≤0 (combined with #2, forces u=0)
   # If x=1, then u≤M (doesn't constrain u much)

2. u ≥ 0
   # u is non-negative

3. u ≤ enrollment
   # u can't exceed actual enrollment

4. u ≥ enrollment - M(1 - x)
   # If x=1: u ≥ enrollment - 0, so u ≥ enrollment
   # Combined with constraint #3: u = enrollment when x=1
   # If x=0: u ≥ enrollment - M (not constraining since u≥0)
```

## Example Walkthrough

Suppose `enrollment[CS235,section1] = 90` and `M = 500`:

### Case 1: x = 0 (not assigned to this room)

- Constraint 1: `u ≤ 500 × 0 = 0`
- Constraint 2: `u ≥ 0`
- Constraint 3: `u ≤ 90`
- Constraint 4: `u ≥ 90 - 500(1-0) = 90 - 500 = -410`

**Combined effect**: `0 ≤ u ≤ 0`

**Result**: `u = 0` ✓

### Case 2: x = 1 (assigned to this room)

- Constraint 1: `u ≤ 500 × 1 = 500`
- Constraint 2: `u ≥ 0`
- Constraint 3: `u ≤ 90`
- Constraint 4: `u ≥ 90 - 500(1-1) = 90 - 0 = 90`

**Combined effect**: `90 ≤ u ≤ 90`

**Result**: `u = 90` ✓

## Our Objective Becomes Linear

Original (nonlinear):
```python
maximize Σ (enrollment[c,s] × x[c,s,r,p] / capacity[r])
```

After Big-M linearization:
```python
maximize Σ (u[c,s,r,p] / capacity[r])
```

This is now **linear**! (dividing by constant capacity is fine)

## Choosing M

### Requirements

**M should be:**
- **Large enough**: `M ≥ max(enrollment[c,s])` for all courses
- **Small as possible**: Too large causes numerical instability

### For Our Scheduling Problem

Looking at the course data:
- Maximum single course enrollment: ~270 students (CS 235)
- Safe choice: `M = 500` or `M = 1000`

### Why Not Just Use M = 1,000,000?

**Numerical Precision Issues:**
- ILP solvers use floating-point arithmetic internally
- Very large M values can cause:
  - Rounding errors
  - Poor branching decisions in branch-and-bound
  - Slower convergence
  - Numerical instability

**Best Practice:** Use the smallest M that satisfies constraints

## Complexity Analysis

### Additional Variables

For each potential assignment, we add one `u` variable:
- Number of `u` variables = Number of `x` variables
- For our problem: ~42 sections × 18 rooms × 35 patterns ≈ 26,000 variables

### Additional Constraints

For each `u` variable, we add 4 constraints:
- Total new constraints ≈ 4 × 26,000 = 104,000 constraints

### Is This Manageable?

**Yes, for modern ILP solvers:**
- Commercial solvers (Gurobi, CPLEX) handle millions of constraints
- Open-source CBC can handle 100k+ constraints
- Will take longer than simplified version but still reasonable

**Typical solving time:**
- Simple problems: seconds
- Our problem: likely 1-10 minutes
- Complex problems: could timeout

## Trade-offs

### Pros
- ✅ **Exact formulation** - preserves true objective
- ✅ Uses existing PuLP/CBC solver (no new dependencies)
- ✅ Well-studied, proven technique
- ✅ Guaranteed to find optimal solution (if one exists)

### Cons
- ❌ Adds many variables (~26,000 for our problem)
- ❌ Adds many constraints (~104,000 for our problem)
- ❌ Slower solving time than simplified objective
- ❌ Large M values can cause numerical precision issues
- ❌ May not find solution within time limit for very large problems

## Alternative: Simplified Linear Objective

Instead of Big-M, use a simpler objective that's already linear:

```python
# Prioritize smaller rooms (inverse capacity weighting)
weight = (1.0 / room.capacity) * 1000 + 1
maximize Σ weight × x[c,s,r,p]
```

**Pros:**
- ✅ Much faster to solve
- ✅ Simpler formulation
- ✅ No numerical issues
- ✅ Guaranteed to find solution quickly

**Cons:**
- ❌ Doesn't explicitly maximize capacity utilization
- ❌ Enrollment distribution arbitrary (any feasible solution)
- ❌ May assign large sections to unnecessarily large rooms

## Comparison Summary

| Aspect | Big-M Linearization | Simplified Objective |
|--------|-------------------|---------------------|
| **Optimization Goal** | Maximize capacity utilization | Prefer smaller rooms |
| **Formulation** | Exact | Approximation |
| **Variables** | 2× (add `u` vars) | 1× (just `x` vars) |
| **Constraints** | 4× more | Normal |
| **Solve Time** | Minutes | Seconds |
| **Solution Quality** | Optimal utilization | Good assignments |
| **Complexity** | High | Low |

## Implementation in Python (PuLP)

```python
# Create auxiliary variables
u_vars = {}
for (c_idx, s_idx, r_idx, p_idx) in x_vars.keys():
    u_vars[(c_idx, s_idx, r_idx, p_idx)] = LpVariable(
        f"u_c{c_idx}_s{s_idx}_r{r_idx}_p{p_idx}",
        lowBound=0,
        cat='Continuous'
    )

# Add Big-M constraints
M = 1000  # Large constant

for (c_idx, s_idx, r_idx, p_idx) in x_vars.keys():
    x = x_vars[(c_idx, s_idx, r_idx, p_idx)]
    u = u_vars[(c_idx, s_idx, r_idx, p_idx)]
    enrollment = enrollment_vars[(c_idx, s_idx)]

    # 1. u <= M * x
    problem += u <= M * x

    # 2. u >= 0 (already in variable definition)

    # 3. u <= enrollment
    problem += u <= enrollment

    # 4. u >= enrollment - M(1 - x)
    problem += u >= enrollment - M * (1 - x)

# Objective: maximize utilization
objective = lpSum([
    u_vars[(c_idx, s_idx, r_idx, p_idx)] / rooms[r_idx].capacity
    for (c_idx, s_idx, r_idx, p_idx) in x_vars.keys()
])

problem += objective
```

## References

- **Linear Programming**: Foundations and Extensions by Vanderbei
- **Integer Programming** by Wolsey
- **Modeling with Linear Programming** - Big-M technique is covered in most operations research textbooks

## Recommendation for Our Project

**Try Big-M first** because:
1. Our problem size (~26k variables) is manageable
2. We want true capacity utilization optimization
3. Can fall back to simplified objective if too slow

**If Big-M times out or causes issues:**
- Increase time limit
- Use simpler objective
- Consider heuristic approaches
