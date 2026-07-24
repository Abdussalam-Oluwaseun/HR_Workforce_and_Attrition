# Exploratory Data Analysis: HR Workforce Planning & Attrition

> [!NOTE]
> This Exploratory Data Analysis (EDA) report provides a comprehensive overview of the HR dataset covering the period from 2018 through 2025. The insights highlight workforce composition, attrition trends, compensation, performance, engagement, absence, and recruitment metrics, providing a foundation for the Power BI report model.

## Dataset Overview

The foundation of our analytics is built upon a robust dataset tracking the entire employee lifecycle.

| Metric | Value |
| --- | --- |
| Total Tables | 12 (3 dimension, 1 date, 6 fact, 1 planning) |
| Total Records | ~30,000+ |
| Coverage Period | 2018 (hire events) through end of 2025 |
| Reporting Period | Jan 2023 to Dec 2025 (36 months) |
| Currency | All monetary values in EUR |
| Total Employees | 1,200 tracked (994 active, 206 terminated) |

*Key Findings:*
- The dataset is structured ideally for a star-schema model, separating dimensions from facts.
- The 36-month reporting window provides sufficient depth for trend analysis and historical context.

---

## 1. Workforce Composition

### Headcount by Department (Dec 2025 Latest Snapshot)

| Department | Headcount | Proportion |
| --- | --- | --- |
| Engineering | 346 | 34.6% |
| Sales | 184 | 18.4% |
| Customer Success | 138 | 13.8% |
| Operations | 100 | 10.0% |
| Marketing | 84 | 8.4% |
| HR | 56 | 5.6% |
| Data & Analytics | 51 | 5.1% |
| Finance | 40 | 4.0% |
| **Total** | **999** | **100%** |


### Employee Level Distribution

| Level | Headcount | Percentage |
| --- | --- | --- |
| L1 | 164 | 13.7% |
| L2 | 271 | 22.6% |
| L3 | 306 | 25.5% |
| L4 | 311 | 25.9% |
| L5 | 148 | 12.3% |


### Demographics and Location

| Category | Breakdown |
| --- | --- |
| **Gender** | Male: 589 (49.1%) \| Female: 583 (48.6%) \| Non-disclosed: 28 (2.3%) |
| **Age Band** | 20-29: 257 (21.4%) \| 30-39: 494 (41.2%) \| 40-49: 350 (29.2%) \| 50-59: 93 (7.8%) \| 60+: 6 (0.5%) |
| **Location** | Riga: 470 (39.2%) \| London: 239 (19.9%) \| Berlin: 167 (13.9%) \| New York: 166 (13.8%) \| Toronto: 87 (7.3%) \| Remote: 71 (5.9%) |
| **Tenure** | 0-6m: 119 (9.9%) \| 6-12m: 109 (9.1%) \| 1-2y: 207 (17.3%) \| 2-5y: 425 (35.4%) \| 5+y: 340 (28.3%) |

> [!TIP]
> The gender split is nearly equal, providing an excellent baseline for diversity and inclusion reporting without significant historical imbalance.

*Key Findings:*
- **Balanced Structure**: The organization maintains a healthy pyramid shape, slightly heavier in the mid-senior levels (L3/L4), accounting for over 50% of the workforce.
- **Geographic Concentration**: Riga is the dominant HQ. European offices account for 73%, while North American offices make up 21.1%.
- **Retention Base**: The majority of the workforce (63.7%) has 2+ years of tenure, indicating a solid retention base.
- **FTE Profile**: Mean FTE is 0.969. The vast majority are full-time, with part-time employees representing a small minority (minimum FTE = 0.6).

---

## 2. Attrition Analysis

### Overall Attrition Metrics

| Metric | Count | Percentage |
| --- | --- | --- |
| Total Employees Ever | 1,200 | 100% |
| Active Employees | 994 | 82.8% |
| Terminated Employees | 206 | 17.2% |

### Termination Type and Reasons

| Termination Type | Count | Percentage |
| --- | --- | --- |
| Involuntary | 125 | 60.7% |
| Voluntary | 81 | 39.3% |

| Termination Reason | Count | Percentage of Exits |
| --- | --- | --- |
| Company Restructuring | 95 | 46.1% |
| Compensation | 22 | 10.7% |
| Work-Life Balance | 21 | 10.2% |
| Career Growth | 19 | 9.2% |
| Performance | 18 | 8.7% |
| Relocation | 13 | 6.3% |
| Role Eliminated | 12 | 5.8% |
| Manager Fit | 6 | 2.9% |


