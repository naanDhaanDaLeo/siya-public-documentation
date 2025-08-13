# SYIA Defects QNA System – Technical Summary

## 🚀 Executive Overview

The SYIA Defects Question and Answer (QNA) System is a comprehensive maritime defect management platform that processes vessel inspection data and generates structured responses to defect-related queries. The system handles 19 different QNA modules that cover various inspection types and Report type defect categories in maritime operations.

## 🧭 System Architecture

### 🧩 Core Components

#### 1) 📥 Data Ingestion Layer
- **MongoDB Collections**: Multiple specialized collections for different defect types
- **Multi-Version Support**: Handles both V2 (legacy) and V3 (enhanced) data formats
- **Real-time Processing**: Continuous data synchronization from inspection sources

#### 2) ⚙️ Processing Engine
- **Python-based ETL**: Pandas and NumPy for data manipulation
- **Aggregation Pipelines**: MongoDB aggregation for complex data queries
- **Data Normalization**: Standardization across different data sources

#### 3) ❓ Question Processing Logic
- **Dynamic Query Generation**: Retrieves question text from menu files collection
- **Context-Aware Responses**: Generates answers based on vessel-specific data
- **Multi-format Output**: JSON components, text responses, and structured data

## 🗂️ QNA Module Categories

### 🔍 Inspection Type Defects
- Various inspection types (VIR, PSC, Flag State, SIRE, CDI, Navigational Audit, External Audit, Internal Audit, SCMM, Terminal, Charterer, Owner's inspections)

### 📝 Report Type Defects
- HMX, ADI and Incident Near Miss defects

### 📦 Others
- Defect summary content aggregation
- CoC
- Excel integration for defect lists

## 🔄 Common Processing Logic

### 1. Data Fetching Logic

The data fetching process is the foundation of each QNA module. It connects to multiple MongoDB databases and retrieves different types of data needed for processing. The system handles both legacy (V2) and modern (V3) data formats to ensure compatibility.

- Connects securely using environment variables for database URIs and names.
- Filters only ACTIVE vessels to scope downstream computations to in-service assets.
- Pulls both legacy (V2) and modern (V3) datasets to maximize coverage and ensure backward compatibility.
- Fetches the canonical question text to drive consistent answer generation.

This function retrieves four key pieces of information: active vessels (only those currently in operation), defect data from both old and new system versions, and the specific question text that will be answered. Each QNA module filters for different inspection types like VIR, PSC, SIRE, etc.

### 2. Data Processing Workflow

#### Step 1: Data Retrieval and Filtering
- Fetch active vessel data (only vessels with status "ACTIVE")
- Query V2 collections (legacy format) with specific inspection type filters
- Query V3 collections (enhanced format) with updated field names
- Retrieve question definitions from menu files collection

#### Step 2: Data Merging and Standardization

- Appends V2 and V3 datasets to create a unified defect dataset.
- Applies an inner join with active vessels to exclude inactive vessels from analysis.
- Produces a clean, consistent DataFrame for downstream computations.

#### Step 3: Overdue Detection Logic

```python
# Simple overdue logic used across QNA scripts
def check_overdue_status(row, current_date):
    target_date = row.get('targetDate') or row.get('dueDate')
    status = row.get('status', '').lower()
    
    # If target date exists and is past, and status is not closed
    if target_date and target_date < current_date and status != 'closed':
        return 'overdue'
    
    return status
```

**Explanation:**
- Determines overdue based on target/due date at row-level during preprocessing.
- Normalizes status to lowercase for consistent downstream mapping.
- Only flags overdue when a planned date exists and entry not closed/cancelled.
- Keeps original status if not overdue.

#### Step 4: Status Processing and Color Assignment

```python
def process_status_colors(df, current_date):
    # Convert all status to lowercase
    df['status'] = df['status'].apply(lambda x: x.lower() if isinstance(x, str) else x)
    
    # Check for overdue items
    df['status'] = df.apply(lambda row: 
        'overdue' if (pd.notna(row['targetDate']) and 
                      row['targetDate'] < current_date and 
                      row['status'] not in ['closed', 'cancelled']) 
        else row['status'], axis=1)
    
    # Assign colors based on status
    df['status_color'] = df['status'].map(status_colors)
    
    return df
```

**Explanation:**
- Converts statuses to lowercase to avoid case-related mismatches.
- Computes 'overdue' status using targetDate vs current_date while excluding closed/cancelled.
- Maps statuses to UI-friendly colors via a centralized dictionary for consistent rendering.

### 3. Status Color Map

```python
# Primary status color mapping for defect management
status_colors = {
    'closed': 'green',
    'open': 'blue',
    'review in progress': 'orange',
    'overdue': 'red'
}
```

**Explanation:**
- Provides a single source of truth for UI color semantics.
- Can be extended with more statuses without changing processing logic.
- Enables consistent visualization across dashboards and reports.

## 🛠️ Output Generation Logic

### 1) 🧾 Answer Formatting
- **Text Responses**: Human-readable summaries of defect status
- **JSON Components**: Structured data for web interface rendering
- **Statistical Summaries**: Aggregated counts and trend analysis

## ✅ Conclusion

The SYIA Defects QNA System provides a robust, scalable architecture for processing maritime defect data and generating intelligent responses to inspection-related queries. Through standardized processing patterns, comprehensive error handling, and flexible output generation, the system ensures consistent and reliable defect management across diverse inspection types and vessel operations.
