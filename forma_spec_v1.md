# Forma Format Specification

## Vision: Markdown for Tabular Data

Forma is designed to be the **"Markdown for spreadsheets"** - a lightweight, text-based format that bridges the gap between basic CSV and full spreadsheet applications. Just as Markdown made rich text formatting universally accessible, Forma aims to make spreadsheet functionality embeddable everywhere.

## The Missing Piece

Business productivity relies on two fundamental content types:

1. **Text content** : Solved by Markdown (ubiquitous, lightweight, embeddable)
2. **Tabular data** : Unsolved gap between CSV (too basic) and Excel/Sheets (too heavy)

Forma fills this gap by providing a declarative approach to tabular data visualization and interaction while maintaining the simplicity and embeddability that made Markdown successful.

## Design Characteristics

* **Declarative** : Separate data from presentation for maximum flexibility
* **Embeddable everywhere** : Like Markdown, designed for universal embedding
* **Progressive enhancement** : Start simple, add complexity only when needed
* **Human readable** : Plain text format suitable for version control
* **Business focused** : Covers 80% of spreadsheet use cases
* **Lightweight** : Minimal syntax overhead
* **AI friendly** : Easy for language models to generate and modify
* **Good enough** : Handles typical business scenarios without replacing Excel/Sheets

## Cross-Platform Portability

### Target Environments

* **Chat interfaces** : Inline spreadsheet editing within conversations
* **Documentation platforms** : Live calculations in wikis and knowledge bases
* **Note-taking apps** : Data that calculates and updates
* **Project management tools** : Budget and resource tracking
* **CRM systems** : Revenue projections and data analysis
* **Business applications** : Embedded tabular functionality

### The "Good Enough" Philosophy

Forma is designed to handle ~80% of spreadsheet use cases without requiring full Excel/Sheets:

**Covers well:**

* Basic calculations and formulas
* Simple charts and visualizations
* Data formatting and presentation
* Multi-table organization
* Import/export workflows

**Clear graduation path:**

* Start with Forma for embedded/collaborative scenarios
* Export to Excel when advanced features needed
* Import back to Forma for sharing and embedding

## File Extension

* **Extension** : `.forma`
* **MIME Type** : `text/forma` or `application/forma`

---

## Data Model Overview

Forma organizes content into three primary concepts:

### Tables

Data storage with optional type annotations, formulas, and validation. Tables can be inline (data embedded in the document) or external (referencing CSV or other data files).

### Views

Views define how data should be displayed. Views define layouts and can contain multiple components like tables, charts, and pivots. Each view renders as a separate tab or page.

### Extensions

Plugin system for custom functions, chart types, and rendering options that extend core functionality without bloating the base specification.

 **Key Architecture Principle** : Separation of data (Tables) from presentation (Views) enables one dataset to power multiple visualizations while keeping the syntax clean and maintainable.

---

## Document Structure & Comments

### Document Organization
Forma documents are organized using heading-style declarations:

- `# Meta` - Document metadata
- `# Table: <name>` - Table definitions
- `# Table: <name> external` - External table references  
- `# View: <name>` - View definitions
- `# Extension: <name>` - Extension declarations

### Comments
Any line beginning with `#` that doesn't match a predefined pattern is treated as a comment:

```text
# This is a comment
# TODO: Add Q4 data
# Last updated: 2024-03-15
# Data source: https://example.com/api

---

## Progressive Enhancement

Forma follows a progressive enhancement model that allows users to start simple and add complexity incrementally:

### Stage 1: Simple Data

```text
# Table: Sales
Month,Revenue
Jan,1000
Feb,1200
Mar,1500
```

Pure data storage. Automatically creates a default view showing the table.

### Stage 2: Add Types

```text
# Table: Sales
Month:text,Revenue:currency
Jan,1000
Feb,1200
Mar,1500
```

Type annotations enable proper formatting and validation.

### Stage 3: Add Formulas

```text
# Table: Sales
Month:text,Revenue:currency,Growth:percentage
Jan,1000,
Feb,1200,=(B3-B2)/B2
Mar,1500,=(B4-B3)/B3
Total,=SUM(B2:B4),
```

Calculations make data dynamic and self-updating.

### Stage 4: Add Visualization

```text
# Table: Sales
Month:text,Revenue:currency,Growth:percentage
Jan,1000,
Feb,1200,=(B3-B2)/B2
Mar,1500,=(B4-B3)/B3
Total,=SUM(B2:B4),

