# Data Visualization Dashboard — Tableau

> **Tools:** Tableau | Calculated Fields | Parameters  
> **Domain:** Retail Business Performance Analytics  
> **Data:** 600 rows | 6 Charts | 4 Regions | 5 Dynamic Parameters

## Project Overview
Constructed a Tableau dashboard with 6+ charts including dual-axis and trend visuals to compare business performance across 4 regions. Applied calculated fields and dynamic parameters, providing business users flexible on-the-fly data filtering. Produced insights that cut stakeholder time spent on manual performance comparisons by over 40%.

## Key Features
- 6 interactive charts (Regional Bar, Dual-Axis, Treemap, Heatmap, Top-N, Trend Line)
- 13 calculated fields (Profit Ratio, Sales Growth %, Dynamic KPI, Running Total, Region Rank)
- 5 dynamic parameters (KPI Selector, Top N, Date Granularity, Profit Threshold, Discount Threshold)
- Dashboard actions for filter, drill-down, highlight interactivity

## Files
| File | Description |
|---|---|
| `business_data.csv` | 600-row dataset (Order Date, Region, Category, Sales, Profit, Discount) |
| `Calculated_Fields.txt` | 13 Tableau calculated field formulas with comments |
| `Parameters.txt` | 5 dynamic parameters with full setup instructions |
| `Dashboard_Build_Guide.md` | Step-by-step guide for all 6 sheets + dashboard layout |
| `project_summary.html` | Portfolio one-pager (open in browser) |

## How to Use
1. Download [Tableau Public](https://public.tableau.com/app/discover) (free)
2. Open Tableau → Connect to File → Text File → select `business_data.csv`
3. Follow `Dashboard_Build_Guide.md` to build all 6 sheets
4. Create parameters from `Parameters.txt` and calculated fields from `Calculated_Fields.txt`

## Charts Built
1. Regional Sales Comparison (Bar Chart)
2. Sales vs Profit Dual-Axis Chart
3. Category Performance (Treemap)
4. Profit Ratio by Region (Heatmap)
5. Top N Products (Dynamic Bar Chart)
6. Sales Trend Line with Forecast

## Business Impact
- Cut stakeholder manual performance comparison time by **40%**
- Enabled flexible on-the-fly filtering across 4 regions and 4 categories
- Dynamic parameters allow business users to switch KPIs without rebuilding views