> [!IMPORTANT]
> The high volume of involuntary terminations is heavily skewed by a significant restructuring event. Company Restructuring (95) and Role Eliminated (12) together account for 107 structural exits (51.9% of all terminations).

*Key Findings:*
- **Restructuring Impact**: The cumulative attrition rate is 17.2% (~5.7% annualized), but when excluding the restructuring event, the organic cumulative rate drops to ~8.3% (~2.8% annualized).
- **Voluntary Push Factors**: Among voluntary exits, Compensation (22), Work-Life Balance (21), and Career Growth (19) are the leading drivers.
- **Timeline**: All terminations occurred between 2023-02-23 and 2025-12-27, falling squarely within our 36-month reporting window.

---

## 3. Compensation Analysis

### Overall Salary Statistics

| Metric | Value (EUR) |
| --- | --- |
| Mean | €81,495 |
| Median | €76,489 |
| Minimum | €29,261 |
| Maximum | €206,254 |
| Standard Deviation | €32,622 |

### Salary by Level

| Level | Average Salary | Minimum | Maximum |
| --- | --- | --- | --- |
| L1 | €43,377 | €29,261 | €70,991 |
| L2 | €57,549 | €39,871 | €97,978 |
| L3 | €77,683 | €55,269 | €134,058 |
| L4 | €103,279 | €70,128 | €172,041 |
| L5 | €130,264 | €88,875 | €206,254 |


> [!WARNING]
> There is significant range overlap between employee levels (e.g., L3 max of €134k heavily overlaps with the L4 range, and L4 max overlaps with L5). This suggests potential salary compression issues that need monitoring.

*Key Findings:*
- **Simplicity in Currency**: All values are in EUR, eliminating foreign exchange complexities in the reporting model.
- **Review Cadence**: There are 2,914 records for 1,200 employees, averaging ~2.4 salary records per employee, aligning with standard annual review cycles.
- **Compression Risk**: The overlap in salary bands across levels could lead to dissatisfaction, tying back to "Compensation" being the top voluntary exit reason.

---

## 4. Performance Analysis

### Rating Distribution (Scale 1-5)

| Rating | Count | Percentage | Classification |
| --- | --- | --- | --- |
| 1 (Low) | 117 | 4.3% | Low Performer |
| 2 | 360 | 13.1% | Low Performer |
| 3 (Mid) | 914 | 33.3% | Core Performer |
| 4 | 874 | 31.9% | High Performer |
| 5 (High) | 476 | 17.4% | High Performer |

*Key Findings:*
- **Positive Skew**: The distribution is slightly right-skewed, with high performers (4+5) making up nearly half the workforce (49.3%).
- **Review Frequency**: 2,741 reviews across 1,200 employees yields ~2.3 reviews per employee over 3 years, confirming an annual review standard.

---

## 5. Engagement Analysis

### Engagement Score Distribution (Scale 1-5)

| Score | Count | Percentage | Classification |
| --- | --- | --- | --- |
| 1 | 503 | 4.9% | Disengaged |
| 2 | 1,272 | 12.5% | Disengaged |
| 3 | 2,780 | 27.3% | Neutral |
| 4 | 3,383 | 33.2% | Highly Engaged |
| 5 | 2,262 | 22.2% | Highly Engaged |


*Key Findings:*
- **Overall Sentiment**: The mean score is 3.55 (median: 4, standard deviation: 1.11). Highly engaged responses represent 55.4% of total surveys.
- **Survey Cadence**: With 10,200 rows, employees average ~8.5 surveys over 36 months, confirming a quarterly survey cadence.
- **Participation**: The response rate is exceptionally healthy (Mean: 80.2%, Min: 65%, Max: 95%).

---

## 6. Absence Analysis

### Absence Metrics

| Metric | Value |
| --- | --- |
| Total Events | 2,755 |
| Sick Leave | 2,517 (91.4%) |
| Unpaid Leave | 238 (8.6%) |
| Mean Days Absent | 4.8 days |
| Median Days Absent | 4 days |
| Max Days Absent | 14 days |

*Key Findings:*
- **Frequency**: There are roughly ~2.3 absence events per employee over the 3-year period.
- **Total Impact**: An estimated 13,224 days were lost over the reporting period, which could have measurable impacts on productivity.
- **Type Distribution**: Sick leave overwhelmingly dominates the absence records.