# View: Dashboard
## Chart type=bar dataset=Sales x=Month y=Revenue title="Monthly Revenue"
```

Charts provide visual representation of data.

### Stage 5: Custom Presentation

```text
# View: Dashboard
## TabularView dataset=Sales columns=Month,Revenue,Growth sort=Revenue:desc
### Columns
Month    align=left  width=120
Revenue  format=currency precision=0 align=right
Growth   format=percentage precision=1 align=center color=green

## Chart type=line dataset=Sales x=Month y=Revenue,Growth title="Revenue & Growth Trends"
```

Full control over formatting and multi-component layouts.

---

## Tables

Tables are the data layer of Forma. They store structured data with optional type annotations, formulas, and metadata.

### Inline Tables

```text
# Table: Customers
Name:text,Status:text,MRR:currency
Acme Corp,Active,12000
Beta Inc,Churned,0
Gamma LLC,Active,8500
```

Data is embedded directly in the document using CSV-compatible syntax.

### External Tables

```text
# Table: Orders external
src="./orders.csv"
delimiter=","
quote='"'
encoding="utf-8"
```

References external data files with configurable parsing options.

**Supported options:**

* `src`: File path or URL to data source
* `delimiter`: Field separator (default: `,`, alternatives: `|`, `\t`, `;`)
* `quote`: Quote character (default: `"`, alternative: ```)
* `encoding`: Text encoding (default: `utf-8`)
* `hasHeaders`: Whether first row contains headers (default: `true`)

### Column Types

Type annotations follow the pattern `ColumnName:Type` in the header row:

**Basic Types:**

* `text` - String values (default)
* `number` - Numeric values (integers, decimals)
* `currency` - Monetary values with locale-appropriate formatting
* `percentage` - Percentage values (0.1 = 10%)
* `date` - Date values (ISO 8601 preferred)
* `boolean` - True/false values
* `category` - Enumerated values with optional validation

**Category Type with Validation:**

```text
Status:category(Active,Inactive,Pending)
```

### Type Inference

When types are not explicitly specified, Forma analyzes cell values to infer appropriate types:

1. **Boolean** : `TRUE`, `FALSE` (case insensitive) → `boolean`
2. **Date** : ISO format `YYYY-MM-DD` or common formats → `date`
3. **Currency** : Values starting with currency symbols → `currency`
4. **Percentage** : Values ending with `%` → `percentage`
5. **Number** : Numeric values → `number`
6. **Text** : Everything else → `text` (default)

### Type Coercion

Forma performs automatic type coercion during operations, following Excel/Google Sheets conventions:

**Mathematical Operations:**

* Strings that represent numbers are automatically converted
* Empty values are treated as zero
* Invalid conversions result in `#VALUE!` error

**String Operations:**

* All values are converted to text for concatenation
* Null values become empty strings

### Formulas

Formulas use Excel-compatible syntax and begin with `=`:

```text
# Table: Budget
Category:text,Planned:currency,Actual:currency,Variance:currency
Housing,2000,1950,=C2-B2
Food,800,750,=C3-B3
Total,=SUM(B2:B3),=SUM(C2:C3),=SUM(D2:D3)
```

**Supported Functions:**

*Mathematical:*

* `SUM(range)`, `AVERAGE(range)`, `MIN(range)`, `MAX(range)`
* `COUNT(range)`, `COUNTA(range)`, `ABS(number)`, `ROUND(number, digits)`

*Logical:*

