# Vessel Financial Reporting System - Operative & Manager Statements

## Overview
This document provides comprehensive guidelines for preparing both **vessel operative statements** and **manager statements** using the ShipNet tool and account categorization system. The guide covers multiple report formats including **summary PDFs**, **detailed PDFs**, **Excel reports**, and **CSV exports**. The process ensures 100% data accuracy by querying all information directly from the vessel management system.

## Implementation Features
The financial reporting system includes the following features:

- ✅ **Operative Statement - Summary**: Professional PDF with category-level totals
- ✅ **Operative Statement - Detailed**: Multi-page PDF with complete account-wise breakdown
- ✅ **Manager Statement - Excel**: Excel file matching template structure with transaction details
- ✅ **Manager Statement - CSV**: Multiple CSV formats with different filtering options
- ✅ **Account-Level Transparency**: Full audit trail with account codes and descriptions
- ✅ **Category Organization**: Operating expenses grouped by maritime categories
- ✅ **Fund Receipt Handling**: Proper identification and formatting of fund receipts
- ✅ **YTD Calculations**: Year-to-date amounts alongside current month data
- ✅ **Professional Formatting**: Maritime industry standard layout and styling
- ✅ **Automated Generation**: Single script to generate all reports

### Generated Files
For each vessel and reporting period, the system generates the following files:

#### Operative Statements
1. **Summary PDF**: `[VESSEL_CODE]_Operative_Statement_Summary_[MMYYYY].pdf`
2. **Detailed PDF**: `[VESSEL_CODE]_Operative_Statement_Detailed_[MMYYYY].pdf`
3. **CSV Export**: `[VESSEL_CODE]_Operative_Statement_Details_[MMYYYY].csv`

#### Manager Statements
4. **Excel Report**: `[VESSEL_CODE]_Manager_Statement_[Month]_[YYYY].xlsx`
5. **CSV Export**: `[VESSEL_CODE]_Manager_Statement_[Month]_[YYYY].csv`
6. **Comprehensive CSV**: `[VESSEL_CODE]_Manager_Statement_Comprehensive_[MMYYYY].csv`
7. **Budget-Filtered CSV**: `[VESSEL_CODE]_Manager_Statement_Budget_Filtered_[MMYYYY].csv`

## Report Types Available

### 1. Operative Statement - Summary Report
- Standard maritime industry format
- Category-level totals for current month and YTD
- Single-page PDF with fund receipts and operating expense categories
- Suitable for executive reporting and quick financial overview

### 2. Operative Statement - Detailed Report
- **Page 1**: Summary section (identical to summary-only report)
- **Page 2+**: Detailed account-wise breakdown with both current month and YTD amounts
- Account-level transactions organized by category
- Complete audit trail with account codes, descriptions, and amounts
- **CSV Export**: Detailed account data exported for further analysis
- Category subtotals for verification and reconciliation
- Professional formatting with grid lines and proper alignment
- Suitable for detailed financial analysis and audit purposes

### 3. Manager Statement - Excel Format
- Matches template structure from Manager Statement template
- Includes opening balance, fund receipts, operating expenses by category, and closing balance
- Serial numbers for each transaction
- Detailed transaction information including document numbers, dates, and remarks
- Professional formatting with proper column widths and styling
- Suitable for accounting teams and management review

### 4. Manager Statement - CSV Options
- **Monthly CSV**: Filtered for specific month only with budget accounts
- **Comprehensive CSV**: All fields from vessel expenses data
- **Budget-Filtered CSV**: Only accounts from budget categories + committed cost codes
- **Filtered CSV**: Specific fields matching the Excel template structure
- Suitable for data import, analysis, and integration with other systems

## Prerequisites

### Required Files
1. **Budget Category CSV**: `budget_category_data.csv` - Account code mapping file
2. **ShipNet Tool Access**: MCP vessel accounts tool for expense data retrieval
3. **Account Code Categories**: Predefined 999-account mappings for accruals and committed costs
4. **Excel Template**: Manager Statement template for Excel format

### System Requirements
- Python 3.x with required libraries:
  - `reportlab` for PDF generation
  - `pandas`, `openpyxl` for Excel generation
  - `json`, `csv` for data processing
  - `datetime` for date filtering
  - `collections` for data organization

## Data Sources and Strategy

### 1. Account Code Management

#### Primary Account Codes (from CSV)
```csv
_id,createdAt,createdAtIst,category,accountCode
687d19d1431e8c3b75848c75,2025-07-20T16:31:13.020Z,2025-07-20T22:01:13.020Z,ADMINISTRATIVE EXPENSES,6176-116
```

