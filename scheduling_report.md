COURSE SCHEDULING SYSTEM - SUMMARY REPORT
Generated: 2025-12-10 14:57:01

1. OVERALL SUMMARY
--------------------------------------------------------------------------------
   Total sections scheduled:  176
   Total students enrolled:   7,644
   Average room utilization:  35.2%

   Semester        Sections   Students   Utilization
   --------------------------------------------------
   Fall 2026             84      3,798         39.4%
   Winter 2027           77      3,433         31.8%
   Spring 2027           10        354         37.7%
   Summer 2027            5         59         12.5%

2. HOW THE SCHEDULER WORKS
--------------------------------------------------------------------------------

   The scheduler uses Integer Linear Programming (ILP) to find optimal room and
   time assignments for all course sections. It maximizes an objective function
   that rewards:

   - Assigning sections to rooms (primary goal)
   - Using Priority 1 rooms over Priority 2/3/4 rooms
   - Prime-time slots (9 AM - 4 PM) in Priority 1 rooms
   - Good capacity utilization (not too empty, not overfilled)
   - Keeping same-instructor sections in the same room
   - Scheduling same-course sections at adjacent times

   The solver explores millions of possible assignments and finds the combination
   that maximizes the objective while satisfying all hard constraints.

3. CONSTRAINTS ENFORCED
--------------------------------------------------------------------------------

   HARD CONSTRAINTS (must be satisfied):
   ----------------------------------------------------------------------
   [  ACTIVE] Each Section Scheduled Once
             Every section must be assigned to exactly one room-pattern combination
   [  ACTIVE] Room Conflicts
             Prevent room double-booking at same day/time
   [  ACTIVE] Instructor Conflicts
             Prevent instructor double-booking
   [  ACTIVE] Enrollment Conservation
             Sum of section enrollments must equal course total
   [  ACTIVE] Linearized Capacity
             Enrollment must fit in assigned room capacity
   [  ACTIVE] Fixed Enrollment
             Equal-sections mode: enforce specific enrollment values per section
   [  ACTIVE] Same-Course Section Conflicts
             Ensure sections of same course are at different times
   [  ACTIVE] Equal Enrollment Same Instructor
             Sections by same instructor must have similar enrollment (±20)
   [  ACTIVE] Instructor Building Restrictions
             Prevent specific instructors from teaching in certain buildings
   [  ACTIVE] Same-Course Same-Day Pattern
             Force same day pattern for multiple sections by same instructor
   [  ACTIVE] Same-Course Same-Room
             Force same room for multiple sections of same course
   [DISABLED] Priority Room Restrictions
             Labs → P3/P4 only; Regular → P1/P2 only
   [  ACTIVE] Large Section Preference
             Force large-preference instructors to get higher enrollment

   SOFT CONSTRAINTS (preferences, optimized in objective function):
   ----------------------------------------------------------------------
   [  ACTIVE] Template Room/Time Preference (weight: 95)
             PREFER matching template room/time for courses (positive bonus)
   [  ACTIVE] Template Instructor Preference (weight: 90)
             PREFER matching template time/room for instructors (positive bonus)
   [  ACTIVE] Priority 1 Room Slot Coverage (weight: 60)
             Encourage filling Priority 1 room slots
   [  ACTIVE] Faculty Teaching Preferences (weight: 50)
             Link preference variables to actual room and pattern assignments
   [  ACTIVE] Adjacent Time Slots Preference (weight: 45)
             Encourage consecutive time slots for same-course sections
   [  ACTIVE] Course Conflicts (weight: 20)
             Ensure conflict-free path exists between conflicting courses

   PARAMETERS:
   ----------------------------------------------------------------------
   Minimum Section Size: 30
             Minimum students per section when total enrollment allows (set to 0 to disable)

4. FACULTY CONSTRAINTS
--------------------------------------------------------------------------------

   Building Restriction:
     - Mercer: MARB

   Prefer Same Room:
     - Mercer: TRUE
     - Crandall: TRUE
     - Ng: TRUE

   Required Time Range:
     - Page: 09:00-12:30
     - Angela: 09:00-12:15
     - Goodrich: 09:00-15:00
     - Mercer: 09:00-17:00
     - Deccio: 9:00-12:00
     - Clift: 14:00-16:00
     - Seamons: 9:00-15:00
     - Wilkerson: 9:00-15:15

   Time Block:
     - Stephens: 08:00-09:00

   Time Proximit:
     - Crandall: 1
     - Ng: 1

