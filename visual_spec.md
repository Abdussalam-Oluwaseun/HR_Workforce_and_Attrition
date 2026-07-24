# Visual Specification
## HR Workforce Planning & Attrition — Power BI Report
### Canvas Size: 1920 × 1080 px

> All visual decisions agreed prior to development. Use this document alongside the Pre-Development Specification.

---

## Canvas Zone Reference

Every page shares the same structural zone map:

```
┌─────────────────────────────────────────────────────────────────────────┐  ← y: 0
│  NAV BAR (page navigation buttons + report title)        H: ~80px       │
├─────────────────────────────────────────────────────────────────────────┤  ← y: 80
│  KPI CARDS STRIP (4–6 cards)                             H: ~140px      │
├────────────────────────────────────────────┬────────────────────────────┤  ← y: 220
│                                            │                            │
│  MAIN VISUAL (primary chart)               │  SECONDARY VISUAL          │
│  W: ~1200–1350px                           │  W: ~550–700px             │
│  H: ~450px                                 │  H: ~450px                 │
│                                            │                            │
├──────────────────┬─────────────────────────┴─────────────┬─────────────┤  ← y: 670
│  BOTTOM LEFT     │  BOTTOM CENTRE                        │  BOTTOM RIGHT│
│  ~550px wide     │  ~820px wide                          │  ~550px wide │
│  H: ~370px       │  H: ~370px                            │  H: ~370px   │
└──────────────────┴───────────────────────────────────────┴─────────────┘  ← y: 1040
```

---

## Page 1 — Overview

**Objective:** Workforce Growth — headcount trends, geographic spread, and workforce demographics at a glance.

### Layout

```
┌───────────────────────────────────────────────────────────────────────┐
│ NAV BAR + TITLE                                                        │
├────────────┬────────────┬────────────┬────────────┬────────────────────┤
│  KPI Card  │  KPI Card  │  KPI Card  │  KPI Card  │  [user-defined]    │
├─────────────────────────────────────┬─────────────────────────────────┤
│                                     │                                  │
│  Headcount Trend Line Chart         │  Dept Headcount Bar Chart        │
│  (all depts, 36 months)             │  (with Level drill-down)         │
│                                     │                                  │
├──────────────────┬──────────────────┴──────────────┬──────────────────┤
│  Gender Donut    │  ZoomCharts Drill Down Map PRO   │  Age Band Donut  │
│                  │  (office locations)              │                  │
└──────────────────┴──────────────────────────────────┴──────────────────┘
```

### Visuals

| # | Visual | Type | Fields | Notes |
|---|---|---|---|---|
| 1 | KPI Cards strip | Card visuals | User-defined (4–6 cards) | Include: Active Headcount, Total FTE, Attrition Rate, Plan Variance minimum |
| 2 | **Headcount Trend** | Line chart | X = Month, Y = Headcount, Legend = Department | One line per department + a bolder 'Total' line. Source: FactMonthlySnapshot |
| 3 | **Department Headcount** | Bar chart (vertical) | X = Department → Level (drill-down), Y = Headcount | Drill path: Department → Level (L1–L5). Source: FactMonthlySnapshot |
| 4 | **ZoomCharts Map PRO** | Drill Down Map PRO | Location = DimLocation[Location], Size = Headcount, Colour = Attrition Rate | Exclude LocationID = 'L006' (Remote). Source: FactMonthlySnapshot + FactEmployeeEvents |
| 5 | **Gender Split** | Donut chart | Values = Employee count, Legend = Gender | Small donut, bottom-left. Source: DimEmployee |
| 6 | **Age Band Split** | Donut chart | Values = Employee count, Legend = AgeBand | Small donut, bottom-right. Source: DimEmployee |

### Page Slicers
- Date Range (Year / Quarter)
- Department
- Location

---

## Page 2 — Attrition & Exits

**Objective:** Attrition Risk + Termination Reasons — show who is leaving, when, why, and from where.

### Layout

```
┌───────────────────────────────────────────────────────────────────────┐
│ NAV BAR + TITLE                                                        │
├────────────┬────────────┬────────────┬────────────────────────────────┤
│  KPI Card  │  KPI Card  │  KPI Card  │  KPI Card                      │
├───────────────────────────────────────┬───────────────────────────────┤
│                                       │                               │
│  Monthly Termination Trend Line       │  ZoomCharts Donut PRO         │
│  (with IsRestructuringExit toggle)    │  Voluntary/Involuntary        │
│                                       │  → Termination Reason         │
│                                       │                               │
├─────────────────────────────────────┬─┴─────────────────────────────┤
│                                     │                                │
│  Dept Attrition Rate                │  Tenure-at-Exit Stacked Bar   │
│  Horizontal Bar Chart               │  (TenureBand × Vol/Invol)     │
│                                     │                                │
└─────────────────────────────────────┴────────────────────────────────┘
```

### Visuals