#### 999 Account Code Categories (Accruals/Committed Costs)
```python
categories_999 = {
    '5320-999': 'ADMINISTRATIVE EXPENSES',
    '5120-999': 'CREW EXPENSES',
    '5115-999': 'CREW WAGES',
    '5110-999': 'CREW WAGES',
    '5162-999': 'DRYDOCKING EXPENSES',
    '5170-999': 'EXTRA ORDINARY ITEMS',
    '5160-999': 'INSURANCE',
    '5152-999': 'LUBE OIL CONSUMPTION',
    '5156-999': 'MANAGEMENT FEES',
    '5158-999': 'MISCELLANEOUS',
    '5166-999': 'NON-BUDGETED EXPENSES',
    '5164-999': 'PRE-DELIVERY EXPENSES',
    '5165-999': 'PRE-DELIVERY EXPENSES',
    '5154-999': 'REPAIRS & MAINTENANCE',
    '5150-999': 'SPARES',
    '5144-999': 'STORES',
    '5130-999': 'VICTUALLING EXPENSES'
}
```

### 2. Data Querying Strategy

#### Step 1: Query Vessel Expenses from ShipNet Tool
```python
# Use MCP vessel_expenses tool
vessel_expenses = mcp.callTool("vessel_expenses", {
    "vesselCode": "VESSEL_CODE",  # Replace with actual vessel code
    "limit": 10000
})
```

#### Step 2: Filter by Time Periods
- **Current Month**: Filter expenses for specific month and year
- **Year-to-Date (YTD)**: Filter expenses from January 1st to end of specified month
- **Use posting date field** for accurate period filtering

#### Step 3: Account Code Filtering
- Combine CSV account codes with 999 account codes
- Implement pattern-based categorization for unmapped accounts
- Filter vessel expenses to include only relevant accounts
- Exclude balance sheet accounts (1xxx, 2xxx series) from operating expenses

### 3. Fund Receipt Handling

#### Fund Receipt Account Codes
```python
fund_receipt_accounts = {
    '2110-100': 'OWN VSL FUND A/C (AD OPEX FUN)',  # Budget Fund
    '2110-200': 'OWN VSL FUND CA (NB REP FUND)',   # Non-Budget Fund
    '2110-150': 'Additional Budget Fund',
    '2110-250': 'Additional Budget Fund',
    '2110-350': 'Additional Budget Fund',
    '2110-550': 'Additional Budget Fund'
}
```

#### Critical Rules for Fund Receipts
- **Always use `abs(amount)` for fund receipts**
- Fund receipts in system are typically negative but should show as positive in reports
- This ensures proper calculation of operative surplus/deficit
- **⚠️ CRITICAL WARNING**: Only use the accounts specified above (2110-xxx series)
- **DO NOT** include internal transfer accounts like 2100-104, 2100-100 as fund receipts

## Generation Process

### 1. Operative Statement Generation

#### Summary Report
```bash
python generate_operative_statement.py --vessel=VESSEL_CODE --month=MONTH --year=YEAR
```
This generates a single-page PDF with category-level totals for current month and YTD.

#### Detailed Report
```bash
python generate_detailed_operative_statement.py --vessel=VESSEL_CODE --month=MONTH --year=YEAR
```
This generates a multi-page PDF with summary page + detailed account breakdown + CSV export.

### 2. Manager Statement Generation

#### Excel Report
```bash
python generate_manager_statement_excel.py --vessel=VESSEL_CODE --month=MONTH --year=YEAR
```
This generates an Excel file matching the template structure with opening/closing balance, fund receipts, and categorized expenses.

#### CSV Export Options
```bash
# For specific month only with budget filtering
python generate_monthly_manager_statement.py --vessel=VESSEL_CODE --month=MONTH --year=YEAR

# For comprehensive export with all fields
python generate_manager_statement_csv.py --vessel=VESSEL_CODE --month=MONTH --year=YEAR

# For budget-filtered export
python generate_budget_filtered_manager_statement.py --vessel=VESSEL_CODE --month=MONTH --year=YEAR
```

### 3. All-in-One Generation
```bash
python generate_all_statements.py --vessel=VESSEL_CODE --month=MONTH --year=YEAR
```
This runs all the statement generation scripts in sequence and validates the outputs.

## PDF Report Generation Process

### 1. Summary Report Generation

