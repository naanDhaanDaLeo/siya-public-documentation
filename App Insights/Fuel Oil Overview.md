# Fuel Oil Dashboard

Our fuel oil dashboard provides comprehensive fuel oil management and analysis capabilities for maritime vessels through three specialized analysis modules: **Fuel Oil Bunker Sample Test Analysis**, **Fuel Oil Tank-wise Bunker Distribution**, and **Fleet-wise Fuel Oil Bunker Analysis**. These integrated systems deliver real-time insights into fuel quality compliance, tank capacity optimization, and fleet-wide fuel performance monitoring.

The dashboard enables fleet operators to maintain fuel quality standards through automated laboratory report analysis, optimize tank utilization with real-time capacity monitoring and safety calculations, and ensure fleet-wide compliance through comprehensive CatFine contamination assessment and sulfur content verification. This integrated approach supports operational efficiency, regulatory compliance, and cost optimization across maritime fuel oil management operations.

## Data Sources

The scripts integrate data from multiple sources to provide accurate and comprehensive fuel oil analysis:

- **MongoDB ETL Data Database**: Primary source for operational fuel oil data and vessel information
- **Laboratory Test Results**: Comprehensive fuel analysis reports from MARITEC, BV, FOBAS, and other certified laboratories
- **Vessel Details Collection**: Active vessel information and technical specifications
- **Tank Capacity Collections**: Vessel tank specifications for both V2 and V3 systems
- **Consumption Log API**: Real-time vessel consumption and ROB (Remaining on Board) data
- **Fleet Hierarchy Data**: Group details, fleet mapping, and vessel assignments
- **S3 Storage**: PDF laboratory reports and analysis documents

## Fuel Oil Bunker Sample Test Analysis

### Purpose
The Fuel Oil Bunker Sample Test Analysis script processes and analyzes fuel oil bunker sample test data from various testing laboratories to provide comprehensive insights about fuel quality and compliance for maritime vessels. This system ensures fuel quality meets operational requirements and regulatory standards.

### Key Features
- **Multi-laboratory data integration** from MARITEC, BV, FOBAS, and other certified testing facilities
- **Intelligent fuel type classification** based on sulfur content analysis
- **Interactive report generation** with embedded PDF access
- **Historical trend analysis** covering 13 months of fuel quality data
- **BDN compliance verification** comparing supplier declarations with laboratory results

### Data Collection Strategy
The system establishes connections to three different MongoDB collections to gather comprehensive fuel oil information. It retrieves active vessel information from the vessel details collection, accesses 13 months of historical test data from the fuel oil overview collection, and pulls question metadata from the menu files collection. This multi-source approach ensures complete coverage while balancing data completeness with system performance.

### Fuel Type Classification Methodology

#### Classification Formula
The system implements intelligent fuel type classification based on sulfur content analysis, following industry-standard thresholds:

```bash
Fuel Type Classification Logic:
If sulfur_value ≤ 0.1% → ULSFO/LSMGO (Ultra Low Sulfur Fuel Oil/Low Sulfur Marine Gas Oil)
If 0.1% < sulfur_value ≤ 0.5% → VLSFO (Very Low Sulfur Fuel Oil)
If 0.5% < sulfur_value < 3.6% → HSFO (High Sulfur Fuel Oil)
If sulfur_value ≥ 3.6% → Unknown
If sulfur_value is missing → Cannot be calculated
```

This classification is crucial for regulatory compliance and operational planning, as different fuel types have varying environmental regulations and engine compatibility requirements.

### Data Processing Logic
The system processes fuel oil data by grouping records by vessel IMO and fuel type, then identifies the most recent bunker date for each combination. This ensures only the latest fuel oil data is used for analysis, preventing outdated information from affecting operational decisions. The system creates structured markdown reports with detailed BDN information and comprehensive test laboratory results.

### Output Generation
The script produces comprehensive fuel oil analysis reports including:
- **Interactive Tables**: Test parameters with drill-down capabilities and PDF report access
- **Fuel Classification Summaries**: Based on industry standards with regulatory implications
- **BDN Compliance Analysis**: Comparing supplier declarations with laboratory results
- **Historical Trend Data**: Enabling fuel quality monitoring over time

---

## Fuel Oil Tank-wise Bunker Distribution

### Purpose
The Fuel Oil Tank-wise Bunker Distribution script provides real-time analysis of fuel oil distribution across vessel tanks, calculating tank capacities, current ROB (Remaining on Board), and available bunker intake capacity. This system ensures safe bunkering operations and optimal fuel management.

### Key Features
- **Real-time tank monitoring** across all vessel fuel tanks
- **Dual system architecture** supporting both V2 (legacy) and V3 (modern) vessel systems
- **Safety threshold calculations** preventing tank overflow and operational hazards
- **Interactive visualization** with tank-wise distribution charts
- **Automated ROB validation** correcting sensor errors and measurement inconsistencies

### Multi-Source Data Integration
The system integrates data from three distinct sources: Consumption Log API for real-time vessel consumption and ROB data, Tank Capacity Collections containing vessel specifications for both V2 and V3 systems, and Tank ROB Collections with current fuel levels. This approach ensures data accuracy and provides redundancy for critical operational decisions.

### Tank Capacity Calculation Methodology

#### Safety Threshold Formulas
The system implements industry-standard tank capacity calculations to ensure safe bunkering operations:

```bash
Tank Capacity Calculations:
Tank Capacity (90%) = Full Tank Capacity × 0.90
Tank Capacity (85%) = Full Tank Capacity × 0.85
Bunker Intake (85%) = Maximum(0, Tank Capacity (85%) - Current ROB)
```

The 85% capacity rule serves as the primary safety threshold for bunkering operations, preventing tank overflow while maximizing fuel storage. The 90% threshold provides additional safety margin for operational planning, accounting for thermal expansion, vessel movement, and measurement uncertainties.

#### ROB Validation Logic
The system implements sophisticated ROB validation to handle measurement errors:

```bash
ROB Validation Rules:
If Current ROB > Tank Capacity (100%) → Set ROB = Tank Capacity (100%)
If Current ROB < 0 → Set ROB = 0
Otherwise → Use Current ROB as reported
```

### System Architecture Logic
The system accommodates two vessel data architectures: V3 vessels with modern systems providing direct API integration and real-time monitoring, and V2 vessels with legacy systems requiring separate data sources and manual reconciliation. This dual approach ensures comprehensive fleet coverage regardless of vessel technology generation.

### Output Generation
The system produces comprehensive tank distribution analysis including:
- **Summary Tables**: Tank details with capacity and ROB information for all vessel tanks
- **Interactive Charts**: Visual representation of fuel distribution patterns with tank classification
- **Operational Alerts**: Tank capacity warnings and maintenance notifications
- **Bunker Intake Recommendations**: Based on current capacity and operational requirements

---

## Fleet-wise Fuel Oil Bunker Analysis

### Purpose
The Fleet-wise Fuel Oil Bunker Analysis script provides comprehensive fleet-level analysis of fuel oil quality, focusing on CatFine (Catalytic Fines) contamination and sulfur compliance across multiple vessels in a fleet. This system enables fleet managers to assess fuel quality risks and ensure regulatory compliance at the fleet level.

### Key Features
- **Fleet hierarchy integration** across multiple organizational levels
- **CatFine risk assessment** with automated intervention recommendations
- **Sulfur compliance verification** ensuring regulatory adherence
- **Interactive risk visualization** with color-coded fleet status indicators
- **Automated reporting** with executive summaries and detailed analysis

### Fleet Data Integration Strategy
The system establishes comprehensive fleet-level analysis through hierarchical data integration: Group Details for fleet structure understanding, Fleet Mapping for vessel relationships, Vessel Assignment for active fleet member identification, and Bunker Data Correlation for fuel quality information across the entire fleet.

### CatFine Risk Assessment Methodology

#### Risk Classification Formula
The system analyzes Aluminum and Silicon content (CatFine) to assess catalytic fines contamination risk:

```bash
CatFine Risk Classification:
If Al+Si ≤ 15 mg/kg → Safe (Optimal operating range)
If 15 < Al+Si < 25 mg/kg → Moderate Risk (Regular monitoring recommended)
If 25 ≤ Al+Si < 35 mg/kg → Elevated Risk (Enhanced monitoring needed)
If Al+Si ≥ 35 mg/kg → Dangerous (Immediate intervention required)
```

The 15 mg/kg threshold represents the industry-accepted safe operating limit. Higher levels indicate increasing risk of engine component wear and potential failure, with specific operational recommendations for each risk level.

### Sulfur Compliance Verification

#### Compliance Assessment Formula
The system compares laboratory-tested sulfur content against BDN declared values:

```bash
Sulfur Compliance Assessment:
If Lab Sulfur ≤ BDN Sulfur → Met (Compliant with declaration)
If Lab Sulfur > BDN Sulfur → Not Met (Non-compliant, requires action)
If data unavailable → Not Applicable (Requires investigation)
```

Non-compliance indicates potential fuel quality issues, supplier problems, or regulatory violations requiring immediate attention and corrective action.

### Advanced Data Processing
The system implements intelligent fuel grade filtering to focus CatFine analysis on appropriate fuel types, automatically excluding distillate fuels where catalytic fines contamination is not relevant. Multi-parameter data cleaning handles inconsistent formats from different laboratories while maintaining audit trails for quality control.

### Output Generation
The system produces comprehensive fleet-level fuel quality analysis including:
- **Executive Summaries**: Overall fleet fuel quality status and key performance indicators
- **Risk Analysis**: Vessels with CatFine contamination issues and intervention recommendations
- **Compliance Reports**: BDN sulfur adherence across the entire fleet with regulatory implications
- **Interactive Visualizations**: Plotly charts for detailed analysis and trend identification

## Benefits

### Operational Benefits
- **Real-time fuel quality monitoring** across individual vessels and entire fleets
- **Predictive maintenance support** through early detection of fuel quality issues
- **Cost optimization** through efficient fuel procurement and usage analysis
- **Safety enhancement** through tank overflow prevention and capacity management

### Regulatory Compliance
- **Laboratory compliance verification** ensuring fuel meets international standards
- **BDN validation** comparing supplier declarations with actual test results
- **Historical data retention** for regulatory audits and compliance reporting
- **Standardized analysis methodologies** following maritime industry guidelines

### Business Value
- **Risk mitigation** through proactive identification of fuel quality problems
- **Fleet optimization** through comparative vessel and fleet performance analysis
- **Supplier evaluation** with comprehensive fuel quality assessment capabilities
- **Operational efficiency** through data-driven fuel management decisions

---

*These quality assurance scripts provide essential tools for maritime operators to maintain fuel quality standards, optimize tank utilization, and support efficient fleet operations through comprehensive data analysis and automated monitoring capabilities.*