| # | Visual | Type | Fields | Notes |
|---|---|---|---|---|
| 1 | KPI Cards strip | Card visuals | User-defined | Suggested: Total Exits, Organic Attrition Rate, Voluntary Exit %, Structural Exits |
| 2 | **Monthly Termination Trend** | Line chart | X = EventDate (Month), Y = COUNT(EmployeeID) where EventType = 'Termination' | Include bookmark toggle: All Exits vs Organic Only (IsRestructuringExit = FALSE). Source: FactEmployeeEvents |
| 3 | **ZoomCharts Donut PRO** | Drill Down Donut Chart PRO | Drill Level 1 = TerminationType (Voluntary/Involuntary), Drill Level 2 = TerminationReason | Top-level shows Vol/Invol split; drill reveals the 8 specific reasons. Source: FactEmployeeEvents |
| 4 | **Department Attrition Rate** | Horizontal bar chart | Y = Department (sorted by attrition rate % desc), X = Attrition Rate % | Use rate, not raw count. Conditional formatting: red if rate > overall avg. Source: FactEmployeeEvents + FactMonthlySnapshot |
| 5 | **Tenure-at-Exit** | Stacked bar chart | X = TenureBandAtExitOrEnd, Y = Exit count, Colour = TerminationType (Voluntary/Involuntary) | Only for terminated employees. Reveals early vs late-career exit patterns. Source: DimEmployee filtered to Terminated |

### Page Slicers
- Date Range
- Department
- Location
- Level
- Termination Type
- IsRestructuringExit (Yes/No toggle)

---

## Page 3 — Retention Drivers

**Objective:** Retention Drivers — engagement, performance, compensation, and absence as predictors of attrition.

### Layout

```
┌───────────────────────────────────────────────────────────────────────┐
│ NAV BAR + TITLE                                                        │
├────────────┬────────────┬────────────┬────────────────────────────────┤
│  KPI Card  │  KPI Card  │  KPI Card  │  KPI Card                      │
├──────────────────────────────┬────────────────────────────────────────┤
│                              │                                        │
│  Engagement vs Attrition     │  Performance Rating Distribution       │
│  Scatter Plot                │  Clustered Bar (Active vs Terminated)  │
│  (per department)            │                                        │
│                              │                                        │
├─────────────────────────────┬┴───────────────────────────────────────┤
│                             │                                         │
│  Salary by Level Bar Chart  │  Absence by Dept Stacked Bar + Line     │
│  (avg ± min/max range)      │  (Sick Leave vs Unpaid + avg days line) │
│                             │                                         │
└─────────────────────────────┴─────────────────────────────────────────┘
```

### Visuals

| # | Visual | Type | Fields | Notes |
|---|---|---|---|---|
| 1 | KPI Cards strip | Card visuals | User-defined | Suggested: Avg Engagement Score, Disengaged %, Avg Performance Rating, Avg Days Absent/Employee |
| 2 | **Engagement vs Attrition Scatter** | Scatter plot | X = Avg EngagementScore, Y = Attrition Rate %, Size = Headcount, Details = Department | One bubble per department. Low-engagement + high-attrition quadrant = critical risk zone. Source: FactEngagement + FactEmployeeEvents + FactMonthlySnapshot |
| 3 | **Performance Rating Split** | Clustered bar chart | X = PerformanceRating (1–5), Y = Employee count, Cluster = EmploymentStatus (Active / Terminated) | Two bars per rating level. Reveals whether exits cluster at low or high ratings. Source: FactPerformance joined to DimEmployee |
| 4 | **Salary by Level** | Bar chart (vertical) | X = Level (L1–L5), Y = Avg AnnualSalary, Error bars = Min and Max AnnualSalary | Overlapping error bars between adjacent levels expose salary compression. All EUR. Source: FactCompensation |
| 5 | **Absence by Department** | Stacked bar + line combo | X = Department, Bars = Sick Leave days vs Unpaid Leave days (stacked), Line = Avg DaysAbsent per employee | Dual axis required (left = total days, right = avg days per person). Source: FactAbsence |

### Page Slicers
- Date Range
- Department
- Level
- EngagementRiskFlag (High Risk / All)

---

## Page 4 — Workforce vs Plan

**Objective:** Workforce Targets — headcount and FTE actuals vs plan, salary budget vs actual, with variance flags and scenario comparison.

### Layout

```
┌───────────────────────────────────────────────────────────────────────┐
│ NAV BAR + TITLE                                                        │
├────────────┬────────────┬────────────┬────────────────────────────────┤
│  KPI Card  │  KPI Card  │  KPI Card  │  KPI Card                      │
├───────────────────────────────────────────────────────────────────────┤
│                                                                        │
│  Headcount Variance Matrix                                             │
│  Rows = Department | Columns = Quarter | Cells = Variance (red/green) │
│  Full-width                                                            │
│                                                                        │
├──────────────────────────────────────┬────────────────────────────────┤
│                                      │                                │
│  Planned vs Actual Salary Cost       │  Scenario Comparison Cards     │
│  Clustered Bar (per dept)            │  or supplementary detail table │
│                                      │                                │
└──────────────────────────────────────┴────────────────────────────────┘
```

