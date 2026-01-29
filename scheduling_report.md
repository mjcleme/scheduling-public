================================================================================
COURSE SCHEDULING SYSTEM - SUMMARY REPORT
================================================================================
Generated: 2026-01-28 17:22:00

1. OVERALL SUMMARY
--------------------------------------------------------------------------------
   Total sections scheduled:  257
   Total students enrolled:   11,197
   Average room utilization:  53.7%

   Semester        Sections   Students   Utilization
   --------------------------------------------------
   Fall 2026            125      5,602         56.8%
   Winter 2027          117      5,182         53.5%
   Spring 2027           10        354         38.5%
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
     - Mercer: 10:00-17:00
     - Deccio: 9:00-12:00
     - Clift: 14:00-16:00
     - Seamons: 9:00-15:00
     - Wilkerson: 9:00-15:15

   Time Block:
     - Stephens: 08:00-09:00

   Time Proximity:
     - Crandall: 1
     - Ng: 1

5. ROOM UTILIZATION BY PRIORITY
--------------------------------------------------------------------------------

   Priority   Sections   Students   Avg Utilization
   --------------------------------------------------
          1        128      4,435             49.3%
          2        120      6,242             61.3%
          3          8        520             19.0%
         99          1          0              0.0%

6. ROOM USAGE DETAILS (All Semesters Combined)
--------------------------------------------------------------------------------

   Room              Priority   Sections   Students   Capacity
   ------------------------------------------------------------
   TMCB 120                  1         25        409         41
   TMCB 1170                 1         21      1,477        203
   TMCB 136                  1         19        628         40
   HBLL 3718                 1         15        396         59
   JKB 3108                  2         15      1,488        306
   MCKB 26                   2         14        420         30
   TMCB 134                  1         14        341         42
   MARB 130                  1         14        531         72
   JKB 1102                  2         12        813        285
   JFSB B092                 2         10        341        125
   ESC C215                  2          9        484        170
   JKB 3106                  2          9        281         92
   CB 377                    3          9        520        342
   JKB 3104                  1          9        302         94
   ESC C285                  2          8        400         50
   ... and 17 more rooms

7. MEETING PATTERN DISTRIBUTION
--------------------------------------------------------------------------------

   Pattern              Sections
   ------------------------------
   T,Th                      130  (50.6%)
   M,W                        75  (29.2%)
   M,W,F                      28  (10.9%)
   W                           8  (3.1%)
   Th                          6  (2.3%)
   T                           6  (2.3%)
   F                           4  (1.6%)

8. ENROLLMENT OPTIMIZATION EXAMPLES
--------------------------------------------------------------------------------

   The scheduler uses flexible enrollment distribution to maximize room
   utilization. When a course has multiple sections, students are distributed
   optimally rather than equally, creating larger sections that fill rooms
   efficiently and smaller sections with at least 30 students (when possible).

   110 (Fall 2026):
   Total: 460 students across 4 sections
   Equal distribution would be: 115 per section
   Optimized distribution:
     Section 1: 200 students [################### ] 98% util
     Section 2: 200 students [##############      ] 70% util
     Section 3:  30 students [##                  ] 15% util
     Section 4:  30 students [##                  ] 15% util

   110L (Fall 2026):
   Total: 656 students across 13 sections
   Equal distribution would be: 50 per section
   Optimized distribution:
     Section 19: 186 students [#############       ] 69% util
     Section 14:  50 students [####################] 100% util
     Section 11:  40 students [####################] 100% util
     Section 12:  40 students [####################] 100% util
     Section 13:  40 students [####################] 100% util
     Section 15:  40 students [####################] 100% util
     Section 16:  40 students [################### ] 95% util
     Section 17:  40 students [####################] 100% util
     Section 18:  40 students [####################] 100% util
     Section 8:  40 students [####################] 100% util
     Section 10:  34 students [#################   ] 85% util
     Section 9:  34 students [####################] 100% util
     Section 7:  32 students [####################] 100% util

   111 (Fall 2026):
   Total: 430 students across 4 sections
   Equal distribution would be: 108 per section
   Optimized distribution:
     Section 3: 200 students [#############       ] 65% util
     Section 2: 110 students [#######             ] 36% util
     Section 1:  90 students [#####               ] 29% util
     Section 5:  30 students [##                  ] 15% util

9. TOP INSTRUCTOR TEACHING LOADS
--------------------------------------------------------------------------------

   Instructor                      Sections   Students
   -------------------------------------------------------
   TBD                                    74      3,234
   Bean                                    9        586
   Reynolds                                9        529
   Barker                                  9        470
   Dougal, Duane                           8        180
   Stephens                                7        701
   Jensen                                  7        319
   Wilkerson                               7        420
   Ringger                                 6        212
   Gates, Darin                            6        187

10. OUTPUT FILES GENERATED
--------------------------------------------------------------------------------

   Schedule files (sorted by course):
     - results2/schedule_Fall_2026.csv
     - results2/schedule_Winter_2027.csv
     - results2/schedule_Spring_2027.csv
     - results2/schedule_Summer_2027.csv

   Schedule files (sorted by instructor):
     - results2/schedule_Fall_2026_by_instructor.csv
     - results2/schedule_Winter_2027_by_instructor.csv
     - results2/schedule_Spring_2027_by_instructor.csv
     - results2/schedule_Summer_2027_by_instructor.csv

   Room schedule grids (visual room-by-time matrices):
     - results2/room_schedule_Fall_2026.csv
     - results2/room_schedule_Winter_2027.csv
     - results2/room_schedule_Spring_2027.csv
     - results2/room_schedule_Summer_2027.csv

================================================================================
END OF REPORT
================================================================================