* `IF(condition, true_value, false_value)`
* `AND(condition1, condition2, ...)`, `OR(condition1, condition2, ...)`
* `NOT(condition)`

*Lookup:*

* `VLOOKUP(lookup_value, table_array, col_index, exact_match)`
* `INDEX(array, row_num)`, `MATCH(lookup_value, lookup_array, match_type)`

*Text:*

* `CONCATENATE(text1, text2, ...)`, `LEN(text)`
* `LEFT(text, num_chars)`, `RIGHT(text, num_chars)`, `MID(text, start, num_chars)`
* `UPPER(text)`, `LOWER(text)`

*Date:*

* `TODAY()`, `NOW()`, `YEAR(date)`, `MONTH(date)`, `DAY(date)`

### Formula Quoting

Formulas containing commas must be quoted to prevent CSV parsing conflicts:

```text
# Simple formulas (no commas) - quotes optional
A:number,B:number,Result:number
10,20,=A1+B1

# Complex formulas (with commas) - quotes required
A:number,B:number,C:number,Total:number
10,20,30,"=SUM(A1,B1,C1)"
```

### Cross-Table References

Reference data from other tables using table name prefixes:

```text
# Table: Products
Name:text,Price:currency
Widget A,25.00
Widget B,30.00

# Table: Orders
Product:text,Quantity:number,Total:currency
Widget A,10,"=VLOOKUP(A2,Products!A:B,2,FALSE)*B2"
Widget B,5,"=VLOOKUP(A3,Products!A:B,2,FALSE)*B3"
```

Table names with spaces require quotes: `'Product Catalog'!A:B`

### Error Handling

Invalid formulas and calculations display standard error codes:

* `#VALUE!` - Type conversion error
* `#REF!` - Invalid reference
* `#DIV/0!` - Division by zero
* `#NAME?` - Unknown function
* `#CIRCULAR!` - Circular reference

---

## Views

Views define how data should be presented and visualized. They separate presentation logic from data storage, enabling multiple visualizations of the same dataset.

### Default Views

When no views are explicitly defined, Forma automatically creates a default view with a TabularView for each table:

```text
# Table: Sales
Month,Revenue
Jan,1000
Feb,1200

# Automatically creates:
# View: Data
# ## TabularView dataset=Sales
```

Users see a clean table display without needing to understand the view concept.

### Explicit Views

Users can define custom views to control presentation:

```text
# View: Dashboard
## TabularView dataset=Sales columns=Month,Revenue sort=Revenue:desc
## Chart type=bar dataset=Sales x=Month y=Revenue title="Revenue Trend"
```

When explicit views exist, default views are not created.

### TabularView

Displays data in table format with extensive formatting options:

```text
# View: Overview
## TabularView dataset=Customers columns=Name,Status,MRR sort=MRR:desc filter=Status=="Active"
### Columns
Name   align=left  width=220 color=blue
Status badge map=Active:green|Paused:yellow|Churned:red
MRR    format=currency precision=0 align=right
```

**TabularView Options:**

* `dataset` - Source table (required)
* `columns` - Column selection and ordering
* `sort` - Sort specification (`column:asc` or `column:desc`)
* `filter` - Row filtering expression
* `limit` - Maximum rows to display

**Column Formatting:**

* `align` - Text alignment (`left`, `center`, `right`)
* `width` - Column width in pixels
* `format` - Data formatting (`currency`, `percentage`, `date`)
* `precision` - Decimal places for numbers
* `color` - Text color
* `bold`, `italic` - Typography options
* `badge` - Status badge with color mapping

### Charts

Visualize data using various chart types:

```text
## Chart type=bar dataset=Sales x=Month y=Revenue title="Monthly Sales"
```

**Chart Types:**

* `bar` - Vertical bar chart
* `column` - Horizontal bar chart
* `line` - Line chart
* `pie` - Pie chart
* `scatter` - Scatter plot

**Chart Properties:**

