================================================================================
COURSE SCHEDULING SYSTEM - SUMMARY REPORT
================================================================================
Generated: 2025-12-10 19:03:33

1. OVERALL SUMMARY
--------------------------------------------------------------------------------
   Total sections scheduled:  73
   Total students enrolled:   3,166
   Average room utilization:  30.8%

   Semester        Sections   Students   Utilization   Conflicts
   --------------------------------------------------------------
   Winter 2027           73       3166        30.8%           0

2. GLOBAL CONSTRAINTS
--------------------------------------------------------------------------------
   earliest_start_time: 09:00
      (No classes start before 9:00 AM)
   latest_end_time: 20:00
      (No classes end after 8:00 PM)
   daytime_end_by: 16:00
      (Daytime classes (non-E suffix) must finish by 4:00 PM)
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
          1         36        713          34.2%
          2         27       1439          25.4%
          3          7        589          24.6%
          4          3        425          52.7%

7. MEETING PATTERN DISTRIBUTION
--------------------------------------------------------------------------------
   Pattern              Sections
   ------------------------------
   T,Th                       33  (45.2%)
   M,W                        32  (43.8%)
   M,W,F                       8  (11.0%)

8. TOP INSTRUCTOR TEACHING LOADS
--------------------------------------------------------------------------------
   Instructor                      Sections   Students
   -------------------------------------------------------
   Bean                                       4        268
   Dougal, Duane                              3         90
   Stephens                                   3        380
   MJ                                         3         76
   Barker                                     3        239
   Wilkerson                                  3         90
   Jensen                                     3        160
   Reynolds                                   3        156
   Jenkins                                    3        120
   Richardson                                 2        222
   Ringger                                    2        117
   Mercer                                     2         76
   Archibald                                  2         60
   Rodham                                     2        276
   Hughes                                     2         54

9. OUTPUT FILES GENERATED
--------------------------------------------------------------------------------

   Schedule files (sorted by course):
     - results/schedule_Winter_2027.csv

   Schedule files (sorted by instructor):
     - results/schedule_Winter_2027_by_instructor.csv

   Conflict reports:
     - results/conflicts_Winter_2027.csv

   Room schedule grids:
     - results/room_schedule_Winter_2027.csv

================================================================================
END OF REPORT
================================================================================