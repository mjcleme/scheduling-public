# COURSE SCHEDULING SYSTEM — SUMMARY REPORT
**Generated:** 2025-12-10 14:57:01

---

## 1. Overall Summary

- **Total sections scheduled:** 176  
- **Total students enrolled:** 7,644  
- **Average room utilization:** 35.2%

| Semester     | Sections | Students | Utilization |
|--------------|----------|----------|-------------|
| Fall 2026    | 84       | 3,798    | 39.4%       |
| Winter 2027  | 77       | 3,433    | 31.8%       |
| Spring 2027  | 10       | 354      | 37.7%       |
| Summer 2027  | 5        | 59       | 12.5%       |

---

## 2. How the Scheduler Works

The scheduler uses **Integer Linear Programming (ILP)** to assign optimal rooms and meeting times.  
The objective function rewards:

- Assigning sections to rooms (primary goal)
- Using Priority 1 rooms over lower-priority rooms
- Prime-time scheduling (9 AM–4 PM) in Priority 1 rooms
- Good capacity utilization
- Keeping same-instructor sections in the same room
- Scheduling same-course sections at adjacent times

The solver evaluates millions of possible assignments and chooses the one that maximizes the objective while satisfying all hard constraints.

---

## 3. Constraints Enforced

### **Hard Constraints (must be satisfied)**

- **[ACTIVE] Each Section Scheduled Once**  
  Every section must be assigned to exactly one room–pattern combination.
- **[ACTIVE] Room Conflicts**  
  Prevent double-booking.
- **[ACTIVE] Instructor Conflicts**
- **[ACTIVE] Enrollment Conservation**
- **[ACTIVE] Linearized Capacity**
- **[ACTIVE] Fixed Enrollment**
- **[ACTIVE] Same-Course Section Conflicts**
- **[ACTIVE] Equal Enrollment Same Instructor**
- **[ACTIVE] Instructor Building Restrictions**
- **[ACTIVE] Same-Course Same-Day Pattern**
- **[ACTIVE] Same-Course Same-Room**
- **[DISABLED] Priority Room Restrictions**
- **[ACTIVE] Large Section Preference**

### **Soft Constraints (rewarded in objective function)**

| Soft Constraint | Weight |
|-----------------|--------|
| Template Room/Time Preference | 95 |
| Template Instructor Preference | 90 |
| Priority 1 Room Slot Coverage | 60 |
| Faculty Teaching Preferences | 50 |
| Adjacent Time Slots | 45 |
| Course Conflicts | 20 |

### **Parameters**

- **Minimum Section Size:** 30  
  (min students per section when enrollment allows; set to 0 to disable)

---

## 4. Faculty Constraints

### Building Restriction
- **Mercer:** MARB

### Prefer Same Room
- Mercer: TRUE  
- Crandall: TRUE  
- Ng: TRUE

### Required Time Range
- Page: 09:00–12:30  
- Angela: 09:00–12:15  
- Goodrich: 09:00–15:00  
- Mercer: 09:00–17:00  
- Deccio: 09:00–12:00  
- Clift: 14:00