#### Standard PDF Format (Summary Only)
```
SYNERGY MARINE PTE LTD.
Operative Statement - Summary

Owner: [OWNER_NAME] - [OWNER_COMPANY]              Voucher Style: 0 - Actuals
Vessel: [VESSEL_CODE] - [VESSEL_NAME]              Layout Id: Owners Report-OS

                        [START_DATE] to [END_DATE]  [YTD_START] to [YTD_END]

Particulars                    Amount in USD      Amount in USD
----------------------------------------------------------------
Funds Receipt                     XXX,XXX.XX      X,XXX,XXX.XX
Fund Receipt - Budget             XXX,XXX.XX      X,XXX,XXX.XX
Fund Receipt - Non Budget         XXX,XXX.XX        XXX,XXX.XX

Operating Expenses                XXX,XXX.XX      X,XXX,XXX.XX
Crew Wages                        XXX,XXX.XX        XXX,XXX.XX
Crew Expenses                      XX,XXX.XX        XXX,XXX.XX
Victualling Expenses                X,XXX.XX         XX,XXX.XX
Stores                             XX,XXX.XX         XX,XXX.XX
Spares                              X,XXX.XX         XX,XXX.XX
Lube oil Consumption               (X,XXX.XX)        XX,XXX.XX
Repairs & Maintenance               X,XXX.XX         XX,XXX.XX
Management Fee                     XX,XXX.XX         XX,XXX.XX
Miscellaneous                      XX,XXX.XX         XX,XXX.XX
Pre-Delivery Expenses                   0.00         XX,XXX.XX
Administrative Expenses             (XXX.XX)            XXX.XX

Operative Surplus (Deficit)        XX,XXX.XX        (XX,XXX.XX)

[REPORT_DATE] [TIME]                                     Page 1 of 1
```

### 2. Detailed Report Generation

#### Enhanced PDF Structure (Summary + Details)

**Page 1: Summary Section**
- Identical to summary-only report above
- Category-level totals for current month and YTD
- Fund receipts breakdown
- Operative surplus/deficit calculation

**Page 2+: Detailed Section**
```
SYNERGY MARINE PTE LTD.
Operative Statement - Detailed

Owner: [OWNER_NAME] - [OWNER_COMPANY]              Voucher Style: 0 - Actuals
Vessel: [VESSEL_CODE] - [VESSEL_NAME]              Layout Id: Owners Report-OS

                                  [START_DATE] to [END_DATE] [YTD_START] to [YTD_END]

FUND RECEIPTS - DETAILED
Account Code    Description          dd-mmm-yy to dd-mmm-yy       dd-mmm-yy to dd-mmm-yy 
------------------------------------------------------------------------
2110-100        OWN VSL FUND A/C (AD OPEX FUN)    XXX,XXX.XX    X,XXX,XXX.XX
2110-200        OWN VSL FUND CA (NB REP FUND)      XX,XXX.XX      XXX,XXX.XX

OPERATING EXPENSES - DETAILED

Crew Wages:
Account Code    Description                        dd-mmm-yy to dd-mmm-yy       dd-mmm-yy to dd-mmm-yy 
------------------------------------------------------------------------
5110-110        BASIC WAGES                         XX,XXX.XX      XXX,XXX.XX
5115-120        OVERTIME WAGES                       X,XXX.XX       XX,XXX.XX
TOTAL           Crew Wages Total                    XX,XXX.XX      XXX,XXX.XX

Crew Expenses:
Account Code    Description                       dd-mmm-yy to dd-mmm-yy       dd-mmm-yy to dd-mmm-yy 
------------------------------------------------------------------------
5120-110        CREW TRAVEL                          X,XXX.XX       XX,XXX.XX
5120-115        CREW MEDICAL                         X,XXX.XX       XX,XXX.XX
TOTAL           Crew Expenses Total                  X,XXX.XX       XX,XXX.XX

[Continue for all categories...]

[REPORT_DATE] [TIME]                                     Page 2 of 2
```

## Manager Statement Excel Format

### Excel Structure
```
SYNERGY MARINE PTE LTD.
Manager's Statement

Owner: [OWNER_NAME] - [OWNER_COMPANY]     Vessel: [VESSEL_CODE] - [VESSEL_NAME]     Vou.Style: 0
For the Period: [START_DATE] [END_DATE]

SN  Account  Description  Crew  Nat  Doc No  Date  Scan No  Po No  Po. Dt.  Particulars  Remarks  Cur  Amount  USD Amt.

Opening Balance                                                                                                [BALANCE]
Funds Receipt
Fund Receipt - Budget
1   2110-100  OWN VSL FUND A/C (AD OPEX FUN)       [DOC_NO]  [DATE]                    [REMARKS]  USD  [AMOUNT]  [AMOUNT]
Total Fund Receipt - Budget                                                                                   [TOTAL]
Total Funds Receipt                                                                                           [TOTAL]
Operating Expenses
Crew Wages
1   5110-100  BASIC WAGES                     IND  [DOC_NO]  [DATE]                    [PARTICULARS]  [REMARKS]  USD  [AMOUNT]  [AMOUNT]
...
```

