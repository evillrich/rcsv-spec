# Rich CSV (RCSV) Format Specification

## Vision: Markdown for Tabular Data

Rich CSV (RCSV) is designed to be the **"Markdown for spreadsheets"** - a lightweight, text-based format that bridges the gap between basic CSV and full spreadsheet applications. Just as Markdown made rich text formatting universally accessible, RCSV aims to make spreadsheet functionality embeddable everywhere.

## The Missing Piece

Business productivity relies on two fundamental content types:

1. **Text content**: Solved by Markdown (ubiquitous, lightweight, embeddable)
2. **Tabular data**: Unsolved gap between CSV (too basic) and Excel/Sheets (too heavy)

RCSV fills this gap by extending CSV with essential spreadsheet features while maintaining the simplicity and embeddability that made Markdown successful.

## Overview

Rich CSV (RCSV) is a lightweight, text-based format that extends standard CSV to support formulas, formatting, multi-sheet structures, and charts. It maintains CSV compatibility while adding business-focused spreadsheet capabilities for embedding in chat interfaces, documentation, wikis, and business applications.

## Design Principles

- **CSV Compatible**: Every CSV file is a valid RCSV file (headers optional)
- **Excel/Sheets Compatible**: Full round-trip compatibility with Excel and Google Sheets
- **Embeddable everywhere**: Like Markdown, designed for universal embedding
- **Markdown-like speed**: Fast parsing and rendering
- **Human readable**: Plain text format suitable for version control
- **Business focused**: Covers 80% of spreadsheet use cases
- **Lightweight**: Minimal syntax overhead
- **AI friendly**: Easy for language models to generate and modify
- **Good enough**: Handles typical business scenarios without replacing Excel/Sheets

## Progressive Enhancement from CSV

RCSV follows a progressive enhancement model that allows users to start with basic CSV and gradually add features as needed. This ensures maximum compatibility while providing a clear learning path.

### Level 1: Plain CSV

```csv
John,25,Engineer
Jane,30,Designer
```

Pure data transfer. Works everywhere. No headers required - RCSV supports headerless CSV with limited functionality.

### Level 2: Add Formulas (No Headers)

```csv
John,25,75000
Jane,30,80000
Total,=SUM(B1:B2),=SUM(C1:C2)
```

Cell references unlock calculations. Still very simple. Formulas work without column headers using standard cell references (A1, B2, etc.).

### Level 3: Add Headers

```csv
Name,Age,Salary
John,25,75000
Jane,30,80000
Average,=AVERAGE(B2:B3),=AVERAGE(C2:C3)
```

Column names enable charts and better readability. Headers unlock advanced features and provide context for data interpretation.

### Level 4: Add Types & Formatting

```csv
Name:text:text-left:bold,Age:number:text-center,Salary:currency:text-right:text-green
John,25,75000
Jane,30,80000
Average,=AVERAGE(B2:B3),=AVERAGE(C2:C3)
```

Type annotations and formatting properties enable proper data display, validation, and styling. Data displays correctly with currency symbols, number formatting, colors, alignment, and typography.

### Level 5: Full Power

```csv
# Title: Team Overview
## Chart: type=bar, title="Team Salary Overview", x=Name, y=Salary

Name:text,Age:number,Salary:currency
John,25,75000
Jane,30,80000
Average,=AVERAGE(B2:B3),=AVERAGE(C2:C3)
```

Metadata, charts, and full RCSV features unlock rich data visualization and documentation capabilities.

### Migration Strategy

This progressive enhancement means:

- **Start simple**: Any CSV works, even without headers
- **Add incrementally**: Enhance features only when needed
- **Universal compatibility**: Each level still works in simpler contexts
- **Learning curve**: Users only learn new syntax when they need new features
- **Tool flexibility**: Parsers can support any subset of features

## Use Cases & Embedding

### Target Environments

- **Chat interfaces**: Inline spreadsheet editing within conversations
- **Documentation platforms**: Live calculations in wikis and knowledge bases
- **Note-taking apps**: Data that calculates and updates
- **Project management tools**: Budget and resource tracking
- **CRM systems**: Revenue projections and data analysis
- **Business applications**: Embedded tabular functionality

### The "Good Enough" Philosophy

RCSV is designed to handle ~80% of spreadsheet use cases without requiring full Excel/Sheets:

**Covers well:**

- Basic calculations and formulas
- Simple charts and visualizations
- Data formatting and presentation
- Multi-sheet organization
- Import/export workflows

**Clear graduation path:**

- Start with RCSV for embedded/collaborative scenarios
- Export to Excel when advanced features needed (pivot tables, macros, complex analytics)
- Import back to RCSV for sharing and embedding

### Embedding Examples

**In documentation:**

````
## Budget Analysis

Our Q4 budget shows solid performance across categories:

```rcsv
## Chart: type=bar, title="Budget Status"
Category:text,Budget:currency,Actual:currency,Variance:currency
Marketing,50000,45000,=C2-B2
Engineering,200000,195000,=C3-B3
```
````

