# Van Rakshak — Report Inventory

All 6 Power BI reports follow the Shared Dataset / Thin Report architecture.
Each report has 2 pages: **Summary View** (Page 1) and **Advanced Analytics** (Page 2).

---

## Report 1 — Patrolling Summary

**File:** `VanRakshak_Patrolling_summary_report_final.pbix`
**Data Source:** `vw_sb_patrol_all`
**Audience:** Forest Rangers, Range Officers, Division Management

### Page 1 — Summary View
| Visual | Type | Fields |
|--------|------|--------|
| SUB DIVISION slicer | Dropdown Slicer | sub_division_name |
| RANGE slicer | Dropdown Slicer | range_area_name |
| CIRCLE slicer | Dropdown Slicer | circle_area_name |
| BEAT slicer | Dropdown Slicer | sub_area_name |
| Date range | Between Slicer | calendar_date |
| Bookmark buttons | Buttons | All Ranges, Multai, Tapti, Bhainsdehi |
| Total Distance | Classic Card | SUM(total_distance) |
| Active Ranges | Classic Card | DISTINCTCOUNT(range_area_name) |
| Avg Speed | Classic Card | COUNT(patrolling_date) |
| Total Officers | Classic Card | DISTINCTCOUNT(guard_name) |
| Distance by Range | Donut Chart | total_distance by range_area_name |
| Officers by Range | Donut Chart | guard_name (Count) by range_area_name |
| Distance by Sub-Division | Bar Chart | total_distance by sub_division_name |
| Beat-Level Patrol Breakdown | Table | sub_area_name, total_distance, guard_name |
| Monthly Patrol Activity | Matrix | Week_Num × Month × total_distance |

### Page 2 — Advanced Analytics
| Visual | Type | Fields |
|--------|------|--------|
| Day Patrol Activity Heatmap | Matrix | Week_Num × Month, total_distance (gradient) |
| Monthly Distance by Range (MoM) | Clustered Bar | range_area_name, total_distance, Month legend |
| Area Coverage per Beat | Bar Chart | sub_area_name, total_distance (sorted desc) |
| Overlapping Patrols ⚠️ | Table | patrolling_date, sub_area_name, guard_name count > 1 |

---

## Report 2 — Night Patrolling Summary

**File:** `VanRakshak_NightPatrolling_report.pbix`
**Data Source:** `vw_sb_patrol_night`
**Audience:** Night Shift Team, Security Supervisors

### Page 1 — Summary View
Same layout as Patrolling Summary with night-specific fields plus:
- 5 bookmark buttons: All Ranges, Multai Range, Tapti Range, Bhainsdehi Range, Night Shift Only
- Hidden designation slicer controlled by bookmarks

### Page 2 — Advanced Analytics
Same as Patrolling Summary Page 2 but using `vw_sb_patrol_night`

---

## Report 3 — Foot Patrol Officer Report

**File:** `van_rakshak_foot_patrol_officer_report.pbix`
**Data Source:** `vw_sb_patrol_all`, `vw_sb_patrol_route`
**Audience:** Field Supervisors, Individual Officers

### Page 1 — Summary View
| Visual | Type | Fields |
|--------|------|--------|
| SUB DIVISION, RANGE, CIRCLE, BEAT, OFFICER NAME | Slicers | Various |
| Date range | Between Slicer | calendar_date |
| Distance Covered | Classic Card | SUM(total_distance) |
| Patrolling Sessions | Classic Card | DISTINCTCOUNT(patrolling_id) |
| Avg Speed | Classic Card | avg_speed |
| Azure Maps | Map Visual | Slatitude, Slongitude, Elatitude, Elongitude |
| Daily Distance Trend | Line Chart | patrolling_date × total_distance |
| Patrol Sessions Breakdown | Table | patrolling_date, sub_area_name, total_distance |

### Page 2 — Advanced Analytics
| Visual | Type | Fields |
|--------|------|--------|
| Officer Activity Heatmap | Matrix | Week_Num × Month, total_distance (gradient) |
| Monthly Distance by Range (MoM) | Clustered Bar | range_area_name, total_distance, Month |
| Beats Covered by Officer | Bar Chart | sub_area_name, total_distance (sorted desc) |
| Overlapping Patrols ⚠️ | Table | patrolling_date, sub_area_name, guard_name count > 1 |

---

## Report 4 — Night Patrol Officer Report

**File:** `VanRakshak_NightPatrol_Officer_report.pbix`
**Data Source:** `vw_sb_patrol_night`, `vw_sb_patrol_route`
**Audience:** Night Shift Supervisors, Individual Night Officers

Same layout as Foot Patrol Officer Report but using `vw_sb_patrol_night` views.

---

## Report 5 — Patrolling Data Extractor

**File:** `VanRakshak_PatrollingDataExtractor_report.pbix`
**Data Source:** `vw_sb_patrol_all`
**Audience:** Data Analysts, HR Administrators

### Page 1 — Summary View
| Visual | Type | Fields |
|--------|------|--------|
| 4 Slicers + Date | Dropdown / Between | sub_division, range, circle, beat, calendar_date |
| Designation Bookmarks | Buttons (hidden slicer) | All Officers, Forest Guard, Circle Incharge, Forester, Range Officer |
| Export to Excel | Button | Visual header export icon |
| Total Distance | Classic Card | SUM(total_distance) |
| Total Sessions | Classic Card | DISTINCTCOUNT(patrolling_id) |
| Active Officers | Classic Card | DISTINCTCOUNT(guard_name) |
| Beats Covered | Classic Card | DISTINCTCOUNT(sub_area_name) |
| Patrol Records | Table | guard_name, patrolling_date, sub_area_name, range_area_name, total_distance |
| Top Officers by Distance | Bar Chart | guard_name × total_distance (sorted desc) |
| Beat-Level Patrol Breakdown | Table | sub_area_name, total_distance, guard_name count |

### Page 2 — Advanced Analytics
Same 4 advanced visuals as other reports.

---

## Report 6 — Night Patrolling Data Extractor

**File:** `VanRakshak_NightPatrollingDataExtractor_report.pbix`
**Data Source:** `vw_sb_patrol_night`
**Audience:** Data Analysts, Night Shift Administrators

Same as Patrolling Data Extractor but using `vw_sb_patrol_night`.
Designation values differ: Forest Guard, Circle Incharge, Range Officer (no Forester in night data).

---

## Bookmark Navigation Summary

| Report | Bookmark Buttons | Controls |
|--------|-----------------|----------|
| Patrolling Summary | All Ranges, Multai, Tapti, Bhainsdehi | RANGE slicer |
| Night Patrolling Summary | All Ranges, Multai, Tapti, Bhainsdehi, Night Shift Only | RANGE slicer |
| Patrolling Data Extractor | All Officers, Forest Guard, Circle Incharge, Forester, Range Officer | Hidden designation slicer |
| Night Data Extractor | All Officers, Forest Guard, Circle Incharge, Range Officer | Hidden designation slicer |

---

## Key Metrics Across All Reports

| Metric | Day Patrol | Night Patrol |
|--------|-----------|--------------|
| Total Distance | 6,932 km | 8,631 km |
| Total Sessions | 830 | 595 |
| Active Officers | 161–229 | 148 |
| Beats Covered | 146 | 134 |
| Overlapping Patrols | 24 instances | 16 instances |
| Active Ranges | 6 | 6 |
