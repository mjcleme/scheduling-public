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
- Seamons: 09:00–15:00  
- Wilkerson: 09:00–15:15  

### Time Block
- **Stephens:** 08:00–09:00

### Time Proximity
- Crandall: **1**  
- Ng: **1**

---

## 5. Room Utilization by Priority

| Priority | Sections | Students | Avg Utilization |
|----------|----------|----------|-----------------|
| 1        | 112      | 3,114    | 37.9%           |
| 2        | 60       | 3,954    | 29.9%           |
| 3        | 4        | 576      | 42.1%           |

---

## 6. Room Usage Details (All Semesters)

| Room       | Priority | Sections | Students | Capacity |
|------------|----------|----------|----------|----------|
| TMCB 134   | 1 | 22 | 419 | 42 |
| JKB 3108   | 2 | 21 | 1,846 | 306 |
| TMCB 120   | 1 | 21 | 223 | 41 |
| HBLL 3718  | 1 | 20 | 592 | 59 |
| MARB 130   | 1 | 19 | 597 | 72 |
| JKB 1102   | 2 | 15 | 954 | 285 |
| TMCB 1170  | 1 | 15 | 870 | 203 |
| JKB 3104   | 1 | 12 | 413 | 94 |
| ESC C215   | 2 | 11 | 552 | 170 |
| JFSB B092  | 2 | 9 | 403 | 125 |
| CB 377     | 3 | 4 | 576 | 342 |
| JKB 3106   | 2 | 3 | 169 | 92 |
| TMCB 136   | 1 | 3 | 0 | 40 |
| JFSB B037  | 2 | 1 | 30 | 120 |

---

## 7. Meeting Pattern Distribution

| Pattern | Sections | Percent |
|---------|----------|----------|
| T,Th | 78 | 44.3% |
| M,W | 68 | 38.6% |
| M,W,F | 26 | 14.8% |
| F | 4 | 2.3% |

---

## 8. Enrollment Optimization Examples

The scheduler optimizes enrollment distribution to maximize room usage rather than splitting evenly.

### **110 — Fall 2026**
- **Total:** 490 students across 5 sections  
- Equal distribution: 98 each  
- **Optimized:**
Section 1: 200 students [############## ] 70%
Section 2: 200 students [############# ] 65%
Section 3: 30 students [## ] 15%
Section 4: 30 students [## ] 15%
Section 6: 30 students [## ] 15%
### **111 — Fall 2026**
- **Total:** 430 students across 4 sections  
- Equal distribution: 108 each  
- **Optimized:**
Section 3: 200 students [################### ] 98%
Section 2: 110 students [########## ] 54%
Section 1: 90 students [###### ] 32%
Section 5: 30 students [## ] 15%
### **224A — Fall 2026**
- **Total:** 101 students across 2 sections  
- Equal distribution: 50 each  
- **Optimized:**
Section 1: 60 students [####### ] 35%
Section 2: 41 students [#### ] 24%

---

## 9. Top Instructor Teaching Loads

| Instructor | Sections | Students |
|------------|----------|----------|
| Bean | 9 | 571 |
| Reynolds | 9 | 529 |
| Barker | 9 | 470 |
| Dougal, Duane | 8 | 180 |
| Stephens | 7 | 685 |
| Jensen | 7 | 319 |
| Wilkerson | 7 | 255 |
| MJ | 7 | 220 |
| Gates, Darin | 6 | 187 |
| Richardson | 4 | 452 |

---

## 10. Output Files Generated

### Schedule (sorted by course)
- `results/schedule_Fall_2026.csv`
- `results/schedule_Winter_2027.csv`
- `results/schedule_Spring_2027.csv`
- `results/schedule_Summer_2027.csv`

### Schedule (sorted by instructor)
- `results/schedule_Fall_2026_by_instructor.csv`
- `results/schedule_Winter_2027_by_instructor.csv`
- `results/schedule_Spring_2027_by_instructor.csv`
- `results/schedule_Summer_2027_by_instructor.csv`

### Room schedule grids
- `results/room_schedule_Fall_2026.csv`  
- `results/room_schedule_Winter_2027.csv`  
- `results/room_schedule_Spring_2027.csv`  
- `results/room_schedule_Summer_2027.csv`  

---

**END OF REPORT**