* `type` - Chart type (required)
* `dataset` - Source table (required)
* `title` - Chart title
* `x` - X-axis column
* `y` - Y-axis column(s), comma-separated for multiple series
* `values` - Values column (for pie charts)
* `labels` - Labels column (for pie charts)
* `series` - Custom series names for legend

**Multiple Series:**

```text
## Chart type=line dataset=Financial x=Month y=Revenue,Expenses,Profit 
       series="Monthly Revenue,Operating Expenses,Net Profit"
```

### Pivot Tables

Aggregate and cross-tabulate data:

```text
## Pivot dataset=Sales rows=Region cols=Quarter values=Revenue:sum totals=both
```

**Pivot Properties:**

* `dataset` - Source table (required)
* `rows` - Row grouping columns
* `cols` - Column grouping columns (optional)
* `values` - Aggregation specification (`column:function`)
* `totals` - Show totals (`rows`, `cols`, `both`, `none`)
* `filters` - Filter expression before aggregation

**Aggregation Functions:**

* `sum`, `average`, `count`, `min`, `max`

### Layout and Positioning

Control the arrangement of view components:

```text
# View: Dashboard
## Layout grid=12

## Chart type=pie dataset=Sales values=Revenue labels=Region title="Regional Sales"
## TabularView dataset=Sales columns=Region,Revenue position=right
## Chart type=line dataset=Sales x=Month y=Revenue title="Sales Trend"
```

**Position Options:**

* `position=bottom` - Below previous component (default)
* `position=right` - To the right of previous component

**Layout Properties:**

* `grid` - Grid system columns (12-column default)

---

## Extensions

Forma supports extensions to add custom functionality without bloating the core specification.

### Extension Points

#### Custom Functions

Add domain-specific functions to the formula engine:

```text
# Extension: financial-functions
# Adds: NPV(), IRR(), PMT(), PV(), FV()

Total:currency,Rate:percentage,NPV:currency
100000,0.1,"=NPV(B2, A2:A5)"
```

#### Custom Chart Types

Extend visualization capabilities:

```text
# Extension: advanced-charts
# Adds: gantt, treemap, heatmap, candlestick

## Chart type=gantt dataset=Projects start=StartDate end=EndDate title="Project Timeline"
## Chart type=heatmap dataset=Metrics x=Category y=Month values=Score
```

#### Custom Renderers

Alternative rendering approaches for specialized use cases:

```text
# Extension: specialized-renderers
# Adds: kanban, calendar, map

## TabularView dataset=Tasks renderer=kanban status=Status title=Title assignee=Owner
## TabularView dataset=Events renderer=calendar date=Date title=Event
```

#### Layout Extensions

Advanced positioning and responsive design:

```text
# Extension: advanced-layout
# Adds: flexbox, responsive breakpoints

## Layout type=flex direction=row wrap=true
## Layout responsive mobile="stack" tablet="grid-6" desktop="grid-4"
```

### Extension Syntax

Extensions use a consistent declaration pattern:

```text
# Extension: extension-name
# Adds: feature1, feature2, feature3

# Extension usage in content
## ComponentType extension:property=value dataset=Table
```

### Extension Discovery

Renderers should gracefully handle unknown extensions:

* Display warning for missing extensions
* Provide fallback rendering when possible
* Continue processing other components

### Popular Extensions (Examples)

**Data Processing:**

```text
# Extension: data-transforms
# Adds: groupBy, pivot, aggregate operations

# DerivedTable: MonthlySales from=Sales
groupBy=Month
aggregate=Revenue:sum,Orders:count
```

**Advanced Analytics:**

```text
# Extension: statistics
# Adds: CORREL(), REGRESSION(), TTEST(), STDEV()

Correlation:number
=CORREL(Sales!Revenue, Sales!Marketing)
```

**Business Intelligence:**

```text
# Extension: bi-charts
# Adds: waterfall, bullet, gauge charts

## Chart type=waterfall dataset=Cashflow x=Category y=Amount title="Cash Flow Analysis"
## Chart type=gauge dataset=KPIs value=Performance min=0 max=100 target=80
```