---

## 7. Recruitment Analysis

### Recruitment Pipeline Metrics

| Metric | Value |
| --- | --- |
| Avg Open Reqs per Month (per dept) | 5.5 |
| Avg Filled Reqs | 3.1 |
| Avg Cancelled Reqs | 1.2 |
| Overall Fill Rate | 50.3% |
| Avg Days to Fill | 48.3 days (Range: 28-73) |

> [!CAUTION]
> The 50.3% fill rate is a critical red flag. Filling only half of the open roles each month signals severe pipeline constraints, demand volatility, or non-competitive offers. Furthermore, the high cancellation rate is likely linked to the instability of the restructuring period.

*Key Findings:*
- **Time to Hire**: Averaging 48.3 days, the time-to-fill metric shows reasonable speed but fails to capture the high volume of roles that remain unfilled or get cancelled.
- **Instability**: The data clearly shows recruitment taking a hit during the restructuring phase.

---

## 8. Workforce Planning vs Actuals

### Planning vs Budget Metrics

| Metric | Mean Value |
| --- | --- |
| Planned Headcount (Monthly) | 133.7 |
| Budgeted FTE | 130.5 |
| Budgeted Salary Cost | €8.7M |

*Key Findings:*
- **Scenario Shifts**: Out of 288 planning records, 264 (91.7%) represent the baseline plan, while 24 records (8.3%) correspond to a "Post-restructuring reset".
- **Tracking Gaps**: Comparing snapshot headcount against planned headcount reveals departmental over/understaffing. For instance, Engineering planned for 310 in Jan 2023 but ended with 346 in Dec 2025, indicating growth beyond the initial plan.

---

## 9. Data Quality Summary

### Null Value Analysis

| Field | Null Count | Reason | Quality Status |
| --- | --- | --- | --- |
| `DimEmployee.TerminationDate` | 994 | Active employees only | Intentional (By Design) |
| `DimEmployee.TerminationType` | 994 | Active employees only | Intentional (By Design) |
| `DimEmployee.TerminationReason` | 994 | Active employees only | Intentional (By Design) |
| `FactEmployeeEvents.TerminationType`| 1,200 | Null for Hire events | Intentional (By Design) |
| `FactEmployeeEvents.TerminationReason`| 1,200| Null for Hire events | Intentional (By Design) |

> [!NOTE]
> All other columns across all 12 tables have zero unexpected null values. Location IDs, Department IDs, and Employee IDs all match consistent formatting patterns. The `DimDate` table completely and accurately covers the 2023-2025 period. The overall data quality is excellent and ready for modeling.

---

## 10. Top 10 Analytical Priorities for Power BI Report

Based on the Exploratory Data Analysis, the upcoming Power BI dashboard must incorporate the following analytical priorities to drive HR decision-making:

1. **Attrition Rate KPI**: Implement a rolling 12-month rate calculation (`Terminations / Avg Headcount`) to smooth out monthly volatility.
2. **Restructuring vs. Organic Split**: Visually separate the 107 structural exits from the 81 voluntary exits to provide a clean, accurate view of organic retention.
3. **Engagement-Attrition Correlation**: Join `FactEngagement` to `FactEmployeeEvents` to visualize if employees with low recent scores (1-2) tend to exit within the subsequent 6-12 months.
4. **Headcount vs. Target Gap Analysis**: Create variance measures comparing `FactMonthlySnapshot.Headcount` against `WorkforceTargets.PlannedHeadcount` by department.
5. **Salary Compression Tracking**: Highlight flight risk for high-earning L3 employees whose salaries heavily overlap with the L4/L5 bands.
6. **Recruitment Fill Rate Trend**: Visualize the month-over-month fill rate by department to isolate bottlenecks in talent acquisition.
7. **Absence Frequency as a Risk Proxy**: Flag employees with 3+ distinct absence events and cross-reference against engagement scores.
8. **Tenure at Exit Analysis**: Analyze whether early-tenure exits (0-12 months) are rising, which would signal potential onboarding or hiring alignment issues.
9. **Performance-Attrition Linkage**: Map performance ratings against termination types to see if low performers exit involuntarily and if the company is successfully retaining its high performers.
10. **Bradford Factor Analysis**: Implement a measure calculating `Absence Frequency × Duration` per employee to standardize absence impact scoring.
