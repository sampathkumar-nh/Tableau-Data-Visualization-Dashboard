# Tableau Dashboard Build Guide
## Business Performance Dashboard — Retail Analytics (4 Regions)

> **Dataset:** `business_data.csv` | **Tableau Version:** 2022.x or later  
> **Sheets:** 6 | **Parameters:** 5 | **Calculated Fields:** 13  

---

## Table of Contents
1. [Connecting the Data Source](#1-connecting-the-data-source)
2. [Creating Parameters](#2-creating-parameters)
3. [Creating Calculated Fields](#3-creating-calculated-fields)
4. [Sheet 1 — Regional Sales Comparison (Bar Chart)](#4-sheet-1--regional-sales-comparison)
5. [Sheet 2 — Sales vs Profit Dual-Axis Chart](#5-sheet-2--sales-vs-profit-dual-axis-chart)
6. [Sheet 3 — Category Performance (Treemap)](#6-sheet-3--category-performance)
7. [Sheet 4 — Profit Ratio by Region (Heatmap)](#7-sheet-4--profit-ratio-by-region)
8. [Sheet 5 — Top N Products (Dynamic Bar Chart)](#8-sheet-5--top-n-products)
9. [Sheet 6 — Sales Trend Line with Forecast](#9-sheet-6--sales-trend-line-with-forecast)
10. [Building the Dashboard Layout](#10-building-the-dashboard-layout)
11. [Filters, Actions, and Interactivity](#11-filters-actions-and-interactivity)
12. [Final Formatting Checklist](#12-final-formatting-checklist)

---

## 1. Connecting the Data Source

1. Open Tableau Desktop → **Connect to a File → Text File**
2. Browse to `Tableau_Project/business_data.csv` → Open
3. In the Data Source canvas:
   - Confirm all column types are detected correctly:
     - `Order Date`, `Ship Date` → **Date**
     - `Sales`, `Profit`, `Discount`, `Quantity` → **Number (decimal / whole)**
     - All remaining columns → **String**
   - If `Order Date` was read as a string, click the column icon and change it to **Date**
4. Click **Sheet 1** in the bottom tab to begin building.

---

## 2. Creating Parameters

Create all five parameters before building sheets (refer to `Parameters.txt` for full details).

| # | Parameter Name           | Type    | Default | Range / List                    |
|---|--------------------------|---------|---------|----------------------------------|
| 1 | KPI Selector             | String  | Sales   | Sales / Profit / Quantity        |
| 2 | Top N Products           | Integer | 10      | 3 – 25                           |
| 3 | Date Granularity         | String  | Month   | Month / Quarter / Year           |
| 4 | Profit Ratio Threshold   | Float   | 0.15    | 0.00 – 0.50                      |
| 5 | Discount Threshold       | Float   | 0.20    | 0.00 – 0.50                      |

**To create:** Right-click the **Data pane** → **Create Parameter** → fill in the fields → OK.  
After creating each parameter, right-click it → **Show Parameter Control** to make it visible on the dashboard.

---

## 3. Creating Calculated Fields

Create these fields in order (some depend on parameters created above).

| # | Field Name                  | Type    | Notes                                     |
|---|-----------------------------|---------|-------------------------------------------|
| 1 | Profit Ratio                | Float   | `SUM([Profit]) / SUM([Sales])`            |
| 2 | Sales Growth %              | Float   | Table calc — LOOKUP offset -1             |
| 3 | Profit per Unit             | Float   | `SUM([Profit]) / SUM([Quantity])`         |
| 4 | Above Average Sales         | Boolean | `SUM([Sales]) > WINDOW_AVG(SUM([Sales]))` |
| 5 | Dynamic KPI Value           | Float   | IF/ELSEIF on KPI Selector parameter       |
| 6 | Dynamic KPI Label           | String  | Axis label switcher                       |
| 7 | Running Total Sales         | Float   | `RUNNING_SUM(SUM([Sales]))`               |
| 8 | Region Rank by Sales        | Integer | `RANK(SUM([Sales]), 'desc')`              |
| 9 | Discount Impact ($)         | Float   | Estimated revenue lost to discounts       |
|10 | Sales per Unit              | Float   | `SUM([Sales]) / SUM([Quantity])`          |
|11 | Top N Flag                  | Boolean | `RANK(SUM([Sales]),'desc') <= [Top N]`   |
|12 | Date Granularity Selector   | Date    | DATETRUNC based on Date Granularity param |
|13 | Profit Category             | String  | Row-level profit tier labeling            |

Full formulas are in `Calculated_Fields.txt`.

**To create:** Right-click the **Data pane** → **Create Calculated Field** → paste the formula → OK.

---

## 4. Sheet 1 — Regional Sales Comparison

**Chart Type:** Horizontal Bar Chart with segment color split  
**Business Question:** Which regions generate the most revenue, and how does segment mix differ?

### Steps:
1. Rename the sheet tab: **"Regional Sales"**
2. Drag `Region` → **Rows**
3. Drag `Sales` → **Columns** (it becomes `SUM(Sales)`)
4. Drag `Customer Segment` → **Color** (Marks card)
5. Sort: Click `SUM(Sales)` axis → **Sort Descending**
6. Drag `SUM(Sales)` → **Label** (Marks card) → format as currency `$#,##0`
7. **Add Reference Line:**
   - Right-click the Sales axis → **Add Reference Line**
   - Value: Average → Label: "Avg Sales" → Line style: dashed gray
8. **Add Dynamic KPI axis (optional upgrade):**
   - Replace `SUM(Sales)` with `[Dynamic KPI Value]` on Columns
   - This makes the bar chart respond to the KPI Selector parameter
9. Title: **"Regional Sales by Customer Segment"**
10. Color palette: **Tableau 10 Medium** (distinct colors per segment)

### Expected Result:
Four horizontal bars (East, North, West, South from top), each split into 3 colored segments (Consumer, Corporate, Home Office), sorted highest-to-lowest total sales.

---

## 5. Sheet 2 — Sales vs Profit Dual-Axis Chart

**Chart Type:** Dual-Axis Line + Bar Chart (time series)  
**Business Question:** How do revenue and profit trend together over time? Where do they diverge?

### Steps:
1. Rename sheet: **"Sales vs Profit Trend"**
2. Drag `[Date Granularity Selector]` → **Columns**
   - This uses the calculated field that responds to the Date Granularity parameter
   - Right-click the pill → **Exact Date** if prompted, choose Continuous
3. Drag `SUM(Sales)` → **Rows**
4. Drag `SUM(Profit)` → **Rows** (second pill, placed to the right)
5. Right-click `SUM(Profit)` pill → **Dual Axis**
6. Right-click the right axis → **Synchronize Axis** (optional — helps comparison)
7. On the **Marks card**:
   - Left axis (SUM Sales): change mark type → **Bar** → color: Steel Blue
   - Right axis (SUM Profit): change mark type → **Line** → color: Orange, Size: Medium
8. Drag `Region` → **Color** on the Line mark (to show profit by region overlay)
   - *Or* keep it as total and use Region in **Filters** for drill-down via dashboard action
9. Add **Trend Line:** Analysis menu → Trend Lines → Show Trend Lines (applies to line mark)
10. Title: **"Sales vs. Profit Trend — [Date Granularity] View"**
    - Insert the parameter value in the title: **Insert → Parameters → Date Granularity**

### Expected Result:
Blue bars show monthly/quarterly/yearly revenue, orange line tracks profit movement on a synchronized secondary axis. Allows instant identification of periods where revenue rose but margin compressed.

---

## 6. Sheet 3 — Category Performance

**Chart Type:** Treemap  
**Business Question:** Which categories and sub-categories contribute most to total revenue?

### Steps:
1. Rename sheet: **"Category Performance"**
2. Drag `Category` → **Rows** (or directly to the canvas)
3. Drag `Sub-Category` → **Detail** (Marks card)
4. Change Mark type: **Square** (this creates the treemap)
5. Drag `SUM(Sales)` → **Size** (Marks card)
6. Drag `SUM(Profit)` → **Color** (Marks card)
   - Edit color: **Red-Blue Diverging** (center at 0) — negative = red, positive = blue
7. Drag `Sub-Category` and `SUM(Sales)` → **Label**
8. Add **Category** as a filter on the dashboard for drill-down
9. Title: **"Category & Sub-Category Revenue Treemap"**

### Alternative (Bar Chart version):
- Rows: `Sub-Category` | Columns: `SUM(Sales)` | Color: `Category` | Sort: Descending

### Expected Result:
Rectangles sized by Sales, colored by Profit (green tones for high-margin, red for low-margin). Technology and Furniture typically dominate in size; users can spot margin risk at a glance.

---

## 7. Sheet 4 — Profit Ratio by Region

**Chart Type:** Highlight Table (Heatmap) — Region × Category matrix  
**Business Question:** Which region-category combinations have the best and worst margins?

### Steps:
1. Rename sheet: **"Profit Ratio Heatmap"**
2. Drag `Region` → **Columns**
3. Drag `Category` → **Rows**
4. Drag `[Profit Ratio]` → **Color** (Marks card)
   - Mark type: **Square**
   - Edit color: **Green-White-Red Diverging** (center: 0.15 = target margin)
5. Drag `[Profit Ratio]` → **Label** — format as Percentage (1 decimal)
6. **Add Reference Line / Target:**
   - Right-click color legend → **Edit Colors** → Advanced → set center to 0.15
     (matching the Profit Ratio Threshold parameter default)
7. **Connect Parameter:**
   - Create a calculated field "Below Target Margin":
     ```
     [Profit Ratio] < [Profit Ratio Threshold]
     ```
   - Drag to **Detail** — use in a tooltip to flag underperforming cells
8. Drag `Sub-Category` into **Rows** (below Category) to add one more level of drill-down
9. Title: **"Profit Ratio by Region & Category"**

### Expected Result:
4×4 grid (Region × Category) with color intensity showing margin health. Deep green = high-margin combos; red = loss/low-margin areas requiring attention.

---

## 8. Sheet 5 — Top N Products

**Chart Type:** Horizontal Bar Chart (dynamic, parameter-driven)  
**Business Question:** Which products are the top revenue generators right now?

### Steps:
1. Rename sheet: **"Top N Products"**
2. Drag `Product Name` → **Rows**
3. Drag `SUM(Sales)` → **Columns**
4. **Add Top N Filter:**
   - Drag `[Top N Flag]` → **Filters** card
   - Keep only **True**
   - (This table calc ranks products by SUM(Sales) and keeps top N from the parameter)
5. Sort: Descending by `SUM(Sales)`
6. Drag `SUM(Sales)` → **Label** — format as `$#,##0`
7. Drag `[Profit Ratio]` → **Color** — use Red-Green diverging palette
8. Drag `Region` → **Tooltip** (for context in tooltip)
9. Title: **"Top [Top N Products] Products by Sales"**
   - Insert parameter: Insert → Parameters → Top N Products in the title
10. Add the **Top N Products** slider parameter control to this sheet view

### Expected Result:
A dynamic bar chart (default: 10 bars) that instantly adjusts when the user changes the "Top N" slider. Products are sorted by sales and color-coded by margin.

---

## 9. Sheet 6 — Sales Trend Line with Forecast

**Chart Type:** Line Chart with Forecast  
**Business Question:** What is the sales trajectory, and what can we expect in the next period?

### Steps:
1. Rename sheet: **"Sales Forecast"**
2. Drag `Order Date` → **Columns** — set to **Month** (continuous green pill)
   - Or use `[Date Granularity Selector]` to make it parameter-driven
3. Drag `SUM(Sales)` → **Rows**
4. Drag `Region` → **Color** (4 colored lines, one per region)
5. Change mark type: **Line**, Size: Medium
6. **Add Forecast:**
   - Menu: **Analysis → Forecast → Show Forecast**
   - Right-click forecast area → **Forecast Options:**
     - Forecast Length: 3 months (or 1 quarter)
     - Prediction Interval: 95%
     - Forecast Model: Automatic
7. **Add Trend Lines:**
   - Menu: **Analysis → Trend Lines → Show Trend Lines**
   - Each region line will display its own trend (Linear by default)
8. **Add Running Total layer (optional):**
   - Duplicate the `SUM(Sales)` pill on Rows → change to Dual Axis
   - Right-click → **Add Table Calculation** → Running Total
   - Mark type: Area, color: light blue with 30% transparency
9. Title: **"Regional Sales Trend & Forecast"**

### Expected Result:
Four colored trend lines (North, South, East, West) extending into forecast territory with shaded confidence intervals. Users can toggle month/quarter/year via the Date Granularity parameter.

---

## 10. Building the Dashboard Layout

### Setup:
1. Click the **New Dashboard** icon (bottom tab row)
2. Rename it: **"Business Performance Dashboard"**
3. Set dashboard size: **Fixed → 1400 × 900 px** (standard widescreen)

### Layout Blueprint:

```
┌────────────────────────────────────────────────────────────────────────┐
│   HEADER: "Business Performance Dashboard — Retail Analytics"           │
│   Subtitle: Region | Segment | Date range filters                       │
├─────────────────────────────┬──────────────────────────────────────────┤
│  Sheet 1: Regional Sales    │  Sheet 2: Sales vs Profit Dual-Axis      │
│  (Horizontal Bar — 350×300) │  (Dual-Axis Line+Bar — 650×300)          │
├──────────────┬──────────────┼──────────────┬───────────────────────────┤
│ Sheet 3:     │ Sheet 4:     │ Sheet 5:     │ Sheet 6:                  │
│ Category     │ Profit Ratio │ Top N        │ Sales Trend +             │
│ Treemap      │ Heatmap      │ Products     │ Forecast                  │
│ (330×280)    │ (330×280)    │ (350×280)    │ (350×280)                 │
└──────────────┴──────────────┴──────────────┴───────────────────────────┘
│   PARAMETER CONTROLS (right panel or top strip):                        │
│   [KPI Selector] [Top N Slider] [Date Granularity] [Profit Threshold]  │
└────────────────────────────────────────────────────────────────────────┘
```

### Step-by-Step Assembly:
1. On the Dashboard canvas, drag **Sheets** (from left panel) into layout containers:
   - Row 1: Sheet 1 (left, 35% width) | Sheet 2 (right, 65% width)
   - Row 2: Sheet 3 | Sheet 4 | Sheet 5 | Sheet 6 (equal 25% each)
2. Drag **Text Object** to the top → type dashboard title
3. On the right side: drag **Vertical Container** → drag all 5 **Parameter Controls** into it
   - Or place parameter controls as a floating strip above the charts
4. Add a **Horizontal Container** at the very top for global filter controls (Region, Segment)

---

## 11. Filters, Actions, and Interactivity

### Global Filters (appear on all sheets):

| Filter Field      | Type          | Applies To      | Default        |
|-------------------|---------------|-----------------|----------------|
| Region            | Multi-select  | All worksheets  | All selected   |
| Customer Segment  | Multi-select  | All worksheets  | All selected   |
| Order Date        | Date Range    | All worksheets  | Full range     |
| Category          | Single-select | All worksheets  | All            |

To make a filter global: On any sheet, right-click the filter → **Apply to Worksheets → All Using This Data Source**

### Dashboard Actions:

#### Action 1 — Region Highlight (Filter Action)
- **Name:** Filter by Region
- **Source sheet:** Regional Sales (Sheet 1)
- **Target sheets:** All sheets
- **Trigger:** Select (click a bar)
- **Filter:** Region → Region
- **Clearing selection:** Show all values

#### Action 2 — Category Drill-Down (Filter Action)
- **Name:** Filter by Category
- **Source sheet:** Category Treemap (Sheet 3)
- **Target sheets:** Top N Products (Sheet 5), Profit Ratio Heatmap (Sheet 4)
- **Trigger:** Select
- **Filter:** Category → Category

#### Action 3 — Highlight Action (Cross-sheet Highlighting)
- **Name:** Highlight Segment
- **Source sheet:** Any sheet with Customer Segment
- **Target sheets:** All
- **Trigger:** Hover
- **Fields:** Customer Segment

#### Action 4 — URL Action (optional drill-through)
- **Name:** Open Order Detail
- **Trigger:** Menu (right-click)
- **URL:** Can link to an external order management system using `<Order ID>`

### To Add Actions:
Dashboard menu → **Actions** → **Add Action** → choose action type → configure source/target/fields → OK

---

## 12. Final Formatting Checklist

### Visual Consistency:
- [ ] Consistent font throughout: **Tableau Book** or **Segoe UI**, size 10–12 for labels
- [ ] Dashboard background: **#F8F9FA** (light gray) for container backgrounds
- [ ] Chart backgrounds: **White (#FFFFFF)**
- [ ] Borders: thin, **#E0E0E0** (light gray separator lines)
- [ ] Remove gridlines from bar charts; keep light gridlines on line charts

### Color Palette:
- Region colors: North = `#4E79A7`, South = `#F28E2B`, East = `#59A14F`, West = `#E15759`
- Profit diverging: **Red (#E15759) → White → Green (#59A14F)**
- KPI bars: **#4E79A7** (Tableau Blue)

### Labels & Formatting:
- [ ] Sales values: `$#,##0` (no decimals for large values)
- [ ] Profit Ratio: `0.0%` (one decimal percentage)
- [ ] Dates: `MMM YYYY` for monthly, `QN YYYY` for quarterly

### Titles:
- [ ] Each sheet has a descriptive title with parameter inserts where applicable
- [ ] Dashboard title: large, **bold**, dark text (#2C3E50)
- [ ] Subtitle shows the current date range (drag Order Date min/max into text object)

### Tooltips:
- [ ] Customize tooltips on every sheet to show: Region, Category, Sales ($), Profit ($), Profit Ratio (%), Quantity
- [ ] Tooltip example (Sheet 1): `Region: <Region> | Sales: <SUM(Sales)> | Profit: <SUM(Profit)> | Margin: <Profit Ratio>`

### Performance:
- [ ] Extract the data source (Data → Extract) for faster load times
- [ ] Remove unused fields from the extract to reduce file size

---

*Build time estimate: approximately 3–4 hours for a complete, polished implementation.*  
*This dashboard directly supports the resume bullet: "6+ charts with dual-axis and trend visuals comparing performance across 4 regions with dynamic parameters cutting stakeholder comparison time by 40%."*
