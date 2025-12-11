================================================================================
COURSE SCHEDULING SYSTEM - SUMMARY REPORT
================================================================================
Generated: 2025-12-10 23:05:31

1. OVERALL SUMMARY
--------------------------------------------------------------------------------
   Total sections scheduled:  5
   Total students enrolled:   59
   Average room utilization:  12.5%

   Semester        Sections   Students   Utilization   Conflicts
   --------------------------------------------------------------
   Summer 2027            5         59        12.5%           0

2. GLOBAL CONSTRAINTS
--------------------------------------------------------------------------------
   earliest_start_time: 09:00
      (No classes start before 9:00 AM)
   latest_end_time: 20:00
      (No classes end after 8:00 PM)
   daytime_end_by: 16:30
      (Daytime classes (non-E suffix) must finish by 4:30 PM)
   evening_start_at: 17:00
      (Evening classes (E suffix) must start at or after 5:00 PM)
   lab_patterns_only: MW|TTh
      (Lab classes (L suffix) can only use MW or TTh patterns)
   pattern_match_strict: TRUE
      (TTh courses only use 75-min TTh pattern (not TTH-L 110-min))

3. FACULTY CONSTRAINTS
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

   Time Proximity:
     - Crandall: 1
     - Ng: 1


4. COURSE CONSTRAINTS
--------------------------------------------------------------------------------
   191/291T: room_requirement = TMCB 1170
      (Room capacity 203 can hold 170 students (disabled - TMCB 1170 not available Friday))
   191/291T: required_time_range = 14:00-16:30
      (Friday 2:00-4:30 PM (F-LONG 150 min pattern))
   235E: required_time_range = 17:00-18:15
      (Evening class must start at 5:00 PM)

5. INSTRUCTOR CONFLICTS
--------------------------------------------------------------------------------
   No instructor conflicts found!

6. ROOM UTILIZATION BY PRIORITY
--------------------------------------------------------------------------------
   Priority   Sections   Students   Avg Utilization
   --------------------------------------------------
          1          5         59          12.5%

7. MEETING PATTERN DISTRIBUTION
--------------------------------------------------------------------------------
   Pattern              Sections
   ------------------------------
   M,W                         3  (60.0%)
   M,W,F                       2  (40.0%)

8. TOP INSTRUCTOR TEACHING LOADS
--------------------------------------------------------------------------------
   Instructor                      Sections   Students
   -------------------------------------------------------
   Dougal, Duane                              2          0
   Barker                                     2         49
   Gates, Darin                               1         10

9. OUTPUT FILES GENERATED
--------------------------------------------------------------------------------

   Schedule files (sorted by course):
     - results/schedule_Summer_2027.csv

   Schedule files (sorted by instructor):
     - results/schedule_Summer_2027_by_instructor.csv

   Conflict reports:
     - results/conflicts_Summer_2027.csv

   Room schedule grids:
     - results/room_schedule_Summer_2027.csv

================================================================================
END OF REPORT
================================================================================