5. ROOM UTILIZATION BY PRIORITY
--------------------------------------------------------------------------------

   Priority   Sections   Students   Avg Utilization
   --------------------------------------------------
          1        112      3,114             37.9%
          2         60      3,954             29.9%
          3          4        576             42.1%

6. ROOM USAGE DETAILS (All Semesters Combined)
--------------------------------------------------------------------------------

   Room              Priority   Sections   Students   Capacity
   ------------------------------------------------------------
   TMCB 134                  1         22        419         42
   JKB 3108                  2         21      1,846        306
   TMCB 120                  1         21        223         41
   HBLL 3718                 1         20        592         59
   MARB 130                  1         19        597         72
   JKB 1102                  2         15        954        285
   TMCB 1170                 1         15        870        203
   JKB 3104                  1         12        413         94
   ESC C215                  2         11        552        170
   JFSB B092                 2          9        403        125
   CB 377                    3          4        576        342
   JKB 3106                  2          3        169         92
   TMCB 136                  1          3          0         40
   JFSB B037                 2          1         30        120

7. MEETING PATTERN DISTRIBUTION
--------------------------------------------------------------------------------

   Pattern              Sections
   ------------------------------
   T,Th                       78  (44.3%)
   M,W                        68  (38.6%)
   M,W,F                      26  (14.8%)
   F                           4  (2.3%)

8. ENROLLMENT OPTIMIZATION EXAMPLES
--------------------------------------------------------------------------------

   The scheduler uses flexible enrollment distribution to maximize room
   utilization. When a course has multiple sections, students are distributed
   optimally rather than equally, creating larger sections that fill rooms
   efficiently and smaller sections with at least 30 students (when possible).

   110 (Fall 2026):
   Total: 490 students across 5 sections
   Equal distribution would be: 98 per section
   Optimized distribution:
     Section 1: 200 students [##############      ] 70% util
     Section 2: 200 students [#############       ] 65% util
     Section 3:  30 students [##                  ] 15% util
     Section 4:  30 students [##                  ] 15% util
     Section 6:  30 students [##                  ] 15% util

   111 (Fall 2026):
   Total: 430 students across 4 sections
   Equal distribution would be: 108 per section
   Optimized distribution:
     Section 3: 200 students [################### ] 98% util
     Section 2: 110 students [##########          ] 54% util
     Section 1:  90 students [######              ] 32% util
     Section 5:  30 students [##                  ] 15% util

   224A (Fall 2026):
   Total: 101 students across 2 sections
   Equal distribution would be: 50 per section
   Optimized distribution:
     Section 1:  60 students [#######             ] 35% util
     Section 2:  41 students [####                ] 24% util

9. TOP INSTRUCTOR TEACHING LOADS
--------------------------------------------------------------------------------

   Instructor                      Sections   Students
   -------------------------------------------------------
   Bean                                    9        571
   Reynolds                                9        529
   Barker                                  9        470
   Dougal, Duane                           8        180
   Stephens                                7        685
   Jensen                                  7        319
   Wilkerson                               7        255
   MJ                                      7        220
   Gates, Darin                            6        187
   Richardson                              4        452

10. OUTPUT FILES GENERATED
--------------------------------------------------------------------------------

   Schedule files (sorted by course):
     - results/schedule_Fall_2026.csv
     - results/schedule_Winter_2027.csv
     - results/schedule_Spring_2027.csv
     - results/schedule_Summer_2027.csv

   Schedule files (sorted by instructor):
     - results/schedule_Fall_2026_by_instructor.csv
     - results/schedule_Winter_2027_by_instructor.csv
     - results/schedule_Spring_2027_by_instructor.csv
     - results/schedule_Summer_2027_by_instructor.csv

   Room schedule grids (visual room-by-time matrices):
     - results/room_schedule_Fall_2026.csv
     - results/room_schedule_Winter_2027.csv
     - results/room_schedule_Spring_2027.csv
     - results/room_schedule_Summer_2027.csv

================================================================================
END OF REPORT
================================================================================
