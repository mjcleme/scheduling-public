# Scheduling for Computer Science
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
