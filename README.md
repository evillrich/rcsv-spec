# Rich CSV (RCSV) Specification

## The Markdown for Spreadsheets

RCSV is a lightweight, text-based format that extends standard CSV to support formulas, formatting, multi-sheet structures, and charts. It bridges the gap between basic CSV files and full spreadsheet applications, making spreadsheet functionality embeddable everywhere.

## Why RCSV?

Business productivity relies on two fundamental content types:

- **Text content**: Solved by Markdown (ubiquitous, lightweight, embeddable)
- **Tabular data**: Unsolved gap between CSV (too basic) and Excel/Sheets (too heavy)

RCSV fills this gap by bringing essential spreadsheet features to a simple text format that can be embedded in chat interfaces, documentation, wikis, and business applications.

## Key Features

- **CSV Compatible**: Every CSV file with headers is a valid RCSV file
- **Excel/Sheets Compatible**: Full round-trip compatibility with Excel and Google Sheets
- **Formulas**: Support for calculations with Excel-compatible syntax
- **Multi-sheet**: Organize data across multiple sheets in a single file
- **Formatting**: Type hints, currency, percentages, and display formatting
- **Charts**: Simple charting directives for common visualizations
- **Layout & Positioning**: Horizontal and vertical positioning of tables and charts
- **Human Readable**: Plain text format suitable for version control
- **AI Friendly**: Easy for language models to generate and modify

## Quick Example

```rcsv
## Chart: type=pie, title="Budget Distribution", values=Budget, labels=Category, position=right
Category,Budget,Actual,Variance
Marketing,$50000,$45000,=C2-B2
Engineering,$200000,$195000,=C3-B3
Sales,$75000,$80000,=C4-B4
Total,"=SUM(B2:B4)","=SUM(C2:C4)",=C5-B5
```

## Use Cases

RCSV is designed for the 80% of spreadsheet use cases that don't require advanced Excel features:

- **Embedded calculations** in documentation and wikis
- **Interactive data** in chat conversations
- **Budget tracking** in project management tools
- **Quick analysis** without opening Excel/Sheets
- **Version-controlled** data with formulas

## Specification

The full specification is available in [rcsv_spec_v1.md](rcsv_spec_v1.md).

### Core Components

1. **Basic Data**: Standard CSV format with values and formulas
2. **Header Requirements**: All tables must include header rows (first data row defines columns)
3. **Sheet Structure**: One table per sheet for clear organization and referencing
4. **Type System**: Column type hints (text, number, currency, percentage, date, boolean)
5. **Formulas**: Excel-compatible formula syntax with `=` prefix
6. **Multi-sheet**: Sheet separation with `## Sheet: name` directives
7. **Charts**: Inline chart definitions with `## Chart:` directives
8. **Layout & Positioning**: Block positioning with `position=right` for horizontal layouts
9. **Formatting**: Display hints using `:type` suffix on headers

## File Format

- **Extension**: `.rcsv`
- **MIME Type**: `text/rcsv` or `application/rcsv`
- **Encoding**: UTF-8

## Implementation Status

This repository contains the RCSV format specification. Implementations are being developed for:

- JavaScript/TypeScript parser and renderer
- Python library
- VS Code extension
- Web component for embedding
- Microsoft Excel/Google Sheets import/export tools

## Contributing

We welcome contributions to the RCSV specification! Please:

1. Review the current [specification](rcsv_spec_v1.md)
2. Open an issue to discuss proposed changes
3. Submit a pull request with specification updates

## Philosophy

RCSV follows the "good enough" philosophy - it's not meant to replace Excel or Google Sheets for complex analysis. Instead, it makes basic spreadsheet functionality universally accessible, just as Markdown did for formatted text.

When you need complex pivot tables, macros, or complex analytics, export to Excel. When you need to embed, share, or version control your data, use RCSV.

## License

The RCSV specification is released under the MIT License. See [LICENSE](LICENSE) file for details.

## Contact

For questions, suggestions, or discussions about RCSV, please open an issue in this repository.
