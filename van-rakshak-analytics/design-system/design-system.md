# Van Rakshak — Design System

A consistent visual design language applied across all 6 Power BI reports.

---

## Color Palette

### Primary Colors
| Name | Hex | Usage |
|------|-----|-------|
| Forest Green | `#1D6B3A` | Header background, KPI values, bar charts |
| Accent Lime | `#C0DD97` | Header text, button backgrounds, chart accents |
| Light Green | `#EAF3DE` | Card backgrounds, alternate row shading, slicer fill |
| Dark Forest | `#1D3D2A` | Footer background, dark text, deep accents |
| Teal Highlight | `#9FE1CB` | Subtitle text, badge text, secondary accents |
| Olive Green | `#3B6D11` | Border colors, label text, secondary green |
| Mid Green | `#27500A` | Data values in tables, chart data labels |

### Semantic Colors
| Name | Hex | Usage |
|------|-----|-------|
| Alert Red | `#C0392B` | Overlapping Patrols table header, warning indicators |
| Success Green | `#27AE60` | Positive MoM trend indicators |
| Warning Orange | `#E67E22` | Negative trend alerts |
| Page Background | `#F4F9F1` | Canvas background tint |

### Heatmap Gradient
| Stop | Hex | Meaning |
|------|-----|---------|
| Minimum | `#EAF3DE` | Low patrol activity |
| Mid-Low | `#C8E6C9` | Below average activity |
| Mid | `#81C784` | Average activity |
| Mid-High | `#388E3C` | Above average activity |
| Maximum | `#1B5E20` | High patrol activity |

---

## Typography

| Element | Font | Size | Weight | Color |
|---------|------|------|--------|-------|
| Report Title | Arial / Segoe UI | 18–22px | Bold | `#C0DD97` |
| Report Subtitle | Arial / Segoe UI | 11px | Regular | `#9FE1CB` |
| Section Titles | Arial / Segoe UI | 11px | Bold | `#1D3D2A` |
| KPI Value | Arial / Segoe UI | 28–32px | Medium | `#1D6B3A` |
| KPI Label | Arial / Segoe UI | 11px | Regular | `#5F5E5A` |
| Slicer Label | Arial / Segoe UI | 9px | Bold, UPPERCASE | `#3B6D11` |
| Table Header | Arial / Segoe UI | 10px | Bold | `#C0DD97` on `#1D6B3A` |
| Table Values | Arial / Segoe UI | 11px | Regular | `#1D3D2A` |
| Button Text | Arial / Segoe UI | 10–11px | Medium | `#1D6B3A` or `#C0DD97` |

---

## Component Specifications

### Header Bar
```
Background:    #1D6B3A
Height:        65px
Title font:    18px Bold #C0DD97
Subtitle font: 11px Regular #9FE1CB
Padding:       14px 20px
```

### Dropdown Slicers
```
Background:    #EAF3DE
Border:        0.5px solid #C0DD97
Border-radius: 6px
Label:         9px Bold UPPERCASE #3B6D11
Value text:    11px #1D3D2A
Style:         Dropdown (not list)
```

### Date Slicer
```
Background:    #EAF3DE
Border:        0.5px solid #C0DD97
Border-radius: 10px
Style:         Between (no slider)
Slider:        Off
```

### Bookmark Navigation Buttons
Active state:
```
Background:    #1D6B3A
Font color:    #C0DD97
Border:        0.5px solid #1D6B3A
Border-radius: 20px
Font size:     10px Bold
```

Inactive state:
```
Background:    #EAF3DE
Font color:    #3B6D11
Border:        0.5px solid #C0DD97
Border-radius: 20px
Font size:     10px
```

### KPI Cards (Classic Card)
```
Visual type:   Classic Card (not new card)
Display units: None (full number)
Value size:    32px #1D6B3A Bold
Label color:   #5F5E5A 11px
Background:    #F4F9F1
Border:        0.5px solid #C0DD97
Border-radius: 8px
```

### Table Visual
```
Column headers:
  Background:  #1D6B3A
  Font color:  #C0DD97
  Font weight: Bold
  Font size:   10px

Values (odd rows):
  Background:  #FFFFFF
  Font color:  #1D3D2A

Values (even rows):
  Background:  #F4F9F1
  Font color:  #1D3D2A

Total row:
  Background:  #C0DD97
  Font color:  #1D3D2A
  Font weight: Bold
```

### Alert Table (Overlapping Patrols)
```
Column headers:
  Background:  #C0392B (red)
  Font color:  #FFFFFF
  Font weight: Bold
```

### Footer Bar
```
Background:    #1D3D2A
Text:          "Copyright © 2024 Intra Techno Solutions Private Limited..."
Font:          9px #5DCAA5
Alignment:     Center
```

---

## Canvas Background

**Botanical Wallpaper** — SVG-based forest botanical illustration featuring:
- Leafy plant illustrations on left and right margins
- Forest tree silhouettes on the right side
- Paw print decorative elements
- Vine decoration along top edge
- White report canvas center area
- Dark green gradient footer section

Applied via: `Format page → Wallpaper → Add image → set to Fill`

---

## Layout Grid

### Standard Report Layout (1280 × 720px)
```
┌────────────────────────────────────────┐
│ HEADER BAR (65px)                      │
├────────────────────────────────────────┤
│ FILTER ROW — 4 slicers + date (80px)   │
├────────────────────────────────────────┤
│ BOOKMARK BUTTONS ROW (45px)            │
├────────────────────────────────────────┤
│ KPI CARDS ROW — 4 cards (100px)        │
├─────────────────┬──────────────────────┤
│                 │                      │
│  LEFT PANEL     │   RIGHT PANEL        │
│  (Table /       │   (Charts /          │
│   Main Visual)  │    Analytics)        │
│                 │                      │
├────────────────────────────────────────┤
│ FOOTER BAR (30px)                      │
└────────────────────────────────────────┘
```

### Advanced Analytics Layout (Page 2)
```
┌────────────────────────────────────────┐
│ HEADER BAR (65px)                      │
├────────────────────────────────────────┤
│ NAVIGATION BUTTON (← Summary View)    │
├──────────────────┬─────────────────────┤
│ HEATMAP          │ MoM COMPARISON      │
│ (Week × Month)   │ (Range × Month)     │
├──────────────────┼─────────────────────┤
│ AREA COVERAGE    │ OVERLAPPING         │
│ PER BEAT         │ PATROLS ⚠️          │
├────────────────────────────────────────┤
│ FOOTER BAR                             │
└────────────────────────────────────────┘
```

---

## Naming Conventions

### File Names
```
VanRakshak_MasterDataset.pbix
VanRakshak_[ReportName]_report.pbix
VanRakshak_Theme_Light.json
VanRakshak_Theme_Earthy.json
```

### Workspace Names
```
VanRakshak-SharedDataset    ← Master dataset workspace
VR-Workspace-Patrolling     ← Day patrol reports
VR-Workspace-NightPatrol    ← Night patrol reports
VR-Workspace-Officers       ← Officer-level reports
VR-Workspace-DataExtract    ← Data extractor reports
```

### DAX Measure Names
```
MoM Distance % Change
MoM Night Distance % Change
Overlapping Patrols
Avg Distance Per Beat
```
