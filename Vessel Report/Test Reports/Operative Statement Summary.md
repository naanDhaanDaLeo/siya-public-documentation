# Operative Statement PDF Preparation Guide - Enhanced with Detailed Reports

## Overview
This document provides comprehensive guidelines for preparing **vessel operative statement PDFs** using the ShipNet tool and account categorization system. The guide covers both **summary-only reports** and **detailed reports with account-wise breakdown**. The process ensures 100% data accuracy by querying all information directly from the vessel management system.

## Report Types Available

### 1. Summary Report Only
- Standard maritime industry format
- Category-level totals for current month and YTD
- Single-page PDF with fund receipts and operating expense categories
- Suitable for executive reporting and quick financial overview

### 2. Detailed Report (Summary + Details)
- **Page 1**: Summary section (identical to summary-only report)
- **Page 2+**: Detailed account-wise breakdown with both current month and YTD amounts
- Account-level transactions organized by category
- Complete audit trail with account codes, descriptions, and amounts
- Suitable for detailed financial analysis and audit purposes

## Prerequisites

### Required Files
1. **Budget Category CSV**: Account code mapping file (e.g., `budget_category_data.csv`)
2. **ShipNet Tool Access**: MCP vessel accounts tool for expense data retrieval
3. **Account Code Categories**: Predefined 999-account mappings for accruals and committed costs

### System Requirements
- Python 3.x with required libraries:
  - `reportlab` for PDF generation (primary requirement)
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
    "vesselCode": "VESSEL_CODE",  # Use actual vessel code
    "limit": 10000
})
```

#### Step 2: Filter by Time Periods
- **Current Month**: Filter expenses for specific month
- **Year-to-Date (YTD)**: Filter expenses from January 1st to current month end
- **Use posting date field** for accurate period filtering

#### Step 3: Account Code Filtering
- Combine CSV account codes with 999 account codes
- Implement pattern-based categorization for unmapped accounts
- Filter vessel expenses to include only relevant accounts
- Exclude balance sheet accounts (1xxx, 2xxx series) from operating expenses
- Ensure no account codes are missed or duplicated

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
- These are budget transfer accounts, not actual fund receipts for operative statements

### 4. Category Mapping Strategy

#### Pattern-Based Account Categorization
For accounts not found in CSV mappings, implement pattern-based categorization:
```python
common_patterns = {
    # Crew related
    '5110': 'CREW WAGES',
    '5115': 'CREW WAGES', 
    '5120': 'CREW EXPENSES',
    
    # Stores and supplies
    '5130': 'VICTUALLING EXPENSES',
    '5140': 'STORES',
    '5142': 'STORES',
    '5144': 'STORES',
    '5150': 'SPARES',
    '5152': 'LUBE OIL CONSUMPTION',
    
    # Repairs and maintenance
    '5154': 'REPAIRS & MAINTENANCE',
    
    # Management and admin
    '5156': 'MANAGEMENT FEES',
    '5158': 'MISCELLANEOUS',
    '5320': 'ADMINISTRATIVE EXPENSES',
    '6176': 'ADMINISTRATIVE EXPENSES',
    
    # Insurance and extraordinary
    '5160': 'INSURANCE',
    '5162': 'DRYDOCKING EXPENSES',
    '5164': 'PRE-DELIVERY EXPENSES',
    '5165': 'PRE-DELIVERY EXPENSES',
    '5166': 'NON-BUDGETED EXPENSES',
    '5170': 'EXTRA ORDINARY ITEMS'
}
```

#### Standard Category Mapping
```python
category_mapping = {
    'CREW EXPENSES': 'Crew Expenses',
    'CREW WAGES': 'Crew Wages', 
    'VICTUALLING EXPENSES': 'Victualling Expenses',
    'STORES': 'Stores',
    'SPARES': 'Spares',
    'LUBE OIL CONSUMPTION': 'Lube oil Consumption',
    'REPAIRS & MAINTENANCE': 'Repairs & Maintenance',
    'MANAGEMENT FEES': 'Management Fee',
    'MISCELLANEOUS': 'Miscellaneous',
    'PRE-DELIVERY EXPENSES': 'Pre-Delivery Expenses',
    'ADMINISTRATIVE EXPENSES': 'Administrative Expenses',
    'DRYDOCKING EXPENSES': 'DryDocking Expenses',
    'INSURANCE': 'Insurance',
    'NON-BUDGETED EXPENSES': 'Non-Budgeted Expenses',
    'EXTRA ORDINARY ITEMS': 'Extra Ordinary Items'
}
```

#### Category Priority Order (for Report Display)
1. Crew Wages
2. Crew Expenses
3. Victualling Expenses
4. Stores
5. Spares
6. Lube oil Consumption
7. Repairs & Maintenance
8. Management Fee
9. Miscellaneous
10. Pre-Delivery Expenses
11. Administrative Expenses
12. DryDocking Expenses
13. Insurance
14. Non-Budgeted Expenses
15. Extra Ordinary Items

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

### 2. Detailed Report Generation (NEW)

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

### 3. Detailed Report Implementation Guidelines

#### Account-Level Data Processing
```python
def process_expenses_with_accounts(current_expenses, ytd_expenses):
    """Process expenses and create account-wise breakdown with both current and YTD amounts"""
    fund_receipt_accounts = defaultdict(lambda: {'current': 0, 'ytd': 0, 'description': ''})
    operating_accounts = defaultdict(lambda: {'current': 0, 'ytd': 0, 'description': '', 'category': ''})
    
    # Process current month and YTD expenses separately
    # Aggregate by account code while preserving descriptions and categories
    # Return structured data for detailed report generation
