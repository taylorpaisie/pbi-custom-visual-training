---
layout: default
title: Demo Data Schema
---

# Demo Data Schema

This page documents the sample datasets included in this training. You can use these for testing the Budget Traffic Light visual.

---

## Dataset 1: Vendor Budget Demo

**File:** `examples/data/budget_traffic_light_demo.csv`

### Overview

A simple vendor-level budget tracking dataset with 8 vendors and their burn rates.

**Use case:** Quick demo with minimal data

### Schema

| Column | Type | Description | Example |
|--------|------|-------------|---------|
| `Category` | Text | Vendor name or identifier | "Vendor A", "Vendor B" |
| `BurnRate` | Number | Expense ratio (Spent / Budget) | 0.45, 1.05 |

### Complete Data

```csv
Category,BurnRate
Vendor A,0.45
Vendor B,0.72
Vendor C,0.88
Vendor D,1.05
Vendor E,1.32
Vendor F,0.2
Vendor G,0.95
Vendor H,1.1
```

### Color Breakdown

- **Green (On Track):** 2 vendors (A, F)
- **Yellow (Watch):** 4 vendors (B, C, G, H)
- **Red (Over Budget):** 2 vendors (D, E)

### Notes

- Minimal dataset - good for quick testing
- Burn rates range from 0.2 to 1.32
- Easy to explain to beginners
- Works well for small presentations

---

## Dataset 2: Task/TPS Budget Demo

**File:** `examples/data/budget_traffic_light_tasks.csv`

### Overview

A more detailed project task tracking dataset with 8 tasks, including actual budget and expended amounts.

**Use case:** More realistic data with multiple columns

### Schema

| Column | Type | Description | Example |
|--------|------|-------------|---------|
| `Task` | Text | Task ID and description | "TPS-001 - System Design" |
| `Budget` | Number | Total budget allocated | 150000 |
| `Expended` | Number | Amount spent to date | 60000 |
| `BurnRate` | Number | Expense ratio (Expended / Budget) | 0.40 |

### Complete Data

```csv
Task,Budget,Expended,BurnRate
TPS-001 - System Design,150000,60000,0.4
TPS-002 - Modeling,200000,165000,0.83
TPS-003 - Testing,120000,135000,1.12
TPS-004 - Documentation,80000,30000,0.38
TPS-005 - PM Support,100000,95000,0.95
TPS-006 - Integration,180000,210000,1.17
TPS-007 - Training,90000,72000,0.8
TPS-008 - Sustainment,160000,155000,0.97
```

### Color Breakdown

- **Green (On Track):** 2 tasks (001, 004)
- **Yellow (Watch):** 4 tasks (002, 005, 007, 008)
- **Red (Over Budget):** 2 tasks (003, 006)

### Financial Summary

| Metric | Value |
|--------|-------|
| Total Budget | $1,080,000 |
| Total Expended | $1,032,000 |
| Overall Burn Rate | 0.96 |
| Tasks Over Budget | 2 |
| Tasks on Track | 2 |

### Notes

- More realistic for enterprise scenarios
- Shows how calculations work (Expended / Budget)
- Good for discussing financial management
- Task descriptions help with context
- Total project is at 96% spend (Yellow status overall)

---

## How to use these datasets in Power BI

### Option A: CSV Files (Recommended for this training)

1. In Power BI, go to **Home** → **Get Data**
2. Select **Text/CSV**
3. Navigate to the CSV file location
4. Select the file and click **Load**
5. Power BI auto-detects the schema

### Option B: Excel Spreadsheet

1. Open the CSV file in Excel
2. Save as `.xlsx` (Excel format)
3. In Power BI, go to **Home** → **Get Data** → **Excel**
4. Select the Excel file

### Option C: Manual Entry

If you want to test with different values:

1. In Power BI, go to **Home** → **Enter Data**
2. Manually enter a table
3. Click **Load**

---

## Creating your own test datasets

The Budget Traffic Light visual expects two columns:

1. **Category** (text) - What you're tracking (vendors, tasks, departments, etc.)
2. **BurnRate** (number) - A numeric value between 0 and 2 (or higher)

### Template CSV

```csv
Category,BurnRate
Item 1,0.3
Item 2,0.6
Item 3,0.9
Item 4,1.2
Item 5,1.5
```

Color mapping:
- < 0.8 → Green
- 0.8 to 1.0 → Yellow
- > 1.0 → Red

### Example: Department Efficiency

```csv
Department,Efficiency
Engineering,0.72
Sales,1.05
Marketing,0.88
Operations,1.15
HR,0.45
```

Here, "efficiency" is the ratio of actual work completed to planned work. Values < 1.0 mean ahead of schedule.

### Example: Project Health

```csv
Project,HealthScore
ProjectAlpha,0.65
ProjectBeta,0.92
ProjectGamma,1.08
ProjectDelta,1.45
```

Health score could be: Risk Level / Risk Tolerance. 1.0 = at threshold.

---

## Data Validation Tips

Before testing with your own data:

1. **Check for NULL values** - The visual skips rows with missing data
2. **Verify numeric column** - BurnRate should be a number, not text
3. **Check column names** - They must match your Power BI mappings exactly
4. **Ensure categories are unique** - Duplicate category names will create duplicate cards
5. **Test with a small subset first** - Try 5-10 rows before loading 10,000 rows

---

## Troubleshooting data issues

### Cards show wrong data

**Check:**
1. Are columns mapped correctly in Power BI?
2. Are category/value columns selected in the visual?
3. Is the data sorted correctly in the source?

### Visual shows no data

**Check:**
1. CSV file has headers in the first row
2. No blank columns or rows
3. BurnRate column contains numbers (not text like "0.5%" or "$0.5")
4. Category column contains actual values (not blank)

### Some cards missing

**Check:**
1. Are all rows filled in? (No blank rows)
2. Is there a sort or filter in Power BI hiding rows?
3. Is the data view truncated in Power BI? (Check the filter pane)

---

## Real-world data sources

The Budget Traffic Light visual works well with data from:

- **Excel** - Expense reports, budget trackers
- **SQL Server** - Project management tables, cost tracking tables
- **Dataverse** - Dynamics 365 project entities
- **SharePoint** - Budget lists
- **CSV files** - Any exported data

The key requirement: You need a **category column** and a **numeric measure column** (burn rate, efficiency, health score, etc.).

---

## Questions?

If you need help with your data:

1. Check the **Lesson 03** troubleshooting section
2. Review the complete data samples above
3. Make sure your columns are named exactly as expected
4. Test with one of the provided CSV files first

Happy building!