**In chat conversations:**

```

User: "Create a budget tracker for our team expenses"
Assistant: [generates RCSV with formulas]
User: "Update marketing to $60k"
[User edits directly in interface - instant update, or Assistant modifies]
Assistant: [RCSV recalculates automatically, charts update]

```

## File Extension

- **Extension**: `.rcsv`
- **MIME Type**: `text/rcsv` or `application/rcsv`

## Basic Syntax

### Single Sheet

```csv
Column1,Column2,Column3
Value1,Value2,=A2+B2
Value4,Value5,=A3+B3
```

### Multi-Sheet Format

```csv
# Sheet: Revenue
Month,Sales,Target,Variance
Jan,100000,95000,=B2-C2
Feb,120000,100000,=B3-C3

# Sheet: Costs
Month,Marketing,Operations,Total
Jan,15000,25000,=B2+C2
Feb,18000,27000,=B3+C3
```

### Sheet Structure Requirements

**One Table Per Sheet**: Each sheet contains exactly one tabular dataset. This ensures:

- Clear data organization and boundaries
- Predictable parsing and referencing
- Consistent cross-sheet formula behavior
- Simple chart data binding

**Header Row Required**: All tabular data must include a header row defining column names:

- The first data row (any row not starting with `#` or `##`) is treated as headers
- Data can appear after any number of comments and metadata declarations
- Column references and formulas depend on consistent header structure
- Type inference and formatting apply to header-defined columns

## Comment Hierarchy

- `#` - Major structural elements (sheets, document metadata)
- `##` - Sheet-level metadata (charts, tables, etc.)

## Data Types

Data types are specified in column headers using colon notation. Type inference is performed when types are not explicitly specified.

```csv
Name:text,Price:currency,Date:date,Active:boolean,Score:percentage
Widget A,2.50,2024-01-15,TRUE,0.85
Widget B,1.75,2024-02-01,FALSE,0.92
```

### Type Inference Algorithm

RCSV uses a sophisticated two-phase type inference algorithm that mirrors the approach used by Excel and Google Sheets to handle columns containing both values and formulas.

#### Phase 1: Pre-Calculation Inference

When data types are not explicitly specified, the parser first analyzes non-formula cells:

1. **Sample Analysis**: Examine the first N cells in each column (configurable, default 100)
2. **Cell Classification**: Separate cells into values vs formulas (cells starting with `=`)
3. **Threshold Check**: Calculate the percentage of non-formula cells in the sample
4. **Type Decision**:
   - If ≥70% are non-formula cells AND they have a consistent type → Apply that type to the column
   - If ≥70% are non-formula cells BUT they have mixed types → Apply TEXT type to the column
   - If <70% are non-formula cells → Mark column as UNSPECIFIED (defer to Phase 2)

#### Phase 2: Post-Calculation Inference

For columns marked as UNSPECIFIED in Phase 1:

1. **Formula Calculation**: Calculate all formulas to produce actual values
2. **Value Analysis**: Apply the same type inference rules to the calculated results
3. **Final Type Assignment**: Assign the inferred type or default to TEXT if still ambiguous

#### Type Detection Rules

Both phases apply these rules in priority order when analyzing cell values:

1. **Boolean**: `TRUE`, `FALSE` (case insensitive) → `boolean`
2. **Date**: ISO format `YYYY-MM-DD` or common formats `MM/DD/YYYY`, `DD/MM/YYYY` → `date`
3. **Currency**: Values starting with currency symbols (`$`, `€`, `£`) → `currency`
4. **Percentage**: Values ending with `%` → `percentage`
5. **Number**: Numeric values (integers, decimals) → `number`
6. **Text**: Everything else → `text` (default)

#### Excel/Google Sheets Compatibility

This two-phase approach replicates the type inference behavior of Excel and Google Sheets:

- Both platforms initially defer type decisions for formula-heavy columns
- Both perform post-calculation analysis to determine appropriate formatting
- Both apply column-level type assignments that affect all cells in the column
- This ensures predictable round-trip compatibility when importing/exporting between platforms

### Supported Data Types

- `text` - String values (default)
- `number` - Numeric values
- `currency` - Monetary values
- `percentage` - Percentage values (0.1 = 10%)
- `date` - ISO date format (YYYY-MM-DD)
- `boolean` - TRUE/FALSE values
- `category` - Enumerated string values with optional validation
  - Values must be from a predefined list
  - Example usage: Status columns (Active/Inactive), Priority levels (High/Medium/Low)
  - Syntax: `Status:category(Active,Inactive,Pending)`
  - Validation: Invalid values treated as text with warning

## Type Coercion and Data Handling

RCSV follows a type system similar to Excel and Google Sheets, with automatic coercion during operations and clear error handling for incompatible conversions.

### Data Storage Model

RCSV stores data using native types from CSV parsing:

- **Numbers**: Stored as native numeric values (`123`, `45.67`)
- **Strings**: Stored as text values (`"hello"`, `"123abc"`)
- **Booleans**: Stored as native boolean values (`true`, `false`)
- **Empty cells**: CSV empty cells (`",,,"`) become `null` values
- **Empty strings**: Explicit empty strings (`""`) from formulas remain as strings

### CSV Empty Cell Handling

**Critical distinction**: RCSV differentiates between truly empty cells and empty strings:

```csv
# CSV input: Item,,Price → Results in: ["Item", null, "Price"]
# Formula result: ="" → Results in: ""
```

- **Empty CSV cells** (`,,,`): Become `null` values, not counted by COUNTA
- **Empty string results** (`""`): From formulas, counted by COUNTA
- **This matches Excel/Google Sheets behavior** for empty cell vs empty string handling

### Null Value Handling

RCSV treats both empty strings and missing values in CSV data as null values:

```csv
# These examples produce identical results:
one,"",three    # Quoted empty string
one,,three      # Missing value between commas
```

Both cases result in the middle cell being stored as a `null` value. This ensures consistent behavior regardless of how empty data is represented in the source CSV file.

**Key behaviors:**
- Empty strings (`""`) and missing values (`,`) are both converted to `null` during parsing
- `null` values are not counted by COUNTA function
- `null` values are treated as 0 in mathematical operations
- This approach maintains compatibility with Excel and Google Sheets empty cell handling

### Automatic Type Coercion Rules

#### Math Operations (`+`, `-`, `*`, `/`, `^`)

RCSV performs automatic coercion to enable mathematical operations:

```csv
Value:text,Number:number,Result:number
"123",10,="123"+10    # Result: 133 (string coerced to number)
"45.5",5,="45.5"*5    # Result: 227.5 (string coerced to number)
"abc",10,="abc"+10    # Result: #VALUE! (cannot coerce)
"",5,=""+5            # Result: 5 (empty string coerced to 0)
```

**Coercion order for math operations**:

1. **Numbers**: Pass through unchanged
2. **Strings**: Attempt `parseFloat()` conversion
   - Pure numeric strings (`"123"`, `"45.67"`) → Convert to number
   - Non-numeric strings (`"abc"`, `"123abc"`) → `#VALUE!` error
   - Empty strings (`""`) → Convert to 0
3. **Booleans**: `true` → 1, `false` → 0
4. **Null**: Convert to 0

#### String Operations (`&` concatenation)

All values are coerced to strings for concatenation:

```csv
A:number,B:text,Result:text
123,"abc",=A2&B2     # Result: "123abc"
true,"test",=A3&B3   # Result: "truetest"
```

#### Comparison Operations

Type-aware comparisons with coercion:

```csv
A:text,B:number,Equal:boolean
"123",123,=A2=B2     # Result: TRUE (string coerced for comparison)
"abc",123,=A3=B3     # Result: FALSE (different types, no coercion)
```

### Function-Specific Behavior

#### COUNTA vs COUNT

**COUNTA** (Count All): Counts all non-empty values regardless of type

- Counts: numbers, strings (including `""`), booleans, dates
- Does NOT count: `null` values (empty CSV cells)

**COUNT**: Counts only numeric values

- Counts: numbers, numeric strings that can be coerced
- Does NOT count: text strings, booleans, `null`, empty strings

```csv
A:text,B:number,C:text,Count:number,CountA:number
"Hello",10,"",=COUNT(A2:C2),=COUNTA(A2:C2)
# COUNT: 1 (only the number 10)
# COUNTA: 2 ("Hello" and "", but not null)
```

#### Mathematical Functions

All mathematical functions (`SUM`, `AVERAGE`, `MIN`, `MAX`) attempt coercion:

```csv
Values:text,Sum:number
"10",=SUM(A2:A4)
"20.5",
"abc"        # This row will cause SUM to return #VALUE!
```

### Error Handling and Display

#### Error Types

- **`#VALUE!`**: Type coercion failure (e.g., `"abc" + 10`)
- **`#DIV/0!`**: Division by zero
- **`#REF!`**: Invalid cell reference
- **`#NAME?`**: Unknown function name
- **`#CIRCULAR!`**: Circular reference detected

#### Renderer Display Standards

**Error cells should be displayed as**:

- Background color: Light red (`#ffebee`)
- Text color: Dark red (`#c62828`)
- Font style: Normal (not bold/italic)
- Content: Show error code (e.g., `#VALUE!`)
- Tooltip/hover: Show detailed error message

#### Error Propagation

Errors bubble up through formula chains:

```csv
A:text,B:number,C:number,D:number
"abc",10,=A2+B2,=C2*2
# C2: #VALUE! (cannot convert "abc")
# D2: #VALUE! (error propagates from C2)
```

### Excel and Google Sheets Compatibility

RCSV type coercion **exactly matches** Excel and Google Sheets behavior:

- **Automatic coercion**: `"123" + 10 = 133`
- **Coercion failures**: `"abc" + 10 = #VALUE!`
- **Empty cell handling**: `null + 10 = 10`
- **Function behavior**: COUNTA counts all non-null values
- **Error propagation**: Errors bubble through formula dependencies

### Performance Considerations

**Current Implementation**: Test coercion on each operation
**Future Optimization**: Cache coercion metadata per cell

- `canCoerceToNumber: boolean`
- `numericValue?: number` (pre-computed)
- Cache invalidated when cell value changes

### Type Coercion Examples

```csv
# Mixed data types with automatic coercion
Value:text,Number:number,Math:number,Concat:text,Valid:boolean
"123",10,="123"+10,"Item: "&10,=ISNUMBER("123")
"45.67",5,="45.67"*5,"Price: "&45.67,=ISNUMBER("45.67")
"abc",15,="abc"+15,"Name: "&15,=ISNUMBER("abc")
"",20,=""+20,"Empty: "&20,=ISNUMBER("")

# Results:
# Row 2: 133, "Item: 10", TRUE
# Row 3: 228.35, "Price: 45.67", TRUE  
# Row 4: #VALUE!, "Name: 15", FALSE
# Row 5: 20, "Empty: 20", FALSE
```

## Formulas

Formulas use Excel-compatible syntax and begin with `=`:

### Basic Operations

```csv
Item,Price,Qty,Total
A,10,2,=B2*C2
B,15,3,=B3*C3
```

### Formula Quoting Requirements

**Important**: Formulas containing commas must be quoted to prevent CSV parsing issues.

```csv
# Simple formulas (no commas) - quotes optional
A:number,B:number,Result:number
10,20,=A1+B1
5,15,=A2*B2

# Complex formulas (with commas) - quotes required
A:number,B:number,C:number,Total:number
10,20,30,"=SUM(A1,B1,C1)"
5,15,25,"=AVERAGE(A2:B2)"
```

**Rule of thumb**: If your formula contains commas, quote it: `"=FUNCTION(arg1,arg2)"`

### Functions

```csv
Month,Sales
Jan,1000
Feb,1200
Mar,1500
Total,=SUM(B2:B4)
Average,=AVERAGE(B2:B4)
```

### Cross-Sheet References

```csv
# Sheet: Data
Product,Revenue
A,50000
B,75000

# Sheet: Summary
Total Revenue,=SUM(Data!B:B)
Best Product,"=INDEX(Data!A:A,MATCH(MAX(Data!B:B),Data!B:B,0))"
```

### Sheet Name Handling

- Simple sheet names (no spaces): `Data!A1`
- Sheet names with spaces: `'Sales Data'!A1`
- Sheet names with apostrophes: `'John''s Data'!A1` (double the apostrophe to escape)
- Complex sheet names: Always use single quotes for consistency

### Supported Functions

**Mathematical:**

- `SUM(range)` - Sum of values in range
- `AVERAGE(range)` - Average of values in range
- `MIN(range)` - Minimum value in range
- `MAX(range)` - Maximum value in range
- `COUNT(range)` - Count of non-empty cells in range
- `COUNTA(range)` - Count of non-empty cells (including text)
- `ABS(number)` - Absolute value
- `ROUND(number, digits)` - Round to specified digits

**Logical:**

- `IF(condition, true_value, false_value)` - Conditional logic
- `AND(condition1, condition2, ...)` - Logical AND
- `OR(condition1, condition2, ...)` - Logical OR
- `NOT(condition)` - Logical NOT

**Lookup:**

- `VLOOKUP(lookup_value, table_array, col_index, exact_match)` - Vertical lookup
- `INDEX(array, row_num)` - Return value at index
- `MATCH(lookup_value, lookup_array, match_type)` - Find position of value

**Text:**

- `CONCATENATE(text1, text2, ...)` - Join text strings
- `LEFT(text, num_chars)` - Extract leftmost characters
- `RIGHT(text, num_chars)` - Extract rightmost characters
- `MID(text, start, num_chars)` - Extract middle characters
- `LEN(text)` - Length of text
- `UPPER(text)` - Convert to uppercase
- `LOWER(text)` - Convert to lowercase

**Date:**

- `TODAY()` - Current date
- `NOW()` - Current date and time
- `YEAR(date)` - Extract year from date
- `MONTH(date)` - Extract month from date
- `DAY(date)` - Extract day from date

### Function Scope

RCSV includes essential functions for 80% of use cases. Advanced functions like `HLOOKUP` (rarely used in practice) and `XLOOKUP` (newer, complex, limited Excel compatibility) are excluded to maintain simplicity and universal compatibility. The `INDEX`/`MATCH` combination provides equivalent functionality to most advanced lookup scenarios.

### Function Error Handling

- Unknown functions: Display `#NAME?` error
- Invalid parameters: Display `#VALUE!` error
- Division by zero: Display `#DIV/0!` error
- Invalid references: Display `#REF!` error
- Circular references: Display `#CIRCULAR!` error after iteration limit