## Manager Statement CSV Options

### 1. Monthly CSV
Filtered for specific month only with budget accounts + committed cost codes.
- **Fields**: SN, Account Code, Acc Description, Crew, Nat, Doc No, Date, Scan No, Po No, Po. Dt., Particulars, Remarks, Cur, Amount, USD Amt.
- **Filtering**: Specific month only + budget accounts + committed cost codes

### 2. Comprehensive CSV
All fields from vessel expenses data for all time periods.
- **Fields**: All available fields from vessel expenses tool
- **No Filtering**: All transactions for all time periods

### 3. Budget-Filtered CSV
Only accounts from budget categories + committed cost codes for all time periods.
- **Fields**: SN, Account Code, Acc Description, Crew, Nat, Doc No, Date, Scan No, Po No, Po. Dt., Particulars, Remarks, Cur, Amount, USD Amt.
- **Account Filtering**: Budget accounts + committed cost codes
- **No Time Filtering**: All time periods

### 4. Filtered CSV
Specific fields matching the Excel template structure.
- **Fields**: SN, Account Code, Acc Description, Crew, Nat, Doc No, Date, Scan No, Po No, Po. Dt., Particulars, Remarks, Cur, Amount, USD Amt.
- **No Filtering**: All transactions for all time periods

## Best Practices

### 1. Data Integrity
- **Never use hardcoded values** from reference documents
- **Always query fresh data** from ShipNet tool
- **Validate data completeness** before report generation
- **Cross-check totals** for mathematical accuracy

### 2. Report Generation
- Generate reports in the following order:
  1. Operative Statement Summary
  2. Operative Statement Detailed
  3. Manager Statement Excel
  4. Manager Statement CSVs
- Verify opening/closing balances match between reports
- Ensure consistent categorization across all reports
- Validate fund receipt handling is consistent

### 3. File Management
- Use consistent naming conventions for all files
- Store related files together for easy access
- Document the generation process and parameters
- Archive reports systematically for future reference

## Troubleshooting

### Common Issues and Solutions

#### Missing Account Codes
**Issue**: New account codes not in CSV mapping
**Solution**: 
- Add new codes to CSV file
- Update 999 account mapping if needed
- Implement pattern-based categorization for common account prefixes
- Re-run report generation

#### Incorrect Fund Receipt Signs
**Issue**: Fund receipts showing as negative
**Solution**: 
- Always use `abs(amount)` for fund receipts
- Verify fund receipt account identification logic
- Only use accounts specified in guidelines (2110-xxx series)

#### Excel Format Issues
**Issue**: Excel format doesn't match template
**Solution**:
- Verify column headers match template
- Check section ordering (opening balance, fund receipts, operating expenses, closing balance)
- Ensure proper date formatting
- Validate calculations for totals and balances

## Documentation
A comprehensive documentation file should be maintained for each vessel with detailed instructions for generating all report types.

## Report Selection Guidelines

### When to Use Summary Report Only
- Executive reporting and board presentations
- Quick financial performance overview
- Monthly management reports
- High-level stakeholder communication
- When detailed account breakdown is not required

### When to Use Detailed Report
- **Audit purposes and compliance requirements**
- **Detailed financial analysis and budget variance analysis**
- **Account-level expense tracking and monitoring**
- **Investigation of specific expense categories**
- **Preparation for external audits**
- **Internal control and expense validation**
- **Training and educational purposes for accounting staff**

### When to Use Manager Statement Excel
- For accounting teams requiring transaction-level details
- When opening and closing balances are needed
- For compatibility with existing accounting workflows
- When a familiar Excel format is required

### When to Use CSV Exports
- For data import into other accounting systems
- For custom analysis in spreadsheet applications
- For data transformation and manipulation
- For archival and data warehouse purposes

## Conclusion

This financial reporting system provides a complete solution for generating both operative statements and manager statements for any vessel. The implementation follows maritime industry standards and ensures accurate financial reporting with multiple output formats to meet different user needs.

### Key Success Factors:
✅ **Multiple Report Formats** - PDFs, Excel, and various CSV options
✅ **100% Tool-Queried Data** - Never use hardcoded values
✅ **Professional Formatting** - Maritime industry standard layouts
✅ **Complete Data Coverage** - Current Month + YTD for all accounts
✅ **Account-Level Transparency** - Full audit trail in detailed reports
✅ **Consistent Formatting** - Professional appearance across all report types
✅ **Automated Generation** - Single script to generate all reports

---

*Last Updated: July 25, 2025*