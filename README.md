# Scheduling for Computer Science
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