## Formatting

RCSV v1.0 supports column-level formatting only. Row-level and cell-level formatting may be added in future versions if they can be implemented with full round-trip compatibility to Excel and Google Sheets.

### Column-Level Formatting

Column formatting is specified directly in column headers using a colon-separated syntax. **Explicit datatypes are required when using any formatting properties** to avoid parsing ambiguity:

```csv
ColumnName:datatype:style1:style2:style3
```

**Column formatting examples:**

```csv
Product:text:text-left,Price:currency:text-right:bold,Status:category:text-center:bg-green:text-white
Widget A,29.99,Active
Widget B,19.99,Pending
```

**Without formatting (type inference works):**

```csv
Product,Price,Status
Widget A,29.99,Active
Widget B,19.99,Pending
```

**Mixed approach (some columns with formatting, others without):**

```csv
Product,Price:currency:text-right:bold,Status
Widget A,29.99,Active
Widget B,19.99,Pending
```

### Supported Formatting Properties

RCSV uses CSS-style naming conventions for formatting properties, but these are predefined RCSV formatting options, not arbitrary CSS. Future versions may expand this list.

**Text Alignment:**

- `text-left`, `text-center`, `text-right`

**Colors:**

- `text-{color}` (text-red, text-green, text-blue, etc.)
- `bg-{color}` (bg-red, bg-green, bg-blue, etc.)
- `bg-{color}-{intensity}` (bg-yellow-100, bg-gray-200, etc.)

**Typography:**

- `bold`, `italic`, `underline`

**Future Properties:**

- `border`, `border-t`, `border-b`, etc. (planned for future versions)

### Column Formatting Examples

```csv
Product:text:text-left:bold,Revenue:currency:text-right:text-green,Status:category:text-center:bg-gray-100
Widget A,125000,Active
Widget B,98000,Pending
Widget C,87500,Inactive
```

**Multiple style combinations:**

```csv
Name:text:text-left:bold:italic,Score:number:text-right:bg-blue:text-white,Grade:text:text-center:underline
John Doe,95.5,A+
Jane Smith,87.2,B+
```

## Charts

Charts are defined using sheet-level metadata comments:

### Basic Chart

```csv
## Chart: type=bar, title="Monthly Sales", x=Month, y=Sales
Month,Sales
Jan,1000
Feb,1200
Mar,1500
```

### Multiple Charts

```csv
## Chart: type=line, title="Sales Trend", x=Month, y=Sales
## Chart: type=pie, title="Sales Distribution", values=Sales, labels=Month
Month,Sales
Q1,5000
Q2,7000
Q3,6500
Q4,8000
```

### Multiple Series Charts

```csv
## Chart: type=line, title="Sales vs Target", x=Month, y=Sales,Target, series="Actual Sales,Monthly Target"
Month,Sales,Target
Jan,1000,950
Feb,1200,1100
Mar,1500,1400
```

### Chart Types

- `bar` - Vertical bar chart
- `column` - Horizontal bar chart
- `line` - Line chart
- `pie` - Pie chart
- `scatter` - Scatter plot

### Chart Properties

- `type` - Chart type (required)
- `title` - Chart title
- `x` - X-axis column name
- `y` - Y-axis column name(s) - comma-separated for multiple series
- `series` - Series names for legend (optional, defaults to column names)
- `values` - Values column for pie charts
- `labels` - Labels column for pie charts
- `position` - Layout positioning: `bottom` (default) or `right`

## Layout and Positioning

### Block Positioning

RCSV content is organized into blocks (data tables and charts) that are positioned sequentially. By default, all blocks render vertically (top to bottom). Optional positioning metadata allows horizontal layouts.

### Default Behavior

All blocks render below the previous block in source order:

```csv
Month,Sales
Jan,1000
Feb,1200

## Chart: type=bar, title="Sales Performance"
## Chart: type=line, title="Sales Trend"
# Result: Table → Chart → Chart (all vertically stacked)
```

### Horizontal Positioning

Use `position=right` to render a block to the right of the previous block:

```csv
Month,Sales,Target
Jan,1000,950
Feb,1200,1100

## Chart: type=bar, title="Performance", position=right
## Chart: type=line, title="Trend"
# Result: Table → Chart (to the right) → Chart (below the table/chart pair)
```

### Table Positioning

Tables can also be positioned using `## Table:` metadata:

```csv
## Chart: type=pie, title="Sales Distribution"

## Table: position=right
Month,Sales,Target
Jan,1000,950
Feb,1200,1100

## Chart: type=line, title="Monthly Trend"
# Result: Chart → Table (to the right) → Chart (below)
```

### Position Properties

- `position=bottom` - Render below the previous block (default, usually omitted)
- `position=right` - Render to the right of the previous block

### Responsive Behavior

Renderers MAY override positioning based on display constraints:

- Mobile devices may force `position=bottom` for all blocks
- Narrow displays may fall back to vertical stacking
- Print layouts may optimize for page breaks

## Comments and Metadata

### Document-Level Metadata

```csv
# Title: Q4 Financial Report
# Author: Finance Team
# Created: 2024-01-15
# Version: 1.0

# Sheet: Revenue
Month,Amount
```

### Sheet-Level Metadata

```csv
# Sheet: Sales Data
## Chart: type=bar, title="Monthly Sales", x=Month, y=Sales
Product:text,Revenue:currency:text-right,Growth:percentage:text-center
Product,Revenue,Growth
```

### Comments

Comments are lines beginning with `#` that do not contain metadata:

```csv
# This file contains our Q4 budget analysis
# Updated weekly by the finance team

# Sheet: Budget Data
## Chart: type=bar, title="Budget vs Actual"

# The following data is updated from our accounting system
Category,Budget,Actual
Marketing,50000,45000
Engineering,200000,195000

# End of budget data
```

## Escaping Rules

### Metadata Escaping

RCSV metadata in comments (chart definitions, column formatting) uses quote-based escaping similar to CSV:

**Simple values:**

```csv
## Chart: type=bar, x=Month, y=Sales
```

**Values with special characters (commas, equals):**

```csv
## Chart: type=bar, title="Q4 Report, Final", x=Month
```

**Multiple values (comma-separated lists):**

```csv
## Chart: type=line, x=Month, y=Revenue,Expenses,Profit
```

**Mixed quoted and unquoted lists:**

```csv
## Chart: type=line, x=Month, y="Revenue, After Tax",Expenses,"Profit, Net"
```

**Escaping quotes within quoted values:**

```csv
## Chart: title="Sales Report \"2024\"", x=Month
## Chart: title='John\'s Report', x=Month
```

**Rules:**

1. Values containing commas or equals signs must be quoted
2. Lists are comma-separated, with individual items optionally quoted
3. Use backslash to escape quotes within quoted strings
4. Parser splits on commas outside of quotes
5. Quotes are removed from individual values after parsing

### CSV Compatibility

RCSV follows standard CSV escaping rules to maintain compatibility:

**Commas in values:**

```csv
Name,Description
"Smith, John","A person with comma in name"
```

**Quotes in values:**

```csv
Name,Quote
John,"He said ""Hello"" to me"
```

**Newlines in values:**

```csv
Name,Address
John,"123 Main St
Apt 2B"
```

### RCSV-Specific Escaping

**Hash symbols in data:**
Hash symbols in CSV data are treated as literal data, not comments:

```csv
Product,Notes
Widget A,Issue #123 reported
Widget B,Version #2.1 released
```

**Comments are only recognized when:**

- The line begins with `#` as the first character
- The line begins with `#` after leading whitespace
- Comments must be outside CSV data sections to maintain compatibility

**Colons in column names:**
Use double colons to escape when specifying formatting:

```csv
Time::Stamp:date:text-left,Value:number:text-right
Time:Stamp,Value
2024-01-15,100
```

**Single quotes in sheet names:**
Use single quotes with doubling for escapes (Excel/Sheets compatible):

```csv
# Sheet: John's Data   → Reference as 'John''s Data'!A1
# Sheet: Sales Data    → Reference as 'Sales Data'!A1
```

## CSV Compatibility

### 100% Round-Trip Support

RCSV is designed for nearly perfect bidirectional compatibility with Excel and Google Sheets:

**Guaranteed Round-Trip:**

- All basic data types (text, number, currency, percentage, date, boolean)
- All supported formulas and functions
- Basic column formatting (alignment, colors, bold, italic)
- Sheet structure and organization
- Chart types: bar, column, line, pie, scatter
- Multi-series charts and multiple charts per sheet

**Export Behavior:**

- RCSV → Excel/Sheets: All features map directly to native functionality
- Excel/Sheets → RCSV: Only supported features are preserved, others are documented as "lost in translation"

**No Approximation Policy:**

- Features that cannot round-trip perfectly are excluded from RCSV
- Better to have fewer features that work perfectly than many that work approximately

### One-Way Compatibility

- **CSV → RCSV**: Every CSV file with headers is a valid RCSV file
- **RCSV → CSV**: Only basic RCSV files (without metadata/comments) are valid CSV
- RCSV files with `#` comments or `##` metadata are not valid CSV
- **Header Requirement**: RCSV assumes the first data row contains column headers, as is standard practice in business CSV files

### Forward Compatibility (CSV to RCSV)

- Standard CSV files with headers can be opened directly as RCSV
- No syntax changes needed for header-based CSV files
- Full feature compatibility with business-standard CSV format
- Zero migration cost for typical business CSV files

### Backward Compatibility (RCSV to CSV)

