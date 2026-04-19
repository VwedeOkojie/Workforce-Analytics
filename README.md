# 👥 Workforce Analytics — Power BI & Excel (Power Query)

## Project Overview

This project demonstrates a full workforce analytics workflow — from raw data cleaning in Excel Power Query to interactive dashboard development in Power BI. The objective was to clean a dirty workforce transactions dataset, build a three-table data model, write DAX measures, and deliver executive-ready insights across three dashboard pages.

The dataset covers 238 workforce transactions across four departments (Production Line A, Production Line B, Packaging & Distribution, and Quality Assurance) spanning January to March 2025.

---

## 🔧 Technologies Used

- **Excel (Power Query)** — data cleaning and transformation
- **Power BI Desktop** — data modelling, DAX measures, and dashboard visualisation
- **DAX** — calculated measures for workforce KPIs

---

## 📁 Project Structure

| File | Description |
|---|---|
| `workforce_transactions_dirty.csv` | Raw dirty workforce transactions dataset (240 rows) |
| `employees_clean.csv` | Clean employee master reference table (44 rows) |
| `departments_clean.csv` | Clean department reference table (4 rows) |
| `workforce_analytics.pbix` | Power BI dashboard file |
| `README.md` | Project overview and findings |

---

## ⚙️ Data Cleaning (Excel Power Query)

The following issues were identified and resolved in the workforce_transactions dataset:

| Issue | Detail | Fix Applied |
|---|---|---|
| Duplicate record | E001 appeared twice on 2025-01-10 | Removed using Remove Duplicates on employee_id + date combination |
| Missing employee ID | Record R101 had no employee_id | Removed — cannot be attributed to a workforce member |
| Outlier shift hours | E004 showed 23 actual hours on 2025-01-08 | Replaced with scheduled hours (8) — clock-out error |
| Late arrival data type | late_arrival_minutes column contained "No" text values | Replaced with 0, changed column to Decimal Number type |
| Data types | date, actual_hours, scheduled_hours, overtime_hours columns | Corrected to Date and Decimal Number types respectively |

**Final clean row count: 238 rows**

---

## 🗂️ Data Model

Three tables connected in Power BI:

- **workforce_transactions** → **employees_clean** on `employee_id` (many to one)
- **workforce_transactions** → **departments_clean** on `department_id` (many to one)
- **Date Table** (DAX generated) → **workforce_transactions** on `date` (one to many)

---

## 📐 DAX Measures

All measures stored in a dedicated Measures table:

```
Total Scheduled Hours = SUM(workforce_transactions_dirty[scheduled_hours])

Total Actual Hours = SUM(workforce_transactions_dirty[actual_hours])

Hours Variance = [Total Actual Hours] - [Total Scheduled Hours]

Total Overtime Hours = SUM(workforce_transactions_dirty[overtime_hours])

Unapproved Overtime Hours = 
CALCULATE(
    SUM(workforce_transactions_dirty[overtime_hours]),
    workforce_transactions_dirty[overtime_approved] = "No"
)

Absenteeism Rate % = 
DIVIDE(
    COUNTROWS(FILTER(workforce_transactions_dirty, workforce_transactions_dirty[absence_type] <> "")),
    COUNTROWS(workforce_transactions_dirty),
    0
) * 100

Late Arrival Count = 
CALCULATE(
    COUNTROWS(workforce_transactions_dirty),
    workforce_transactions_dirty[late_arrival_minutes] > 0
)

Headcount = 
CALCULATE(
    DISTINCTCOUNT(employees_clean[employee_id]),
    employees_clean[status] = "Active"
)

Disciplinary Incidents = 
CALCULATE(
    COUNTROWS(workforce_transactions_dirty),
    workforce_transactions_dirty[disciplinary_flag] = "Yes"
)

Grievance Count = 
CALCULATE(
    COUNTROWS(workforce_transactions_dirty),
    workforce_transactions_dirty[grievance_flag] = "Yes"
)
```

---

## 📊 Dashboard Pages

### Page 1 — Workforce Overview
KPI cards: Total Scheduled Hours, Total Actual Hours, Hours Variance, Absenteeism Rate %, Headcount

Visuals:
- Clustered bar chart: Scheduled vs Actual Hours by Department
- Month slicer for period filtering

### Page 2 — Compliance
KPI cards: Total Overtime Hours, Unapproved Overtime Hours, Late Arrival Count

Visuals:
- Bar chart: Unapproved Overtime Hours by Employee
- Bar chart: Late Arrival Minutes by Employee

### Page 3 — Employee Relations
KPI cards: Disciplinary Incidents, Grievance Count, Late Arrival Count, Headcount

Visuals:
- Bar chart: Late Arrival Minutes by Employee
- Table: Disciplinary Incidents by Employee and Department

---

## 📈 Key Findings

**Workforce Hours**
<img width="1155" height="651" alt="image" src="https://github.com/user-attachments/assets/029deaa1-231a-43a7-9f05-4d8252d6b873" />

- 1,888 hours scheduled vs 1,805 actual — a variance of -83.5 hours driven by absenteeism
- Production Line A has the highest workforce hours across all departments
- All four departments show actual hours below scheduled — absenteeism is a cross-departmental issue

**Compliance**
<img width="1150" height="648" alt="image" src="https://github.com/user-attachments/assets/f077c7d5-0342-4ac9-8e23-433d315e9262" />

- 34.5 total overtime hours recorded across the period
- 14.5 hours (42%) of overtime was unapproved — a significant compliance risk
- Tom Wallace has the highest unapproved overtime at 2.5 hours — requires manager follow-up
- 14 late arrival incidents recorded — Marcus Lee has the highest accumulated late minutes

**Employee Relations**
<img width="1128" height="561" alt="image" src="https://github.com/user-attachments/assets/0f21fea7-a686-4543-a184-558ca0aae834" />

- 9 disciplinary incidents across the period
- Production Line A and Quality Assurance have the most incidents
- Nathan Brooks, Oluwaseun Adeyemi, and Zoe Adams each recorded 2 incidents
- 1 grievance filed — relatively low for the period
---

## 💡 Business Recommendations

1. **Address unapproved overtime immediately** — 42% of overtime is unapproved, creating labour cost and compliance exposure. Implement manager approval workflows before shifts are extended.

2. **Investigate absenteeism root cause** — 6.3% absenteeism rate exceeds the typical 3-5% benchmark. A department-level breakdown would identify whether this is concentrated or spread across teams.

3. **Monitor repeat disciplinary incidents** — three employees have 2 incidents each within a 3-month period. Early intervention through coaching or performance plans is recommended before escalation.

4. **Review punctuality in high late-arrival employees** — Marcus Lee's accumulated late minutes suggest a pattern worth addressing in a development conversation.

---

## About

Built as a portfolio project to demonstrate workforce analytics capability including data cleaning, data modelling, DAX measure development, and executive dashboard design — directly applicable to People Analyst and HR Analytics roles.