---

## Excel / Sheets Interoperability

Forma supports **export to** and **import from** Excel (and, indirectly, Google Sheets). The guiding principle is:

* **Data** round-trips at 100% fidelity
* **Formulas** are highly compatible (core Excel set)
* **Views/Presentation** export in useful but approximate form

---

### Interop Levels

* **Level A – Data (100%)**: rows, columns, types, values
* **Level B – Calculations (High)**: formulas, cross-table references, supported functions
* **Level C – Presentation (Partial)**: formatting, charts, pivots, layouts

---

### Export to Excel (Forma → .xlsx)

**Tables → Worksheets**

* Each `# Table: <Name>` → Excel worksheet `<Name>`
* Column types mapped to Excel formats (text, number, currency, percentage, date, boolean, category)
* Formulas exported as-is in Excel syntax
* Optionally create ListObjects (`tbl_<Name>`) for structured references

**Views → Worksheets**

* Each `# View: <Name>` → Excel worksheet `view_<Name>`
* `TabularView` → table copy with formatting, sort, filters
* `Chart` → Excel chart linked to source ranges
* `Pivot` → PivotTable (and optional PivotChart)
* `Layout` → approximated (stacked or side-by-side regions)

**Extensions**

* Non-Excel functions → static values with `#NAME?` warning
* Custom charts/renderers → static tables/charts if possible, else omitted with note

**Metadata**

* Hidden sheet `__FORMA_META` stores mappings, export version, warnings, and lossy conversions

---

### Import from Excel (xlsx → Forma)

**Tables**

* Use ListObjects where available; else infer from header row
* Map Excel formats to Forma types
* Validation lists → `category`
* Supported formulas preserved; unsupported functions → values + warning

**Views**

* Sheets prefixed `view_` → views
* Charts → `## Chart` components
* PivotTables → `## Pivot` specs
* Formatted tables → `## TabularView`

**Unknowns**

* Unmapped features → converted to comments in the document

---

### Loss Matrix

| Feature                        | Export | Import |
| ------------------------------ | :----: | :----: |
| Rows/columns/values            |   ✅   |   ✅   |
| Types & number formats         |   ✅   |   ✅   |
| Category validation            |   ✅   |   ✅   |
| Formulas (core Excel set)      |   ✅   |   ✅   |
| Cross-table references         |   ✅   |   ✅   |
| TabularView formatting         |   ◕   |   ◕   |
| Charts (bar/line/pie/scatter)  |   ✅   |   ✅   |
| Pivot tables                   |   ✅   |   ✅   |
| Layout grid/positioning        |   ◔   |   ◔   |
| Extensions (custom charts/fns) |   ◔   |   ◔   |

Legend: ✅ exact / ◕ partial / ◔ approximate

---

### Guardrails

* **Sheet names** limited to 31 chars; invalid characters sanitized
* **Locale**: export currencies with workbook locale, preserve ISO code in meta
* **Dates**: store ISO in metadata, Excel serial in cells
* **Function set**: guarantee support for SUM, AVERAGE, MIN, MAX, COUNT, IF, AND, OR, NOT, INDEX, MATCH, VLOOKUP, LEFT/RIGHT/MID/LEN, UPPER/LOWER, TODAY/NOW/YEAR/MONTH/DAY
* **Charts**: guarantee type and data bindings; style fidelity best-effort
* **Performance**: prefer ListObjects, lightweight pivot caches

---

### Example

**Forma**

```text
# Table: Sales
Month:text,Revenue:currency
Jan,1000
Feb,1200
Mar,1500

# View: Dashboard
## TabularView dataset=Sales columns=Month,Revenue sort=Revenue:desc
## Chart type=line dataset=Sales x=Month y=Revenue title="Monthly Revenue"
```

**Excel**

