# Van Rakshak — System Architecture

## Overview

Van Rakshak follows a modern cloud-native analytics architecture with three distinct tiers: data collection, data engineering, and analytics/visualization.

---

## Tier 1 — Data Collection

### Mobile App (Android)
- Forest guards use Android devices to log patrol sessions
- Records GPS coordinates, start/end times, beat assignments, and distance
- Syncs data to the backend via REST API when connectivity is available
- Supports offline patrol logging with local storage

### Backend API (FastAPI)
- Built with **FastAPI** (Python) and **SQLAlchemy ORM**
- Handles mobile data synchronization
- Enforces multi-tenant isolation using forest division prefixes (`southBetul_*`)
- REST endpoints for patrol submission, officer management, and attendance

### Django Admin
- Web-based admin interface for forest department administrators
- Manages officer profiles, beat assignments, and division configuration
- Built on **Django** with custom admin views

---

## Tier 2 — Data Engineering

### Storage Layer

| Store | Technology | Data Type |
|-------|-----------|-----------|
| Relational DB | Azure SQL / PostgreSQL | Patrol records, officers, attendance |
| File Storage | Azure Blob Storage | .kml patrol track files |
| Data Lakehouse | Azure Synapse Analytics | Parquet files (batch analytics) |

### Azure Synapse Analytics
- Serves as the central data lakehouse
- Raw patrol data loaded as **Parquet files** via batch pipelines
- **Serverless SQL Pool** exposes data as views (no data movement)
- Star Schema implemented via layered view scripts:

```
01a_base_tables.sql     → Raw source views
01b_staging.sql         → Cleaned/typed staging views
02_facts.sql            → Fact views (patrol routes, incidents)
03_dimensions.sql       → Dimension views (date, guard, patrol, month)
```

### Star Schema Views

```
                    ┌─────────────────┐
                    │ vw_sb_dim_date  │
                    └────────┬────────┘
                             │
┌──────────────┐    ┌────────┴────────┐    ┌─────────────────┐
│vw_sb_dim_    │    │ vw_sb_patrol_   │    │ vw_sb_dim_      │
│guard         ├────┤ all (FACT)      ├────┤ patrol          │
└──────────────┘    └────────┬────────┘    └─────────────────┘
                             │
                    ┌────────┴────────┐
                    │vw_sb_patrol_    │
                    │night (FACT)     │
                    └─────────────────┘
```

**Key Views:**

| View | Description |
|------|-------------|
| `vw_sb_patrol_all` | All day patrol records with guard, beat, distance, date |
| `vw_sb_patrol_night` | Night shift patrol records |
| `vw_sb_patrol_route` | GPS route points with lat/long coordinates |
| `vw_sb_patrol_day` | Day-level aggregated patrol data |
| `vw_sb_dim_date` | Date dimension (calendar_date, Week_Num, Month, Year) |
| `vw_sb_dim_guard` | Guard/officer dimension (name, designation, division) |
| `vw_sb_dim_patrol` | Patrol hierarchy (sub_division, range, circle, beat) |
| `vw_sb_dim_month` | Month dimension for time intelligence |
| `vw_sb_kpi_patrol_monthly` | Pre-aggregated monthly KPI view |
| `vw_sb_adv_patrol_route` | Advanced route analytics |
| `vw_sb_adv_same_patrol` | Same-beat overlap detection |

---

## Tier 3 — Analytics & Visualization

### Shared Dataset / Thin Report Architecture

```
Azure Synapse (vw_sb_* views)
        │
        │ OAuth2 / Gateway
        ▼
VanRakshak_MasterDataset.pbix
[VanRakshak-SharedDataset Workspace]
├── Star Schema relationships
├── 4 Custom DAX measures
├── Scheduled refresh (daily 04:00 AM)
└── RLS roles (division-level)
        │
        │ Live Connection (no data import)
        ├──────────────────────────────────────┐
        ▼                                      ▼
Patrolling Summary              Night Patrolling Summary
[VR-Workspace-Patrolling]       [VR-Workspace-NightPatrol]
        │                                      │
        ▼                                      ▼
Foot Patrol Officer             Night Patrol Officer
[VR-Workspace-Officers]         [VR-Workspace-Officers]
        │                                      │
        ▼                                      ▼
Patrolling Data Extractor       Night Data Extractor
[VR-Workspace-DataExtract]      [VR-Workspace-DataExtract]
```

### Key Design Decisions

**Why Shared Dataset?**
- Zero dataset duplication — one refresh pipeline for all reports
- Single point of truth for all DAX measures
- RLS defined once, propagates to all thin reports automatically
- RBAC enforced at workspace level — users only see their assigned report

**Why Live Connection?**
- Thin reports contain zero data — only visualization logic
- All queries pass through to the master dataset
- Instant updates when master dataset refreshes
- No risk of data drift between reports

**Why Azure Synapse Serverless?**
- No data movement — views query Parquet files in-place
- Scales automatically with query load
- Cost-effective — pay per query, not per cluster
- Star Schema implemented purely in SQL views (no ETL tools needed)

---

## Data Flow

```
Forest Guard → Android App → FastAPI → Azure SQL
                                           │
                              Batch Pipeline (nightly)
                                           │
                                           ▼
                               Azure Synapse Analytics
                               (Parquet + SQL Views)
                                           │
                              Power BI Scheduled Refresh
                              (Daily 04:00 AM)
                                           │
                               MasterDataset in Service
                                           │
                              All 6 Thin Reports (Live)
                                           │
                               Forest Department Users
                               (Role-based access via Apps)
```

---

## Security Architecture

### Authentication
- **OAuth2 / Azure Active Directory** — all connections use organizational identity
- No SQL passwords stored — all gateway connections use SSO

### Authorization
- **Workspace-level RBAC** — users only see workspaces they're assigned to
- **Build permissions** — users can query master dataset but not see it
- **Row-Level Security (RLS)** — division-level data isolation in master dataset
- **Power BI Apps** — hides workspace structure from end users

### Data Privacy
- No PII beyond officer names (organizational context)
- GPS coordinates are patrol routes on public forest land
- All data stays within Azure tenant boundary