```

#### Detailed Section Layout Specifications
- **Page Size**: A4 (same as summary)
- **Margins**: 0.5 inch all sides
- **Font**: Helvetica (8pt for detailed data, 9pt for headers)
- **Table Structure**: 4 columns (Account Code, Description, Current Month, YTD)
- **Column Widths**: 1.2", 2.8", 1.5", 1.5"
- **Category Grouping**: Organized by expense categories with subtotals
- **Grid Lines**: Light grey grid for readability

#### Data Validation for Detailed Reports
- [ ] All accounts have proper descriptions
- [ ] Current month amounts ≤ YTD amounts (logical validation)
- [ ] Category subtotals match summary page totals
- [ ] Fund receipt accounts properly identified
- [ ] No duplicate account entries within categories
- [ ] All operating accounts properly categorized

### 4. Amount Formatting Rules (Applied to Both Report Types)

#### Positive Amounts
```
Format: XXX,XXX.XX
Example: 213,840.00
```

#### Negative Amounts (Deficits)
```
Format: (XXX,XXX.XX)
Example: (82,956.61)
```

#### Zero Amounts
```
Format: 0.00
```

## Implementation Code Templates

### 1. Summary Report Generator
```python
def generate_summary_pdf(vessel_code, report_month, report_year, csv_file_path, output_directory):
    """Generate summary-only operative statement PDF"""
    # Implementation for summary report only
    pass
```

### 2. Detailed Report Generator (NEW)
```python
def generate_detailed_pdf(vessel_code, report_month, report_year, csv_file_path, output_directory):
    """Generate detailed operative statement PDF with summary + account breakdown"""
    # Implementation for detailed report with both summary and detailed sections
    
    # Key components:
    # 1. Create summary section (Page 1)
    # 2. Create detailed fund receipts section (Page 2)
    # 3. Create detailed operating expenses by category (Page 2+)
    # 4. Include category subtotals
    # 5. Maintain consistent formatting and headers
    pass
```

### 3. Account-Level Data Processing (NEW)
```python
def process_expenses_with_accounts(current_expenses, ytd_expenses):
    """Process expenses for detailed account-wise reporting"""
    # Aggregate by account code
    # Preserve account descriptions
    # Calculate both current month and YTD amounts
    # Categorize accounts properly
    # Return structured data for detailed reporting
    pass