* Sheet `Sales`: ListObject `tbl_Sales` with types
* Sheet `view_Dashboard`: formatted table + line chart
* Hidden `__FORMA_META`: export version + mapping

---

## Detailed Examples

### Personal Budget Tracker

```text
# Meta
title="Monthly Budget 2024"
author="John Doe"

# Table: Budget
Category:text,Budgeted:currency,Actual:currency,Difference:currency,Status:text
Housing,2000,1950,=C2-B2,"=IF(D2>=0,\"Under\",\"Over\")"
Food,800,850,=C3-B3,"=IF(D3>=0,\"Under\",\"Over\")"
Transportation,400,375,=C4-B4,"=IF(D4>=0,\"Under\",\"Over\")"
Entertainment,300,425,=C5-B5,"=IF(D5>=0,\"Under\",\"Over\")"
Savings,500,400,=C6-B6,"=IF(D6>=0,\"Under\",\"Over\")"
Total,"=SUM(B2:B6)","=SUM(C2:C6)","=SUM(D2:D6)",

# View: Overview
## Chart type=pie dataset=Budget values=Actual labels=Category title="Spending Distribution"
## TabularView dataset=Budget position=right
### Columns
Category    align=left width=140
Budgeted    format=currency align=right
Actual      format=currency align=right
Difference  format=currency align=right color=green
Status      badge map=Under:green|Over:red align=center

# View: Analysis
## Chart type=bar dataset=Budget x=Category y=Budgeted,Actual title="Budget vs Actual"
## Chart type=line dataset=Budget x=Category y=Difference title="Variance Analysis"
```

### Sales Dashboard

```text
# Table: Sales external
src="./sales_data.csv"
delimiter=","

# Table: Targets
Region:text,Q1:currency,Q2:currency,Q3:currency,Q4:currency
North,100000,110000,120000,130000
South,80000,85000,90000,95000
West,90000,95000,100000,105000

# View: Executive Summary
## Chart type=line dataset=Sales x=Month y=Revenue title="Revenue Trend" 
## Chart type=bar dataset=Sales x=Region y=Revenue title="Regional Performance" position=right

## TabularView dataset=Targets
### Columns
Region  align=left width=100
Q1      format=currency align=right
Q2      format=currency align=right  
Q3      format=currency align=right
Q4      format=currency align=right

# View: Regional Analysis  
## Pivot dataset=Sales rows=Region cols=Quarter values=Revenue:sum totals=both
## Chart type=bar dataset=Sales x=Quarter y=Revenue title="Quarterly Performance"
```

### Project Management

```text
# Table: Tasks
Task:text,Assignee:text,Status:category(Todo,InProgress,Done),Priority:category(High,Medium,Low),DueDate:date,Effort:number
Setup Database,Alice,Done,High,2024-03-15,8
API Development,Bob,InProgress,High,2024-03-22,16  
Frontend UI,Charlie,Todo,Medium,2024-03-29,12
Testing,Alice,Todo,Medium,2024-04-05,6
Documentation,Bob,Todo,Low,2024-04-10,4

# Table: TeamCapacity
Member:text,HoursPerWeek:number,Utilization:percentage
Alice,40,0.85
Bob,35,0.90
Charlie,40,0.75

# View: Sprint Board
## TabularView dataset=Tasks filter=Status!="Done" sort=Priority:desc,DueDate:asc
### Columns
Task        align=left width=200
Assignee    align=left width=100
Status      badge map=Todo:gray|InProgress:blue|Done:green
Priority    badge map=High:red|Medium:yellow|Low:green
DueDate     format=date align=center
Effort      align=right suffix=" hrs"

# View: Analytics
## Chart type=pie dataset=Tasks values=Effort labels=Status title="Effort by Status"
## Chart type=bar dataset=Tasks x=Assignee y=Effort title="Effort by Team Member" position=right
## Chart type=line dataset=TeamCapacity x=Member y=Utilization title="Team Utilization"
```

---

*Forma Format Specification v1.0*
