# Van Rakshak — DAX Measures Guide

All 4 custom DAX measures written for the VanRakshak_MasterDataset.

---

## Overview

| Measure | Purpose | Used In |
|---------|---------|---------|
| `MoM Distance % Change` | Month-over-month % change in day patrol distance | Patrolling Summary, Data Extractor |
| `MoM Night Distance % Change` | Month-over-month % change in night patrol distance | Night Patrolling, Night Extractor |
| `Overlapping Patrols` | Count of patrols where 2+ officers covered same beat same day | All Advanced Analytics pages |
| `Avg Distance Per Beat` | Average patrol distance per unique beat | Advanced Analytics |

---

## Measure 1 — MoM Distance % Change

```dax
MoM Distance % Change =
VAR CurrentMonth =
    CALCULATE(
        SUM(vw_sb_patrol_all[total_distance]),
        DATESMTD(vw_sb_dim_date[calendar_date])
    )
VAR LastMonth =
    CALCULATE(
        SUM(vw_sb_patrol_all[total_distance]),
        DATEADD(
            DATESMTD(vw_sb_dim_date[calendar_date]),
            -1,
            MONTH
        )
    )
RETURN
    DIVIDE(CurrentMonth - LastMonth, LastMonth, 0)
```

**Functions used:**
- `CALCULATE` — Modifies filter context
- `DATESMTD` — Returns dates from start of month to current date
- `DATEADD` — Shifts date range by -1 month
- `DIVIDE` — Safe division (returns 0 instead of error when denominator is 0)

**Expected behavior:**
- Returns `0.0` when full date range selected (no month context)
- Returns `0.124` (12.4%) when April is selected and March had less distance
- Format as Percentage, 1 decimal place

---

## Measure 2 — MoM Night Distance % Change

```dax
MoM Night Distance % Change =
VAR CurrentMonth =
    CALCULATE(
        SUM(vw_sb_patrol_night[total_distance]),
        DATESMTD(vw_sb_dim_date[calendar_date])
    )
VAR LastMonth =
    CALCULATE(
        SUM(vw_sb_patrol_night[total_distance]),
        DATEADD(
            DATESMTD(vw_sb_dim_date[calendar_date]),
            -1,
            MONTH
        )
    )
RETURN
    DIVIDE(CurrentMonth - LastMonth, LastMonth, 0)
```

Identical to Measure 1 but references `vw_sb_patrol_night` instead of `vw_sb_patrol_all`.

---

## Measure 3 — Overlapping Patrols

```dax
Overlapping Patrols =
CALCULATE(
    COUNTROWS(vw_sb_patrol_all),
    FILTER(
        vw_sb_patrol_all,
        CALCULATE(
            DISTINCTCOUNT(vw_sb_patrol_all[guard_name]),
            ALLEXCEPT(
                vw_sb_patrol_all,
                vw_sb_patrol_all[sub_area_name],
                vw_sb_patrol_all[patrolling_date]
            )
        ) > 1
    )
)
```

**Functions used:**
- `CALCULATE` — Outer: counts rows matching filter; Inner: counts distinct officers per beat-date
- `COUNTROWS` — Counts matching patrol records
- `FILTER` — Iterates rows and applies condition
- `DISTINCTCOUNT` — Counts unique officer names
- `ALLEXCEPT` — Removes all filters except beat and date (so we count all officers for that beat-date combination)

**Logic:**
For each patrol record, checks if more than 1 distinct officer patrolled the same beat on the same date. If yes, counts that record as an overlap.

**Results:**
- Day patrol overlaps: 24 instances
- Night patrol overlaps: 16 instances

---

## Measure 4 — Avg Distance Per Beat

```dax
Avg Distance Per Beat =
DIVIDE(
    SUM(vw_sb_patrol_all[total_distance]),
    DISTINCTCOUNT(vw_sb_patrol_all[sub_area_name]),
    0
)
```

**Functions used:**
- `SUM` — Total distance across all patrols
- `DISTINCTCOUNT` — Number of unique beats covered
- `DIVIDE` — Safe division

**Note:** This measure returns a single scalar (global average). For per-beat breakdown in charts, use `SUM(total_distance)` grouped by `sub_area_name` directly instead.

---

## How to Add Measures to Master Dataset

1. Open `VanRakshak_MasterDataset.pbix`
2. Go to `Modeling` tab in ribbon
3. Click `New measure`
4. Paste the DAX formula in the formula bar
5. Press `Enter` or click ✅
6. Repeat for each measure
7. `Ctrl + S` to save
8. `Home → Publish → Replace` to update Power BI Service

All thin reports automatically inherit new measures via live connection — no changes needed in individual report files.

---

## DAX Best Practices Used

- **VAR statements** — Improves readability and performance (calculated once, referenced twice)
- **DIVIDE instead of /** — Prevents division by zero errors
- **ALLEXCEPT for context manipulation** — Precise control over filter removal
- **DATESMTD for time intelligence** — Proper month-to-date calculation respecting date table
