# Van Rakshak — RBAC & Workspace Design

Role-Based Access Control architecture for the Van Rakshak Power BI analytics suite.

---

## Two-Tier Permission Model

Access control is enforced at two levels:

### Tier 1 — Dataset Workspace (VanRakshak-SharedDataset)
All report consumers need **Build permission** on the master dataset.
This allows them to query the dataset but they cannot see the workspace or other reports.

```
VanRakshak-SharedDataset Workspace
└── VanRakshak_MasterDataset
    ├── SG-Patrolling-Users  → Build permission
    ├── SG-NightPatrol-Users → Build permission
    ├── SG-Officers          → Build permission
    ├── SG-Analysts          → Build permission
    └── SG-DataEngineers     → Admin
```

### Tier 2 — Report Workspaces (Viewer Role)
Each report has its own isolated workspace. Users only see the workspace assigned to them.

```
VR-Workspace-Patrolling
└── SG-Patrolling-Users → Viewer

VR-Workspace-NightPatrol
└── SG-NightPatrol-Users → Viewer

VR-Workspace-Officers
└── SG-Officers → Viewer

VR-Workspace-DataExtract
└── SG-Analysts → Viewer

VanRakshak-SharedDataset
└── SG-DataEngineers → Admin
```

---

## Workspace Structure

| Workspace | Reports | Users |
|-----------|---------|-------|
| `VanRakshak-SharedDataset` | MasterDataset only | Data Engineers (Admin) |
| `VR-Workspace-Patrolling` | Patrolling Summary | Forest Rangers, Range Officers |
| `VR-Workspace-NightPatrol` | Night Patrolling Summary | Night Shift Team |
| `VR-Workspace-Officers` | Foot Patrol + Night Patrol Officer | Field Supervisors |
| `VR-Workspace-DataExtract` | Patrolling + Night Data Extractor | Analysts, HR Admins |

---

## Row-Level Security (RLS)

RLS is defined on the master dataset and propagates automatically to all thin reports.

### Creating RLS Roles in Power BI Desktop
```
Modeling → Manage Roles → Create

Role: BetulSouth
Table: vw_sb_dim_guard
DAX Filter: [division_name] = "Betul South"
```

### Assigning RLS Roles in Power BI Service
1. Go to `VanRakshak-SharedDataset` workspace
2. Dataset → `⋯ → Security`
3. Select role → Add security group or user
4. Click `Save`

---

## Power BI Apps (End-User Layer)

Apps hide workspace structure from end users completely.

### Creating an App
1. In each report workspace → `Create app`
2. App name: e.g., `Van Rakshak — Patrolling`
3. Audience: specific security group only
4. Publish app

### User Experience
- Users access via `app.powerbi.com`
- They see only the app assigned to them
- No workspace navigation visible
- No access to other reports or datasets

---

## Permission Summary Matrix

| User Group | SharedDataset | Patrolling WS | NightPatrol WS | Officers WS | DataExtract WS |
|------------|--------------|---------------|----------------|-------------|----------------|
| Data Engineers | Admin | — | — | — | — |
| Forest Rangers | Build | Viewer | — | — | — |
| Night Shift | Build | — | Viewer | — | — |
| Field Supervisors | Build | — | — | Viewer | — |
| Analysts | Build | — | — | — | Viewer |

---

## Security Principles

1. **Least Privilege** — Users get minimum permissions needed
2. **Isolation** — Report workspaces are completely separate
3. **Single Source** — One dataset, one refresh, one RLS configuration
4. **No Direct Data Access** — Users query through semantic model only
5. **App Layer** — End users never see workspace structure
