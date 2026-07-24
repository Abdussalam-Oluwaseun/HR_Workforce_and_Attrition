# Pre-Development Specification
## HR Workforce Planning & Attrition — Power BI Report

> All decisions below were agreed before development began. This document is the technical contract that governs how the Power BI model, DAX layer, and report structure will be built.

---

## Table of Contents
1. [Attrition KPI Definition](#1-attrition-kpi-definition)
2. [Date Model Strategy](#2-date-model-strategy)
3. [Core KPI Card Set](#3-core-kpi-card-set)
4. [Calculated Columns & Derived Fields](#4-calculated-columns--derived-fields)
5. [Slicer & Filter Strategy](#5-slicer--filter-strategy)
6. [Row-Level Security](#6-row-level-security)
7. [Report Page Structure](#7-report-page-structure)
8. [ZoomCharts Visual Selection](#8-zoomcharts-visual-selection)
9. [Data Source & Refresh Strategy](#9-data-source--refresh-strategy)
10. [DAX Measures Organisation](#10-dax-measures-organisation)
11. [Development Checklist](#11-development-checklist)

---

## 1. Attrition KPI Definition

**Method:** Method A — Average Headcount denominator

```
Attrition Rate = Terminations ÷ Average Headcount in Period
```

**Restructuring Handling:**
- **Headline attrition KPI** → **Organic attrition only** (excludes Company Restructuring + Role Eliminated exits)
- **Structural exits** → Displayed as a separate `Structural Exits` KPI card

**Segmentation logic:**
| Category | TerminationReason values | DAX Flag |
|---|---|---|
| Structural (Involuntary) | Company Restructuring, Role Eliminated | `IsRestructuringExit = TRUE` |
| Organic Voluntary | Compensation, Work-Life Balance, Career Growth, Manager Fit, Relocation | `IsRestructuringExit = FALSE`, TerminationType = Voluntary |
| Organic Involuntary | Performance | `IsRestructuringExit = FALSE`, TerminationType = Involuntary |

> [!IMPORTANT]
> All rolling attrition rate measures must use `AVERAGEX` over monthly headcount snapshots from `FactMonthlySnapshot`, not a simple count of active employees at a point in time.

---

## 2. Date Model Strategy

**Approach:** Single `DimDate` table with **one active relationship** + `USERELATIONSHIP()` in DAX for all other date columns.

| Fact Table | Date Column | Relationship to DimDate | Status |
|---|---|---|---|
| FactMonthlySnapshot | Month | DimDate[Month] | ✅ Active |
| FactEmployeeEvents | EventDate | DimDate[Date] | ✅ Active |
| FactCompensation | EffectiveDate | DimDate[Date] | ⚠️ Inactive — use USERELATIONSHIP() |
| FactPerformance | ReviewDate | DimDate[Date] | ⚠️ Inactive — use USERELATIONSHIP() |
| FactEngagement | SurveyDate | DimDate[Date] | ⚠️ Inactive — use USERELATIONSHIP() |
| FactAbsence | AbsenceDate | DimDate[Date] | ⚠️ Inactive — use USERELATIONSHIP() |
| FactRecruitment | Month | DimDate[Month] | ✅ Active |
| WorkforceTargets | Month | DimDate[Month] | ✅ Active |

> [!NOTE]
> Mark `DimDate` as the official **Date Table** in Power BI Desktop (Table Tools → Mark as date table → select `Date` column). This enables built-in time-intelligence functions (DATESYTD, DATESINPERIOD, etc.).

---

## 3. Core KPI Card Set

Five headline KPIs to feature as scorecards on relevant report pages:

| # | KPI Name | Formula (DAX Summary) | Source Tables | Page |
|---|---|---|---|---|
| 1 | **Rolling 12-Month Attrition Rate** | Organic terminations (L12M) ÷ Avg Headcount (L12M) | FactEmployeeEvents, FactMonthlySnapshot | Overview, Attrition |
| 2 | **Organic Attrition Rate** | Non-restructuring terminations ÷ Avg Headcount | FactEmployeeEvents, FactMonthlySnapshot | Attrition |
| 3 | **Recruitment Fill Rate %** | FilledRequisitions ÷ OpenRequisitions | FactRecruitment | Recruitment |
| 4 | **FTE vs Budgeted FTE (Variance)** | FactMonthlySnapshot[FTE] − WorkforceTargets[BudgetedFTE] | FactMonthlySnapshot, WorkforceTargets | Workforce vs Plan |
| 5 | **Avg Days to Fill (Trend)** | AVERAGE(FactRecruitment[AvgDaysToFill]) | FactRecruitment | Recruitment |

> [!TIP]
> Add conditional formatting to KPI cards: red for attrition above threshold, green for fill rate above 70%, amber for FTE variance > ±10%.

---

## 4. Calculated Columns & Derived Fields

Five derived fields to create **before building any visuals:**

| Field Name | Table | Type | Logic | Purpose |
|---|---|---|---|---|
| `IsRestructuringExit` | FactEmployeeEvents | Calculated Column | `TerminationReason IN ("Company Restructuring", "Role Eliminated")` | Separates structural from organic attrition in all measures |
| `AttritionFlag` | DimEmployee | Calculated Column | `IF(EmploymentStatus = "Terminated", 1, 0)` | Numeric flag for counting terminated employees in DAX |
| `YearsOfTenure` | DimEmployee | Calculated Column | `ROUND(TenureMonthsAtExitOrEnd / 12, 1)` | Continuous tenure metric for scatter plots and correlations |
| `EngagementRiskFlag` | FactEngagement | Calculated Column | `IF(EngagementScore <= 2, TRUE, FALSE)` | Identifies disengaged employees for attrition risk overlays |
| `AbsenceFrequencyBand` | FactAbsence (agg.) | Measure / Calc Table | Count of absence events per employee: Low (1), Medium (2–3), High (4+) | Absence-as-risk-proxy segmentation |

> [!NOTE]
> `AbsenceFrequencyBand` requires aggregating FactAbsence at the EmployeeID level. Build this as a DAX measure (`COUNTROWS` + `CALCULATE` + employee context) rather than a calculated column, to avoid row-context issues.

---

## 5. Slicer & Filter Strategy

**Approach:** Page-level slicers only — each page surfaces only the filters relevant to its topic.

| Page | Slicers Available |
|---|---|
| Overview | Date Range (Year/Quarter), Department, Location |
| Attrition & Exits | Date Range, Department, Location, Level, Termination Type, IsRestructuringExit |
| Retention Drivers | Date Range, Department, Level, Engagement Risk Flag, Performance Tier |
| Workforce vs Plan | Date Range, Department, Planning Scenario |
| Recruitment | Date Range, Department |

**Cross-filter direction:** Single direction (Dimension → Fact) for all relationships. Enable bidirectional only for `DimEmployee ↔ FactEmployeeEvents` if slicer cross-highlighting is needed.

> [!WARNING]
> Avoid bidirectional cross-filtering between `DimEmployee` and multiple fact tables simultaneously — this can cause ambiguous filter paths and slow query performance.

---

## 6. Row-Level Security

**Decision:** No RLS for this report.

The report is a single, fully open view intended for competition judges and HR leadership stakeholders. All 12 tables will be visible in their entirety to all viewers.

*If repurposed for a live HR tool post-competition, add RLS by DimDepartment[Department] to restrict department-level access.*

---

## 7. Report Page Structure

**5-page report** — each page maps directly to one or more of the 6 challenge objectives:

| Page # | Page Name | Challenge Objectives Covered | Primary Tables |
|---|---|---|---|
| 1 | **Overview** | Workforce Growth | FactMonthlySnapshot, DimEmployee, WorkforceTargets |
| 2 | **Attrition & Exits** | Attrition Risk, Termination Reasons | FactEmployeeEvents, DimEmployee |
| 3 | **Retention Drivers** | Retention Drivers | FactEngagement, FactPerformance, FactAbsence, FactCompensation |
| 4 | **Workforce vs Plan** | Workforce Targets | FactMonthlySnapshot, WorkforceTargets |
| 5 | **Recruitment** | Recruitment Support | FactRecruitment, FactEmployeeEvents |

**Navigation:** Button-based page navigation bar consistent across all pages.

---

## 8. ZoomCharts Visual Selection

**Selected visuals:**

| Visual | Page(s) | Data Shown | Drill Path |
|---|---|---|---|
| **Drill Down Donut Chart PRO** | Attrition & Exits | Termination reasons breakdown | Top-level type (Voluntary / Involuntary) → Specific reason |
| **Drill Down Map PRO** | Overview, Attrition & Exits | Headcount or attrition rate by office location | Region → Country → City |

**Standard Power BI visuals** for everything else (line charts, bar charts, cards, tables, scatter plots) — keeps the report fast and readable without over-engineering the visual layer.

> [!TIP]
> Use the ZoomCharts **Drill Down Map PRO** with `DimLocation[Latitude]` and `DimLocation[Longitude]` for precise pin placement on the office map. The `Remote` location (Lat: 0, Lon: 0) should be excluded from the map visual or handled with a separate card.

---

## 9. Data Source & Refresh Strategy

**Mode:** Import mode — static `.xlsx` file, no scheduled refresh.

**File:** `HR_Workforce_Planning_Attrition_Dataset_4U_Challenge.xlsx`

**Power Query steps to apply on load:**
1. Promote headers (already clean)
2. Set correct data types for all date columns (`datetime → Date`)
3. Create `IsRestructuringExit` flag column in Power Query (alternative to DAX calculated column) for FactEmployeeEvents
4. Replace `null` in `TerminationType` and `TerminationReason` with `"Active Employee"` for cleaner slicer display (or filter out in visuals)
5. Validate `EmployeeID` referential integrity across all fact tables (no orphaned keys)

> [!CAUTION]
> The `Remote` location has Latitude = 0.0 and Longitude = 0.0 — this will plot at the Gulf of Guinea on map visuals. Filter out `LocationID = "L006"` from any map-based visualisation, or replace coordinates with `null`.

---

## 10. DAX Measures Organisation

**Approach:** Dedicated `_KPI Measures` measures-only table (empty data table, all measures housed here).

**Suggested measure groups (organised by display folder):**

```
_KPI Measures
├── 📊 Headcount
│   ├── Total Headcount
│   ├── Active Headcount
│   ├── FTE Total
│   └── Headcount Growth Rate MoM %
│
├── ⚠️ Attrition
│   ├── Total Terminations
│   ├── Organic Terminations
│   ├── Structural Exits
│   ├── Rolling 12M Attrition Rate
│   └── Organic Attrition Rate
│
├── 🎯 Workforce Targets
│   ├── Planned Headcount
│   ├── Headcount Variance
│   ├── Budgeted FTE
│   ├── FTE Variance
│   ├── Budgeted Salary Cost
│   └── Salary Cost Variance
│
├── 🧲 Recruitment
│   ├── Open Requisitions
│   ├── Filled Requisitions
│   ├── Fill Rate %
│   ├── Cancelled Requisitions
│   └── Avg Days to Fill
│
├── ⭐ Performance & Engagement
│   ├── Avg Performance Rating
│   ├── High Performer %
│   ├── Low Performer %
│   ├── Avg Engagement Score
│   └── Disengaged Employee %
│
└── 🏥 Absence
    ├── Total Absence Events
    ├── Total Days Absent
    └── Avg Days Absent per Employee
```

---

## 11. Development Checklist

Use this as the step-by-step build order in Power BI Desktop:

- [ ] **Step 1 — Load Data**: Import all 12 sheets from `.xlsx` in Import mode
- [ ] **Step 2 — Power Query**: Apply data type corrections, clean nulls, add `IsRestructuringExit` column
- [ ] **Step 3 — Mark Date Table**: Set `DimDate` as official Date Table on `Date` column
- [ ] **Step 4 — Relationships**: Build all active relationships; set inactive relationships for non-primary date columns
- [ ] **Step 5 — Calculated Columns**: Add `AttritionFlag`, `YearsOfTenure`, `EngagementRiskFlag` in DAX
- [ ] **Step 6 — Measures Table**: Create `_KPI Measures` empty table; build all measures organised by display folder
- [ ] **Step 7 — Validate Measures**: Spot-check all 5 core KPIs against raw data for accuracy
- [ ] **Step 8 — Install ZoomCharts**: Add Drill Down Donut Chart PRO and Drill Down Map PRO from AppSource
- [ ] **Step 9 — Build Pages**: Construct all 5 report pages with agreed slicer sets and visuals
- [ ] **Step 10 — Polish**: Apply theme, conditional formatting, tooltips, and navigation buttons
