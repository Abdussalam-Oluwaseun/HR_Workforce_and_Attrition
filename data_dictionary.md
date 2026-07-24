# HR Workforce Planning & Attrition - Data Dictionary

## Introduction
This document serves as the comprehensive data dictionary for the HR Workforce Planning & Attrition dataset. It details the schema, constraints, data types, and business logic for all 12 tables within the dataset, providing a foundational understanding for analytics and reporting tasks. The model consists of 3 dimension tables, 1 date table, 6 fact tables, and 1 planning table.

## Table of Contents
- [Dimensions](#dimensions)
  - [📋 DimEmployee](#-dimemployee)
  - [📋 DimDepartment](#-dimdepartment)
  - [📋 DimLocation](#-dimlocation)
- [Dates](#dates)
  - [🗓️ DimDate](#️-dimdate)
- [Facts](#facts)
  - [📊 FactMonthlySnapshot](#-factmonthlysnapshot)
  - [📊 FactEmployeeEvents](#-factemployeeevents)
  - [📊 FactCompensation](#-factcompensation)
  - [📊 FactPerformance](#-factperformance)
  - [📊 FactEngagement](#-factengagement)
  - [📊 FactAbsence](#-factabsence)
  - [📊 FactRecruitment](#-factrecruitment)
- [Planning](#planning)
  - [🎯 WorkforceTargets](#-workforcetargets)
- [Key Relationships](#key-relationships)

---

## Dimensions

### 📋 DimEmployee
**Description:** The master employee dimension. Contains one record for every person who has ever worked at the company — both currently active staff and those who have since left. It is the central hub of the data model, linking workforce demographics, seniority, location, and termination details to all analytical fact tables. Serves as the primary lens for workforce segmentation (by role, level, gender, age, tenure, and employment status).
**Grain:** One row per employee (active or terminated). Employees hired 2018-01-05 to 2025-12-27.
**Size:** 1,200 rows, 19 columns

| Column Name | Data Type | Nullable | Sample Values | Business Description |
| :--- | :--- | :--- | :--- | :--- |
| EmployeeID | str | NO | E100001 | Unique employee identifier (primary key). |
| DepartmentID | str | NO | D001 | Foreign key to DimDepartment. |
| Department | str | NO | Engineering, Sales | The department the employee belongs to. |
| Role | str | NO | Software Engineer (86), DevOps Engineer (80) | The employee's specific job role (29 unique roles). |
| Level | str | NO | L1(164), L2(271), L3(306), L4(311), L5(148) | The seniority level of the employee. |
| LocationID | str | NO | L001 | Foreign key to DimLocation. |
| Location | str | NO | Riga(470), London(239), Remote(71) | The geographic location of the employee. |
| ManagerID | str | NO | M045 | The employee's direct manager ID. |
| Gender | str | NO | Male(589), Female(583), Non-disclosed(28) | The gender of the employee. |
| BirthYear | int | NO | 1987 | The birth year of the employee. |
| AgeBand | str | NO | 20-29(257), 30-39(494), 40-49(350), 50-59(93), 60+(6) | The age group classification for the employee. |
| HireDate | datetime | NO | 2018-01-05 | The date the employee was hired (Range: 2018-01-05 to 2025-12-27). |
| TerminationDate | datetime | YES | 2023-02-23 | The date the employee left the company (994 nulls = active employees). |
| EmploymentStatus | str | NO | Active(994), Terminated(206) | Current status of employment. |
| TerminationType | str | YES | Involuntary(125), Voluntary(81) | Type of termination, if applicable (994 nulls). |
| TerminationReason | str | YES | Company Restructuring(95), Compensation(22) | Specific reason for termination (994 nulls). |
| FTE | float | NO | 1.0 (mean=0.969, min=0.6) | Full-time equivalent status. |
| TenureMonthsAtExitOrEnd | int | NO | 36 | Total tenure in months at termination or report end date. |
| TenureBandAtExitOrEnd | str | NO | 0-6 months, 1-2 years, 5+ years | Categorical tenure band. |

### 📋 DimDepartment
**Description:** The department reference table. Defines the 8 organisational departments within the company and groups them into broader functional areas (e.g., Product & Technology, Revenue, G&A). Each department is also mapped to a financial cost centre code, enabling budget and salary cost analysis by business unit. Used to slice every fact table by organisational unit.
**Grain:** One row per department.
**Size:** 8 rows, 4 columns

| Column Name | Data Type | Nullable | Sample Values | Business Description |
| :--- | :--- | :--- | :--- | :--- |
| DepartmentID | str | NO | D001 | Unique department identifier (primary key). |
| Department | str | NO | Engineering, Sales, HR | The name of the department. |
| Function | str | NO | Product & Technology, Revenue | The broader functional group the department falls under. |
| CostCenter | str | NO | CC-100, CC-200 | The financial cost center code. |

### 📋 DimLocation
**Description:** The office and remote location reference table. Captures the 5 physical offices (Riga, London, Berlin, New York, Toronto) plus a Remote category for employees with no fixed office. Includes geographic coordinates (Latitude/Longitude) that support map-based visualisations in Power BI, and regional groupings (Europe, North America, Global) for higher-level geographic analysis.
**Grain:** One row per office location.
**Size:** 6 rows, 7 columns

| Column Name | Data Type | Nullable | Sample Values | Business Description |
| :--- | :--- | :--- | :--- | :--- |
| LocationID | str | NO | L001 | Unique location identifier (primary key). |
| Location | str | NO | Riga, London, Berlin | The city or primary name of the location. |
| Country | str | NO | Latvia, United Kingdom, Remote | The country of the location. |
| Region | str | NO | Europe, North America, Global | The geographic region. |
| TimeZone | str | NO | EET, GMT, Mixed | The primary time zone for the location. |
| Latitude | float | NO | 56.9496 | Geographic coordinate. |
| Longitude | float | NO | 24.1052 | Geographic coordinate. |

---

## Dates

### 🗓️ DimDate
**Description:** The standard calendar date dimension that provides time intelligence across the entire model. Covers every calendar day from 1 January 2023 to 31 December 2025 — the full 36-month reporting window. Provides pre-calculated attributes (year, quarter, month name, weekday) to support time-based slicing, period-over-period comparisons, and rolling calculations in Power BI without complex DAX. Should be marked as the official Date Table in Power BI Desktop.
**Grain:** One row per calendar day (2023-01-01 to 2025-12-31).
**Size:** 1,096 rows, 7 columns

| Column Name | Data Type | Nullable | Sample Values | Business Description |
| :--- | :--- | :--- | :--- | :--- |
| Date | datetime | NO | 2023-01-01 | Calendar date (primary key). |
| Month | datetime | NO | 2023-01-01 | First day of the respective month. |
| Year | int | NO | 2023, 2024, 2025 | Calendar year. |
| Quarter | str | NO | Q1, Q2, Q3, Q4 | Calendar quarter. |
| MonthNumber | int | NO | 1, 2, 12 | Numeric month representation (1-12). |
| MonthName | str | NO | Jan, Feb, Dec | Abbreviated month name. |
| WeekdayNumber | int | NO | 1, 7 | Numeric day of the week (1=Monday, 7=Sunday). |

---

## Facts

### 📊 FactMonthlySnapshot
**Description:** A pre-aggregated periodic snapshot of workforce state, captured at the end of each month. Rather than requiring calculation from raw employee records, this table provides ready-made headcount, FTE, and average tenure figures sliced by department, location, and seniority level. It is the primary source for tracking workforce growth trends, comparing headcount across dimensions over time, and feeding period-over-period KPIs. Best used alongside WorkforceTargets to measure actual vs planned staffing levels.
**Grain:** One row per month × department × location × level combination.
**Size:** 6,826 rows, 9 columns
**Scope:** 2023-01-01 to 2025-12-01 (36 months).

| Column Name | Data Type | Nullable | Sample Values | Business Description |
| :--- | :--- | :--- | :--- | :--- |
| Month | datetime | NO | 2023-01-01 | Foreign key to DimDate. |
| DepartmentID | str | NO | D001 | Foreign key to DimDepartment. |
| Department | str | NO | Engineering | Denormalized department name. |
| LocationID | str | NO | L001 | Foreign key to DimLocation. |
| Location | str | NO | Riga | Denormalized location name. |
| Level | str | NO | L1, L2, L5 | Seniority level for the snapshot grouping. |
| Headcount | int | NO | 4 (mean=4.5, max=37) | Total number of employees in this slice. |
| FTE | float | NO | 4.3 (max=35.8) | Total Full-Time Equivalent count. |
| AvgTenureMonths | float | NO | 34.2 (max=94.0) | Average tenure of employees in this slice. |

### 📊 FactEmployeeEvents
**Description:** The transactional record of every employee lifecycle event — specifically hires and terminations. Each row captures when an employee joined or left the organisation, and for terminations, how and why they left (voluntary vs involuntary, and the specific reason). This is the core table for attrition analysis, hire rate tracking, and understanding workforce flow (inflows vs outflows) over time. The termination reason breakdown (e.g., Company Restructuring, Compensation, Career Growth) directly supports root-cause analysis of employee exits.
**Grain:** One row per employee lifecycle event (hire or termination).
**Size:** 1,406 rows, 9 columns
**Scope:** 2018-01-05 to 2025-12-27.

| Column Name | Data Type | Nullable | Sample Values | Business Description |
| :--- | :--- | :--- | :--- | :--- |
| EmployeeID | str | NO | E100001 | Foreign key to DimEmployee. |
| EventDate | datetime | NO | 2018-01-05 | Date the event occurred. |
| EventType | str | NO | Hire(1200), Termination(206) | The lifecycle event classification. |
| DepartmentID | str | NO | D001 | Foreign key to DimDepartment. |
| Department | str | NO | Engineering | Denormalized department name. |
| LocationID | str | NO | L001 | Foreign key to DimLocation. |
| Location | str | NO | Riga | Denormalized location name. |
| TerminationType | str | YES | Involuntary(125), Voluntary(81) | Applies only to termination events (null for hires). |
| TerminationReason | str | YES | Company Restructuring(95), Compensation(22) | Specific reason for termination events (null for hires). |

### 📊 FactCompensation
**Description:** A historical log of every salary record for each employee, updated at each annual compensation review. Tracks how individual salaries have evolved over time, and what each employee earned at their seniority level. Supports compensation benchmarking by level, identification of salary compression (where pay bands between adjacent levels overlap), and analysis of whether compensation is a driver of voluntary attrition. All values are in EUR — no currency conversion is required. Average ~2.4 records per employee reflect the 3-year reporting window.
**Grain:** One row per employee per salary review date.
**Size:** 2,914 rows, 5 columns

| Column Name | Data Type | Nullable | Sample Values | Business Description |
| :--- | :--- | :--- | :--- | :--- |
| EmployeeID | str | NO | E100001 | Foreign key to DimEmployee. |
| EffectiveDate | datetime | NO | 2023-04-01 | Date the compensation became effective. |
| AnnualSalary | int | NO | 81495 (min=29261, max=206254) | The annualized salary amount. |
| Level | str | NO | L1, L2, L3 | Denormalized employee level at time of review. |
| Currency | str | NO | EUR | The currency of the salary. |

### 📊 FactPerformance
**Description:** Records the outcome of each employee's annual performance review using a 1–5 rating scale. Provides the data needed to understand the distribution of talent across the workforce, identify high performers (ratings 4–5) and low performers (ratings 1–2), and correlate performance outcomes with attrition. For example, it enables analysis of whether involuntary exits are concentrated among low performers, or whether high performers are leaving voluntarily due to limited career growth. Only Annual Reviews are present in this dataset.
**Grain:** One row per employee per annual review.
**Size:** 2,741 rows, 4 columns
**Scope:** 2023-12-15 to 2025-12-15 (Annual Reviews only).

| Column Name | Data Type | Nullable | Sample Values | Business Description |
| :--- | :--- | :--- | :--- | :--- |
| EmployeeID | str | NO | E100001 | Foreign key to DimEmployee. |
| ReviewDate | datetime | NO | 2023-12-15 | The date of the performance review. |
| PerformanceRating | int | NO | 1-5 (3=914, 4=874) | Numeric performance rating (1 to 5 scale). |
| ReviewType | str | NO | Annual Review | Type of performance review. |

### 📊 FactEngagement
**Description:** Stores the results of quarterly employee engagement surveys, capturing how connected, motivated, and satisfied employees feel at the time of each survey. With ~8–9 responses per employee over the 36-month period, it provides a rich longitudinal signal of employee sentiment. Engagement scores (1–5) are a key leading indicator of voluntary attrition risk — employees who score consistently low are more likely to resign. The response rate column reflects survey participation levels, which can signal cultural or managerial issues when low. This table is most powerful when joined to FactEmployeeEvents to validate the engagement-to-attrition pipeline.
**Grain:** One row per employee per quarterly survey response.
**Size:** 10,200 rows, 4 columns

| Column Name | Data Type | Nullable | Sample Values | Business Description |
| :--- | :--- | :--- | :--- | :--- |
| EmployeeID | str | NO | E100001 | Foreign key to DimEmployee. |
| SurveyDate | datetime | NO | 2023-03-31 | Date the survey was conducted/submitted. |
| EngagementScore | int | NO | 1-5 (mean=3.55, 4=3383) | The core engagement score provided by the employee. |
| ResponseRatePct | int | NO | 80 (min=65, max=95) | Overall response rate for the survey grouping. |

### 📊 FactAbsence
**Description:** Logs every recorded employee absence episode, capturing the type of absence (Sick Leave or Unpaid Leave) and the number of days missed. Absence patterns are an important proxy for employee wellbeing and organisational health — high or frequent sick leave can signal burnout, disengagement, or poor management. When cross-referenced with FactEngagement and FactEmployeeEvents, absence frequency can serve as a compounding attrition risk signal. Also supports workforce planning by quantifying the volume of productive days lost across departments and locations.
**Grain:** One row per employee absence event.
**Size:** 2,755 rows, 4 columns

| Column Name | Data Type | Nullable | Sample Values | Business Description |
| :--- | :--- | :--- | :--- | :--- |
| EmployeeID | str | NO | E100001 | Foreign key to DimEmployee. |
| AbsenceDate | datetime | NO | 2023-06-15 | The date the absence started. |
| AbsenceType | str | NO | Sick Leave(2517), Unpaid Leave(238) | The reason or category of the absence. |
| DaysAbsent | int | NO | 4 (mean=4.8, min=1, max=14) | Number of days absent for the event. |

### 📊 FactRecruitment
**Description:** A monthly pipeline summary of recruitment activity by department, tracking open, filled, and cancelled job requisitions alongside the average time taken to fill a role. This table reveals how effectively the organisation is replenishing its workforce in response to attrition and growth demands. An overall fill rate of ~50% across the dataset signals significant recruitment inefficiency or high demand volatility — particularly relevant against the backdrop of the restructuring event. Best analysed alongside WorkforceTargets (to assess whether recruitment is closing the gap to planned headcount) and FactEmployeeEvents (to understand how hire rates relate to termination volumes).
**Grain:** One row per month × department combination.
**Size:** 288 rows, 8 columns
**Scope:** 2023-01-01 to 2025-12-01 (36 months × 8 departments). Overall fill rate: ~50.3%.

| Column Name | Data Type | Nullable | Sample Values | Business Description |
| :--- | :--- | :--- | :--- | :--- |
| ReqID | str | NO | R001 | Unique recruitment record ID (primary key). |
| Month | datetime | NO | 2023-01-01 | Foreign key to DimDate. |
| DepartmentID | str | NO | D001 | Foreign key to DimDepartment. |
| Department | str | NO | Engineering | Denormalized department name. |
| OpenRequisitions | int | NO | 5 (max=16) | Number of open job requisitions. |
| FilledRequisitions | int | NO | 3 (max=13) | Number of successfully filled job requisitions. |
| CancelledRequisitions | int | NO | 1 (max=9) | Number of cancelled job requisitions. |
| AvgDaysToFill | int | NO | 48 (min=28, max=73) | Average days taken to fill the open roles. |

---

## Planning

### 🎯 WorkforceTargets
**Description:** Contains the official HR workforce plan, defining the approved headcount, FTE, and salary budget targets for each department on a monthly basis. Represents what the business intended — as opposed to FactMonthlySnapshot, which represents what actually happened. Two planning scenarios are present: the standard "Baseline workforce plan" (264 records) and a "Post-restructuring reset" (24 records) that reflects revised targets following the company restructuring event. Comparing this table against FactMonthlySnapshot is the primary way to surface over/understaffing variances, and against FactCompensation to identify salary cost overruns or underspend.
**Grain:** One row per month × department combination.
**Size:** 288 rows, 7 columns

| Column Name | Data Type | Nullable | Sample Values | Business Description |
| :--- | :--- | :--- | :--- | :--- |
| Month | datetime | NO | 2023-01-01 | Foreign key to DimDate. |
| DepartmentID | str | NO | D001 | Foreign key to DimDepartment. |
| Department | str | NO | Engineering | Denormalized department name. |
| PlannedHeadcount | int | NO | 133 (max=350) | The targeted/planned headcount. |
| BudgetedFTE | float | NO | 130.5 (max=343) | The budgeted Full-Time Equivalent count. |
| BudgetedSalaryCost | int | NO | 8700000 (max=25.2M) | Budgeted monetary cost for salaries (in EUR). |
| PlanningScenario | str | NO | Baseline workforce plan(264), Post-restructuring reset(24) | The context/scenario for these targets. |

---

## Key Relationships

The following mappings outline the referential integrity across the star schema model:

1. **`DimEmployee` Relationships**
   - `DimEmployee.DepartmentID` -> `DimDepartment.DepartmentID`
   - `DimEmployee.LocationID` -> `DimLocation.LocationID`

2. **`FactMonthlySnapshot` Relationships**
   - `FactMonthlySnapshot.Month` -> `DimDate.Date` (or `DimDate.Month`)
   - `FactMonthlySnapshot.DepartmentID` -> `DimDepartment.DepartmentID`
   - `FactMonthlySnapshot.LocationID` -> `DimLocation.LocationID`

3. **`FactEmployeeEvents` Relationships**
   - `FactEmployeeEvents.EmployeeID` -> `DimEmployee.EmployeeID`
   - `FactEmployeeEvents.DepartmentID` -> `DimDepartment.DepartmentID`
   - `FactEmployeeEvents.LocationID` -> `DimLocation.LocationID`

4. **`FactCompensation` Relationships**
   - `FactCompensation.EmployeeID` -> `DimEmployee.EmployeeID`

5. **`FactPerformance` Relationships**
   - `FactPerformance.EmployeeID` -> `DimEmployee.EmployeeID`

6. **`FactEngagement` Relationships**
   - `FactEngagement.EmployeeID` -> `DimEmployee.EmployeeID`

7. **`FactAbsence` Relationships**
   - `FactAbsence.EmployeeID` -> `DimEmployee.EmployeeID`

8. **`FactRecruitment` Relationships**
   - `FactRecruitment.Month` -> `DimDate.Date` (or `DimDate.Month`)
   - `FactRecruitment.DepartmentID` -> `DimDepartment.DepartmentID`

9. **`WorkforceTargets` Relationships**
   - `WorkforceTargets.Month` -> `DimDate.Date` (or `DimDate.Month`)
   - `WorkforceTargets.DepartmentID` -> `DimDepartment.DepartmentID`