### Visuals

| # | Visual | Type | Fields | Notes |
|---|---|---|---|---|
| 1 | KPI Cards strip | Card visuals | User-defined | Suggested: FTE Variance, Headcount Variance (total), Budget Utilisation %, Planning Scenario (current) |
| 2 | **Headcount Variance Matrix** | Matrix visual | Rows = Department, Columns = Quarter (Q1 2023 → Q4 2025), Values = HeadcountVariance (Actual − Planned) | Conditional formatting: green = at/above plan, red = below plan. Include 'Target Met' icon column. Source: FactMonthlySnapshot vs WorkforceTargets |
| 3 | **Salary Budget vs Actual** | Clustered bar chart | X = Department, Y = EUR value, Cluster = Planned (BudgetedSalaryCost) vs Actual (from FactCompensation sum) | Split by Planning Scenario via slicer. Source: WorkforceTargets + FactCompensation |
| 4 | **Scenario Detail Table** | Table visual | Columns = Department, PlanningScenario, PlannedHeadcount, ActualHeadcount, Variance, Target Met (Yes/No icon) | Supplementary detail view below the bar chart. Supports the drill-down from the matrix. |

### Page Slicers
- Date Range
- Department
- Planning Scenario (Baseline / Post-restructuring reset)

---

## Page 5 — Recruitment

**Objective:** Recruitment Support — fill rate, time-to-hire, and pipeline health by department.

### Layout

```
┌───────────────────────────────────────────────────────────────────────┐
│ NAV BAR + TITLE                                                        │
├────────────┬────────────┬────────────┬────────────────────────────────┤
│  KPI Card  │  KPI Card  │  KPI Card  │  KPI Card                      │
├───────────────────────────────────────────────────────────────────────┤
│                                                                        │
│  Fill Rate % Over Time — Line Chart (with 70% reference line)         │
│  Full-width                                                            │
│                                                                        │
├──────────────────────────────────────┬────────────────────────────────┤
│                                      │                                │
│  Avg Days to Fill                    │  Requisition Pipeline          │
│  Horizontal Bar (dept ranked)        │  Stacked Bar (Open/Filled/     │
│                                      │  Cancelled per dept per month) │
└──────────────────────────────────────┴────────────────────────────────┘
```

### Visuals

| # | Visual | Type | Fields | Notes |
|---|---|---|---|---|
| 1 | KPI Cards strip | Card visuals | User-defined | Suggested: Avg Fill Rate %, Avg Days to Fill, Total Open Reqs, Total Cancelled Reqs |
| 2 | **Fill Rate % Trend** | Line chart | X = Month, Y = Fill Rate % (FilledRequisitions / OpenRequisitions) | Add a constant reference line at 70% as target. Add linear trend line. Source: FactRecruitment |
| 3 | **Avg Days to Fill by Dept** | Horizontal bar chart | Y = Department (sorted by AvgDaysToFill desc), X = Avg Days to Fill | Slowest dept at top. Conditional formatting: red if > 60 days, amber 45–60, green < 45. Source: FactRecruitment |
| 4 | **Requisition Pipeline** | Stacked bar chart | X = Department (or Month), Stacks = OpenRequisitions / FilledRequisitions / CancelledRequisitions | Reveals which depts have the highest cancellation rate (demand instability). Source: FactRecruitment |

### Page Slicers
- Date Range
- Department

---

## Cross-Page Elements (All Pages)

| Element | Description |
|---|---|
| **Navigation bar** | Top strip with 5 buttons (one per page), active page highlighted. Height: ~80px |
| **Page title** | Left-aligned in the nav bar or immediately below it. Consistent font + colour |
| **Report title / logo** | Top-left corner of nav bar across all pages |
| **Slicer reset button** | Bookmark button to clear all filters — present on every page |
| **Tooltip pages** | Custom tooltip pages for key visuals (e.g., map tooltip showing dept breakdown per office) |

---

## Visual Count Summary

| Page | KPI Cards | Charts / Visuals | ZoomCharts | Total |
|---|---|---|---|---|
| Overview | User-defined | 3 (line, bar, 2× donut) | 1 (Map PRO) | 5+ |
| Attrition & Exits | User-defined | 3 (line, horizontal bar, stacked bar) | 1 (Donut PRO) | 4+ |
| Retention Drivers | User-defined | 4 (scatter, clustered bar, bar w/ error bars, combo bar+line) | — | 4+ |
| Workforce vs Plan | User-defined | 3 (matrix, clustered bar, table) | — | 3+ |
| Recruitment | User-defined | 3 (line, horizontal bar, stacked bar) | — | 3+ |
| **Total** | | **16 charts** | **2 ZoomCharts** | **18+ visuals** |