- Basic RCSV files (data only, no metadata) work in CSV parsers
- Files with metadata comments will cause parsing errors in strict CSV tools
- Formulas appear as text in CSV parsers (won't calculate)
- Column-level formatting is lost when opened as CSV

### Migration Path

```csv
# Stage 1: Plain CSV (compatible with both CSV and RCSV parsers)
Product,Price,Quantity
Widget A,2.50,10
Widget B,1.75,15

# Stage 2: Enhanced with RCSV data types (RCSV parsers only)
Product:text,Price:currency,Quantity:number
Widget A,2.50,10
Widget B,1.75,15

# Stage 3: Full RCSV with metadata and formulas
## Chart: type=bar, title="Product Sales"
Product:text,Price:currency,Quantity:number,Total:currency
Widget A,2.50,10,=B2*C2
Widget B,1.75,15,=B3*C3
```

## Internationalization

### Locale Support

- **Date formats**: Support both US (`MM/DD/YYYY`) and ISO (`YYYY-MM-DD`) formats in all locales
- **Number formats**: Support locale-specific decimal separators (`.` vs `,`) and thousands separators
- **Currency symbols**: Recognize major currency symbols (`$`, `€`, `£`, `¥`, etc.)
- **Percentage**: Universal `%` symbol support

### Implementation Guidelines

- Parsers MUST support ISO 8601 dates as universal standard
- Parsers SHOULD detect locale from system settings or explicit configuration
- When ambiguous, prefer ISO formats for maximum compatibility
- Type inference should account for locale-specific formatting patterns

### Canonical Serialization

To ensure consistent data exchange, RCSV files MUST use standardized number formatting:

- **Decimal separator**: Always use dot (`.`)
- **Thousands separator**: Always use comma (`,`)
- **Example**: `1,234.56` not `1.234,56`

Parsers MAY accept locale-specific input formats but MUST output canonical format for interoperability.

## Error Handling

### Parser Modes

RCSV parsers support two operational modes to accommodate different use cases:

- **Strict mode** (`strict=true`): Fail immediately on any parse errors, invalid metadata, or type mismatches
- **Lenient mode** (`strict=false`, default): Skip invalid sections with warnings, continue processing

### Usage Context

- **Validation tools, CI/CD pipelines**: Use strict mode for data quality assurance
- **Chat interfaces, embedded contexts**: Use lenient mode for user-friendly experience

### Parse Errors

- **Malformed CSV**: Follow standard CSV error handling, report line/column
- **Invalid metadata**: Ignore invalid metadata lines, log warnings
- **Unknown data types**: Fall back to `text` type, log warning
- **Invalid formulas**: Display error value in cell, continue parsing

### Runtime Errors

- **Formula calculation errors**: Display standard error codes (`#VALUE!`, `#REF!`, etc.)
- **Type conversion errors**: Display original value with warning flag
- **Circular references**: Attempt resolution with iteration limit, display `#CIRCULAR!` if unresolved

## Implementation Guidelines

### Performance Expectations

RCSV is optimized for embedded and interactive use cases:

- **Target capacity**: Up to 10,000 rows for typical interactive performance
- **Recommended limits**: 100 sheets per document, 1,000 columns per sheet
- **Calculation performance**: Formula recalculation should complete within 100ms for typical documents
- **Memory usage**: Target <50MB for documents within recommended limits

Implementations MAY support larger datasets but should clearly document performance characteristics and limitations.

### Error Recovery

- Parsers should be resilient and continue processing after errors
- Invalid sections should be skipped with appropriate warnings
- Partial functionality is preferred over complete failure

## Development Roadmap

### POC Scope (Internal Proof of Concept)

**Core Features:**

- Single sheet support
- Basic data types: `text`, `number`, `currency`
- One formula function: `SUM()`
- Basic cell references: `=A1+B2`
- One chart type: `bar`
- No formatting

**POC Example:**

```csv
## Chart: type=bar, title="Budget Overview", x=Category, y=Budget
Category:text,Budget:currency,Actual:currency,Total:currency
Housing,2000,1950,=B2+C2
Food,800,850,=B3+C3
Total,"=SUM(B2:B3)","=SUM(C2:C3)","=SUM(D2:D3)"
```

### MVP Scope (Public Release v1.0)

**All POC features plus:**

- Multi-sheet support with cross-references
- All basic data types: `text`, `number`, `currency`, `percentage`, `date`, `boolean`
- Essential functions: Mathematical, logical, basic lookup, text, date
- All chart types: `bar`, `column`, `line`, `pie`, `scatter`
- Basic formatting: colors, bold, italic, alignment
- Type inference
- Comprehensive error handling

### Post-MVP Features (Future Versions)

**Priority features for future releases:**

- **Conditional formatting**: Simple rules for data validation and visualization
  - Syntax: `## Conditional: range, condition, formatting`
  - Example: `## Conditional: B2:B10, >1000, color=green:bold`
  - Limited to basic conditions and formatting to maintain round-trip compatibility
- Advanced functions: Financial functions, statistical functions
- Data validation constraints and rules
- Import/export tools for Excel/Sheets with full fidelity
- Advanced chart options (styling, legends, axes customization)
- Enhanced date/time functions
- Text manipulation functions
- Error handling improvements

### Features We Won't Support

These features conflict with our design principles of being lightweight, performant, simple, human-readable, and Excel/Sheets compatible:

**Complex Excel Features:**

- Macros and VBA scripting
- Custom functions and user-defined functions
- Complex pivot tables with multiple dimensions and custom calculations
- Advanced charting (3D charts, stock charts, complex combinations)
- Form controls and interactive elements beyond basic editing
- Array formulas and complex matrix operations

**Platform-Specific Features:**

- Real-time collaborative editing metadata (belongs in application layer)
- User permissions and access control (application responsibility)
- Advanced data connections (databases, web services, APIs)
- Audit trails and change tracking (application responsibility)
- Platform-specific formatting that doesn't round-trip to Excel/Sheets
- Cross-workbook references and external file linking

**Heavy Computational Features:**

- Solver and optimization tools
- Statistical analysis packages beyond basic functions
- Machine learning integrations
- Large dataset processing (specific limits TBD based on performance testing)
- Complex financial modeling tools requiring specialized functions

**Why We Exclude These:**

- They don't round-trip cleanly to Excel/Google Sheets
- They require heavy runtime environments
- They compromise human readability of the text format
- They create vendor lock-in and reduce portability
- They conflict with our "good enough" philosophy
- They move beyond the 80% use case target

## Governance and Versioning

### Standards Management

- RCSV specification maintained by core team with community input
- Semantic versioning: Major.Minor.Patch format
- Backward compatibility guaranteed within major versions
- RFC process for significant changes and new features

### Preventing Fragmentation

- Single canonical specification repository
- Reference implementation maintained alongside specification
- Compliance test suite for parser validation
- Clear extension mechanism for experimental features
- Early governance structure to prevent dialect proliferation

## Examples

### Budget Tracker

```csv
# Title: Monthly Budget
## Chart: type=pie, title="Budget Distribution", values=Budgeted, labels=Category, position=right

Category:text,Budgeted:currency,Actual:currency,Difference:currency,Status:text
Housing,2000,1950,=C2-B2,"=IF(D2<0,""Over"",""Under"")"
Food,800,750,=C3-B3,"=IF(D3<0,""Over"",""Under"")"
Transport,400,450,=C4-B4,"=IF(D4<0,""Over"",""Under"")"
Total,"=SUM(B2:B4)","=SUM(C2:C4)","=SUM(D2:D4)",
```

### Sales Analysis

```csv
# Sheet: Sales Data
Region:text,Q1:currency,Q2:currency,Q3:currency,Q4:currency,Total:currency
North,100000,120000,115000,140000,"=SUM(B2:E2)"
South,85000,95000,105000,125000,"=SUM(B3:E3)"
West,90000,110000,125000,135000,"=SUM(B4:E4)"
Total,"=SUM(B2:B4)","=SUM(C2:C4)","=SUM(D2:D4)","=SUM(E2:E4)","=SUM(F2:F4)"

# Sheet: Analysis  
## Chart: type=line, title="Quarterly Growth", x=Quarter, y=Total
Quarter:text,Total:currency,Growth:percentage
Q1,"=SUM('Sales Data'!B2:B4)",
Q2,"=SUM('Sales Data'!C2:C4)",=(B3-B2)/B2
Q3,"=SUM('Sales Data'!D2:D4)",=(B4-B3)/B3
Q4,"=SUM('Sales Data'!E2:E4)",=(B5-B4)/B4
```

### Simple CSV Migration

```csv
# This is a valid CSV that any tool can read
Name,Email,Department
John Doe,john@company.com,Engineering
Jane Smith,jane@company.com,Marketing

# Enhanced with RCSV features (still CSV compatible!)
Name:text,Email:text,Department:text,Active:boolean
John Doe,john@company.com,Engineering,TRUE
Jane Smith,jane@company.com,Marketing,TRUE
```

## Advantages Over Alternatives

### vs. Standard CSV

- Formulas and live calculations
- Data types and formatting
- Chart generation
- Multi-sheet support
- Full backward compatibility

### vs. Excel/Google Sheets

- Human readable and version control friendly
- Lightweight and embeddable
- Platform independent
- AI/programmatically friendly
- Universal access (no app required)

### vs. Other Markup Formats

- Familiar CSV syntax with low learning curve
- Business-focused features out of the box
- **Forward compatible with standard business CSV files** (CSV files with headers can be opened as RCSV)
- Purpose-built for tabular data

### The Markdown Parallel

Just as Markdown democratized rich text formatting across platforms, RCSV aims to democratize spreadsheet functionality. Both formats share:

- Text-based simplicity
- Universal embeddability
- Version control compatibility
- AI/automation friendliness
- "Good enough" feature coverage for most use cases

---

*Rich CSV (RCSV) Format Specification v1.0*
