# Van Rakshak — Power BI Setup Guide

Step-by-step guide to set up the Van Rakshak Power BI analytics suite from scratch.

---

## Prerequisites

- Power BI Desktop (latest version) — [Download](https://powerbi.microsoft.com/desktop)
- Power BI Pro or Premium Per User license
- Access to Azure Synapse Analytics workspace (`its-patrolling-ondemand.sql.azuresynapse.net`)
- Azure AD organizational account (`@intratechnosolutions.com`)
- Access to `VanRakshak-SharedDataset` workspace in Power BI Service

---

## Step 1 — Set Up the Master Dataset

### 1.1 Open the Master Dataset File
```
Open: VanRakshak_MasterDataset.pbix
```

### 1.2 Publish to Power BI Service
1. Click `Home → Publish`
2. Select workspace: `VanRakshak-SharedDataset`
3. Click `Select`
4. Wait for publish confirmation

### 1.3 Configure Gateway Connection
1. Go to `app.powerbi.com`
2. Navigate to `VanRakshak-SharedDataset` workspace
3. Find `VanRakshak_MasterDataset` → click `⋯ → Settings`
4. Under `Gateway and cloud connections`:
   - Server: `its-patrolling-ondemand.sql.azuresynapse.net`
   - Database: `lake_db`
   - Authentication: `OAuth2`
5. Click `Edit credentials → Sign in` with your Azure AD account
6. Click `Apply`

### 1.4 Configure Scheduled Refresh
1. In dataset Settings → `Scheduled refresh`
2. Toggle `On`
3. Frequency: `Daily`
4. Time: `4:00 AM` (after Synapse pipeline completes)
5. Click `Apply`

---

## Step 2 — Connect Thin Reports to Master Dataset

For each of the 6 report files:

### 2.1 Open Power BI Desktop → New File
```
File → New
```

### 2.2 Connect to Master Dataset
1. Click `Home → Get Data`
2. Search for `Power BI`
3. Select `Power BI semantic models`
4. Click `Connect`
5. Select `VanRakshak_MasterDataset` from `VanRakshak-SharedDataset`
6. Click `Connect`

**Verify:** Bottom status bar shows:
```
Connected live to the Power BI semantic model: VanRakshak_MasterDataset in VanRakshak-SharedDataset
```

### 2.3 Apply Theme
1. `View → Themes → dropdown arrow`
2. `Browse for themes`
3. Select: `themes/VanRakshak_Theme_Light.json`
4. Click `Open`

---

## Step 3 — Apply Botanical Background

1. Click blank canvas (not on any visual)
2. Right panel → `Format page (paint roller)`
3. `Wallpaper → Add image`
4. Select your botanical background PNG
5. Image fit: `Fill`
6. Transparency: `0%`

---

## Step 4 — Configure Bookmarks (Data Extractor Reports Only)

### 4.1 Add Hidden Designation Slicer
1. Add a Slicer visual
2. Field: `designation` from `vw_sb_patrol_all` (or `vw_sb_patrol_night`)
3. Style: `Dropdown`

### 4.2 Create Bookmarks
1. `View → Bookmarks` (open panel)
2. `View → Selection` (open panel)
3. For each bookmark:
   - Set slicer to desired value (e.g., "Forest Guard")
   - Bookmarks panel → `Add`
   - Rename bookmark
   - Right-click → `Update`

### 4.3 Hide the Slicer
1. In Selection panel
2. Click the 👁 eye icon next to the designation slicer
3. Slicer disappears from canvas but bookmarks still control it

### 4.4 Assign Bookmarks to Buttons
1. Click each navigation button
2. Format panel → `Action → On`
3. Type: `Bookmark`
4. Bookmark: select corresponding bookmark

---

## Step 5 — Publish Thin Reports

For each report file:
1. `Ctrl + S` to save
2. `Home → Publish`
3. Select target workspace (see table below)
4. Click `Select`

| Report | Target Workspace |
|--------|-----------------|
| Patrolling Summary | VR-Workspace-Patrolling |
| Night Patrolling Summary | VR-Workspace-NightPatrol |
| Foot Patrol Officer | VR-Workspace-Officers |
| Night Patrol Officer | VR-Workspace-Officers |
| Patrolling Data Extractor | VR-Workspace-DataExtract |
| Night Data Extractor | VR-Workspace-DataExtract |

---

## Step 6 — Configure RBAC

### 6.1 Grant Dataset Build Permission
1. Go to `VanRakshak-SharedDataset` workspace
2. Find `VanRakshak_MasterDataset` → `⋯ → Manage permissions`
3. Add each security group with `Build` permission only

### 6.2 Assign Workspace Roles
For each report workspace:
1. Go to workspace → `Manage access`
2. Add relevant security group → `Viewer` role

### 6.3 Package as Power BI App (Optional)
1. In each workspace → `Create app`
2. Set audience to specific security group
3. Users access via `app.powerbi.com`

---

## Troubleshooting

### "Can't determine relationships between fields"
- Ensure all fields in a visual come from the same table
- Check that `vw_sb_dim_date` has a relationship to fact tables in the master dataset

### "Failed to test connection to data source"
- Go to dataset Settings → Edit credentials
- Re-authenticate with OAuth2
- Click Apply

### Bookmarks not filtering correctly
- Right-click each bookmark → `Update` to re-capture current filter state
- Ensure hidden slicer is NOT hidden in Selection panel when capturing bookmarks
- Hide slicer AFTER all bookmarks are captured

### Heatmap showing same value for all cells
- Ensure all 3 fields (Rows, Columns, Values) come from the same table
- Check that Cell elements → Background color → conditional formatting is enabled
- Set Format style to `Gradient` with min `#EAF3DE` and max `#1B5E20`

### Export to Excel not visible
- Click table visual
- Format → Visual tab → `Visual header` → toggle On
- `Visual header icons` → `Export data` → toggle On
