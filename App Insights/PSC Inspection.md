# PSC Inspection Dashboard - Technical Documentation

## Overview

The PSC (Port State Control) Inspection Dashboard is a comprehensive data processing and visualization system designed to aggregate, process, and present PSC inspection data from multiple sources. The system handles vessel inspection records, defect tracking, and corrective actions across different ERP system versions while providing structured outputs for web-based dashboards.

## Data Sources

The system integrates data from multiple authoritative sources to provide comprehensive PSC inspection coverage:

### Primary Data Sources
- **Company ERP System**: Internal vessel management and defect tracking (V2 and V3 schema versions)
- **Fleet Distribution Data**: Vessel registry and operational status information
- **PSC MOU Websites**: Real-time inspection data from international port state control authorities

### PSC MOU Coverage
- **Paris MOU** 
- **Tokyo MOU** 
- **Abuja MOU** 
- **Carribbean MOU** 
- **Mediterranean MOU** 
- **Black Sea MOU** 
- **Indian MOU**


*Note: Data availability varies by MOU region and may not be complete for all jurisdictions.*

## System Architecture

The system is built on a modern technology stack designed for scalability and reliability:

- **Language**: Python with advanced data processing libraries
- **Database**: MongoDB for flexible document storage
- **Data Processing**: Pandas for complex data manipulation
- **Version Management**: Intelligent handling of ERP system schema evolution

## Key Features

### Multi-Version ERP Data Support

The system seamlessly handles different versions of the company's ERP system data schemas:

- **V2 Schema**: Legacy inspection and defect data format
- **V3 Schema**: Enhanced schema with expanded corrective action tracking and cross-referencing capabilities

```python
def fetch_vessel_version(imo):
    """Determines ERP data schema version for vessel"""
    if db[v3_collection].find_one({"imo": imo}):
        return "V3"
    else:
        return "V2"
```

### Intelligent Data Processing

The system processes complex, nested inspection data from multiple sources:

```python
# Status color coding for visual clarity
status_colors = {
    "Closed": "green",
    "Open": "blue", 
    "Review in progress": "orange",
    "Invalid": "purple",
    "Overdue": "red"
}
```

### Comprehensive Data Integration

```python
# Multi-source data aggregation
filtered_df = df_vessel_details[
    (df_vessel_details["VesselStatus_Compare"] == "ACTIVE")
]
filtered_df["version"] = filtered_df["imo"].apply(fetch_vessel_version)
```

## Data Processing Workflow

### 1. Data Collection
- Retrieves vessel information from ERP system collections
- Fetches PSC inspection records from MOU websites
- Cross-references defect data across V2 and V3 schemas

### 2. Data Standardization
- Normalizes date formats across different data sources
- Standardizes status classifications and terminology
- Handles missing data with consistent placeholder values

### 3. Processing & Analysis
- Processes nested defect structures and corrective actions
- Calculates pending defect statistics and status distributions
- Generates cross-referenced findings between ERP versions

### 4. Output Generation
- Creates interactive data tables with sortable columns
- Generates comprehensive HTML reports with embedded statistics
- Produces JSON structures for frontend dashboard consumption

## Database Operations

The system maintains data integrity through structured update operations:

```python
query = {"questionNo": 46, "imo": vessel_imo}
update = {
    "$set": {
        "refreshDate": datetime.utcnow(),
        "answer": generated_report,
        "questionNo": 46,
        "question": "PSC Inspection defects"
    }
}
collection.update_one(query, update, upsert=True)
```

## Output Formats

### Interactive Data Tables
- AG-Grid compatible JSON structures with expandable details
- Status-based color coding for immediate visual assessment
- Sortable and filterable columns for efficient data navigation

### Comprehensive Reports
- Markdown-formatted inspection summaries
- Statistical dashboards with pending defect counts
- Latest inspection status from PSC MOU websites
- Clear attribution of data sources and versions

### Real-time Updates
- Automated refresh cycles for current inspection status
- Timestamp tracking for data freshness validation
- Component-based updates for modular dashboard integration

## Business Value

### Operational Excellence
- **Unified View**: Single dashboard combining ERP data with external PSC sources
- **Real-time Monitoring**: Current status of all vessel inspections and defects
- **Historical Analysis**: Trend tracking across multiple data schema versions

### Compliance Management
- **Regulatory Oversight**: Comprehensive tracking of PSC inspection requirements
- **Audit Trail**: Complete documentation of defect lifecycle and corrective actions
- **Risk Assessment**: Early identification of potential compliance issues

### Decision Support
- **Executive Dashboards**: High-level summaries for management reporting
- **Operational Insights**: Detailed defect analysis for fleet managers
- **Performance Metrics**: KPIs for inspection success rates and response times

---

*The PSC Inspection Dashboard represents a sophisticated integration of internal ERP systems with external regulatory data sources, providing comprehensive visibility into vessel compliance status and inspection management.*
