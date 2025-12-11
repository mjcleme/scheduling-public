================================================================================
COURSE SCHEDULING SYSTEM - SUMMARY REPORT
================================================================================
Generated: 2025-12-10 18:14:58

1. OVERALL SUMMARY
--------------------------------------------------------------------------------
   Total sections scheduled:  213
   Total students enrolled:   11,069
   Average room utilization:  48.5%

   Semester        Sections   Students   Utilization   Conflicts
   --------------------------------------------------------------
   Fall 2026            103       5589        48.4%           2
   Winter 2027           95       5009        48.5%           6
   Spring 2027           10        365        49.9%           0
   Summer 2027            5        106        49.6%           0

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
   Total conflicts: 8

   Fall 2026:
     - Dougal, Duane: 110-4 conflicts with 324-2
     - Wilkerson: 340-2 conflicts with 340-3

   Winter 2027:
     - Bean: 301R-1 conflicts with 401R-1
     - Dougal, Duane: 110-2 conflicts with 110-2
     - Dougal, Duane: 110-2 conflicts with 324-3
     - Dougal, Duane: 110-2 conflicts with 324-3
     - Wilkerson: 240-3 conflicts with 340-2
     - Hughes: 256-1 conflicts with 556-1


6. ROOM UTILIZATION BY PRIORITY
--------------------------------------------------------------------------------
   Priority   Sections   Students   Avg Utilization
   --------------------------------------------------
          1         89       2844          40.0%
          2         47       3622          36.4%
          3         21       1900          38.8%
          4         17       1134          33.0%
        100         39       1569          94.6%

7. MEETING PATTERN DISTRIBUTION
--------------------------------------------------------------------------------
   Pattern              Sections
   ------------------------------
   T,Th                       98  (46.0%)
   M,W                        77  (36.2%)
   M,W,F                      28  (13.1%)
   M,T,W,Th                    5  (2.3%)
   M                           2  (0.9%)
   Th                          1  (0.5%)
   F                           1  (0.5%)
   T,W,Th,F                    1  (0.5%)

8. TOP INSTRUCTOR TEACHING LOADS
--------------------------------------------------------------------------------
   Instructor                      Sections   Students
   -------------------------------------------------------
   Dougal, Duane                             15        660
   Bean                                      13       1049
   Reynolds                                  13        942
   Stephens                                  11       1415
   Jensen                                    10        470
   Wilkerson                                 10        749
   Barker                                     9        470
   MJ                                         9        264
   Richardson                                 8        967
   Rodham                                     7        312
   Gates, Darin                               6        187
   Giles, Steven                              5        135
   Page                                       4        521
   Jones, Angela                              4        218
   Goodrich                                   4        229

9. OUTPUT FILES GENERATED
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

   Conflict reports:
     - results/conflicts_Fall_2026.csv
     - results/conflicts_Winter_2027.csv
     - results/conflicts_Spring_2027.csv
     - results/conflicts_Summer_2027.csv

   Room schedule grids:
     - results/room_schedule_Fall_2026.csv
     - results/room_schedule_Winter_2027.csv
     - results/room_schedule_Spring_2027.csv
     - results/room_schedule_Summer_2027.csv

================================================================================
END OF REPORT
================================================================================