```

## Quality Assurance

### 1. Cross-Reference Validation

#### Compare Summary vs Detailed Totals
- Verify category totals in detailed section match summary page
- Check fund receipt totals consistency
- Validate operative surplus/deficit calculations
- Ensure YTD amounts include current month amounts

#### Account-Level Validation (NEW for Detailed Reports)
- Verify all accounts have proper descriptions
- Check for duplicate account entries
- Validate account code categorization
- Ensure no accounts are missing from detailed breakdown

### 2. Mathematical Validation

#### Balance Verification
```
Operative Surplus = Funds Receipt - Operating Expenses
```

#### YTD Logic Check
```
YTD Amount >= Current Month Amount (for all accounts and categories)
```

#### Detailed Report Consistency (NEW)
```
Sum of Account Details = Category Total (for each category)
Sum of Category Totals = Operating Expenses Total
```

## Common Issues and Solutions

### 1. Detailed Report Specific Issues (NEW)

#### Issue: Account Details Don't Match Category Totals
**Solution**: 
- Verify account categorization logic
- Check for missing or miscategorized accounts
- Validate aggregation calculations
- Ensure consistent date filtering for current and YTD

#### Issue: Detailed Report Performance with Large Data Sets
**Solution**:
- Implement efficient data aggregation
- Use appropriate data structures (defaultdict)
- Optimize PDF table generation for large account lists
- Consider pagination for very large detailed reports

#### Issue: Account Descriptions Truncated
**Solution**:
- Implement smart text truncation (35-40 characters)
- Add ellipsis (...) for truncated descriptions
- Ensure critical account information remains visible
- Consider tooltip or full description in separate column

### 2. Existing Issues (Apply to Both Report Types)

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
- **CRITICAL**: Only use accounts specified in guidelines (2110-xxx series)

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

## PDF Generation Automation Guidelines (Enhanced)

### 1. Report Type Selection
```python
def generate_operative_statement(
    vessel_code: str,
    report_month: int,
    report_year: int,
    csv_file_path: str,
    output_directory: str,
    report_type: str = "summary"  # "summary" or "detailed"
) -> str:
    """
    Generate operative statement PDF with specified report type
    Returns: PDF file path
    """
    if report_type == "detailed":
        return generate_detailed_pdf(vessel_code, report_month, report_year, csv_file_path, output_directory)
    else:
        return generate_summary_pdf(vessel_code, report_month, report_year, csv_file_path, output_directory)
```

### 2. File Naming Convention (Updated)
- **Summary Report**: `VESSEL_Operative_Statement_Summary_MMYYYY.pdf`
- **Detailed Report**: `VESSEL_Operative_Statement_Detailed_MMYYYY.pdf`
- **Full Detailed Report**: `VESSEL_Operative_Statement_Full_Detailed_MMYYYY.pdf`

### 3. Error Handling for Detailed Reports (NEW)
- Validate account-level data completeness
- Handle large data sets gracefully
- Implement memory-efficient processing
- Provide meaningful error messages for detailed report issues
- Log detailed report generation steps

## Best Practices (Enhanced)

### 1. Data Integrity for Both Report Types
- **Never use hardcoded values** from reference documents
- **Always query fresh data** from ShipNet tool
- **Validate data completeness** before PDF generation
- **Cross-check totals** for mathematical accuracy between summary and detailed sections

### 2. Report-Specific Best Practices

#### Summary Reports
- Focus on category-level insights
- Ensure clean, professional presentation
- Optimize for quick reading and decision-making

#### Detailed Reports (NEW)
- **Provide complete audit trail with account-level transparency**
- **Organize accounts logically within categories**
- **Include category subtotals for easy verification**
- **Maintain readability despite increased data volume**
- **Test PDF performance with large account lists**

### 3. Documentation and Version Control (Enhanced)
- Document report type selection criteria
- Maintain templates for both summary and detailed reports
- Version control all PDF generation scripts
- Record detailed report customizations by vessel
- Archive both summary and detailed reports systematically

## Conclusion

This enhanced guide ensures consistent, accurate, and flexible generation of **both summary and detailed vessel operative statement PDFs**. The detailed report option provides complete transparency and audit trail capabilities while maintaining the professional maritime industry standard format.

### Key Success Factors:
✅ **Flexible Report Types** - Summary-only or detailed with account breakdown
✅ **100% Tool-Queried Data** - Never use hardcoded values
✅ **Professional PDF Format** - Maritime industry standard layout for both report types
✅ **Complete Data Coverage** - Current Month + YTD for all accounts
✅ **Account-Level Transparency** - Full audit trail in detailed reports
✅ **Consistent Formatting** - Professional appearance across all report types
✅ **Print and Digital Ready** - Suitable for all distribution methods

### Report Selection Summary:
- **Summary Reports**: Executive overview, quick insights, category-level analysis
- **Detailed Reports**: Audit trail, account-level analysis, compliance requirements, detailed financial investigation

**Remember: Always query data from tools, choose appropriate report type based on requirements, maintain data integrity across both summary and detailed sections.**
