# Voyage Dashboard - Comprehensive Voyage Management System

Our voyage dashboard provides a comprehensive suite of voyage management tools that enable fleet operators to monitor, analyze, and optimize vessel performance across all aspects of maritime operations. This integrated system combines voyage tracking, charter party compliance, resource management, and AI-powered forecasting to deliver complete operational visibility and control.

## Data Sources

The voyage dashboard integrates multiple data sources to provide comprehensive operational insights:

- **ERP System**: Primary source for voyage details, consumption logs, and operational data
- **Vessel Consumption Logs**: Real-time fuel, oil, and fresh water consumption data
- **Charter Party Agreements**: Speed and consumption requirements for compliance monitoring
- **Weather Data**: Environmental conditions affecting vessel performance
- **Vessel Shop Trial Data**: Baseline performance metrics and specifications
- **ETA/ETD Information**: Schedule data for predictive analysis
- **Historical Performance Data**: Long-term trends and patterns for forecasting

## System Architecture

The voyage dashboard consists of six integrated modules working together to provide comprehensive voyage management:

### 1. Voyage Details & Tracking
**Core voyage information and status monitoring**

### 2. Charter Party Compliance  
**Real-time compliance analysis against charter requirements**

### 3. Fuel Oil Management
**Comprehensive fuel consumption and ROB analysis**

### 4. Cylinder Lube Oil Monitoring
**Engine lubrication system management**

### 5. System Oil Analysis
**Main and auxiliary engine system oil tracking**

### 6. Fresh Water Management
**Advanced water resource management with AI forecasting**

## Module Functionalities

### Voyage Details & Tracking Module

**Purpose**: Centralized voyage information management and vessel status tracking

**Key Features**:
- **Current voyage tracking** with voyage number and start dates
- **Charterer information** including head charterer and cargo details
- **Vessel synchronization status** between ERP systems
- **Charter party instructions** for speed and consumption requirements
- **Real-time voyage status updates** from multiple data sources

**Data Processing**:
```python
def process_voyage_data():
    # Extract current voyage information
    voyage_details = {
        "voyage_number": get_current_voyage(),
        "start_date": get_voyage_start_date(),
        "charterer": get_head_charterer(),
        "cargo_type": get_cargo_information(),
        "cp_speed": get_charter_party_speed(),
        "cp_consumption": get_charter_party_consumption()
    }
    return voyage_details
```

### Charter Party Compliance Module

**Purpose**: Real-time monitoring of vessel performance against charter party requirements

**Key Features**:
- **Speed compliance analysis** comparing actual vs charter party speed
- **Consumption compliance tracking** for fuel efficiency requirements
- **Weather impact assessment** with wind force corrections
- **Color-coded status indicators** (Green: Met, Red: Not Met, Orange: At Port/Anchor)
- **Slip percentage calculations** for performance deviations

**Compliance Calculation**:
```bash
# Compliance Status Logic
IF vessel_status == "AT SEA":
    speed_compliance = actual_speed >= (charter_speed - tolerance)
    consumption_compliance = actual_consumption <= (charter_consumption + tolerance)
    
    IF speed_compliance AND consumption_compliance:
        status = "COMPLIANT" (Green)
    ELSE:
        status = "NON_COMPLIANT" (Red)
        
ELIF vessel_status == "AT PORT/ANCHOR":
    status = "AT_PORT" (Orange)
```

### Fuel Oil Management Module

**Purpose**: Comprehensive fuel consumption analysis and remaining on board (ROB) management

**Key Features**:
- **Multi-grade fuel tracking** (HSFO, ULSFO, MDO, LSMGO, VLSFO, LNG)
- **Engine-specific consumption** (Main Engine, Auxiliary Engine, Boiler)
- **ROB level monitoring** with safety margin alerts
- **Consumption forecasting** based on historical patterns
- **Fuel efficiency analysis** and optimization recommendations

**ROB Calculation**:
```python
def calculate_fuel_rob(consumption_data, current_rob):
    # Calculate remaining days based on consumption patterns
    daily_consumption = get_average_daily_consumption(consumption_data)
    safety_margin = 0.1  # 10% safety buffer
    
    effective_rob = current_rob * (1 - safety_margin)
    remaining_days = effective_rob / daily_consumption
    
    return {
        "current_rob": current_rob,
        "daily_consumption": daily_consumption,
        "remaining_days": remaining_days,
        "safety_status": get_safety_status(remaining_days)
    }
```

### Cylinder Lube Oil Monitoring Module

**Purpose**: Specialized monitoring of cylinder lubricating oil for main and auxiliary engines

**Key Features**:
- **LSBN/HSBN consumption tracking** for different oil grades
- **Engine-specific monitoring** (Main Engine, Auxiliary Engine)
- **90-day rolling averages** for trend analysis
- **ROB level alerts** with remaining days calculations
- **Monthly consumption patterns** and forecasting

**CLO Analysis**:
```bash
# Cylinder Lube Oil Consumption Analysis
monthly_consumption = total_consumption / steaming_hours * 30 * 24
remaining_days = current_rob / daily_average_consumption
status = "CRITICAL" if remaining_days < 30 else "NORMAL"
```

### System Oil Analysis Module

**Purpose**: Monitoring of main engine and auxiliary engine system oil consumption

**Key Features**:
- **MECC (Main Engine System Oil) tracking**
- **AECC (Auxiliary Engine System Oil) monitoring**
- **Consumption vs steaming time correlation**
- **Monthly consumption averages** and trend analysis
- **ROB forecasting** with operational planning support

**System Oil Metrics**:
```python
def analyze_system_oil(mecc_data, aecc_data, steaming_hours):
    # Calculate consumption rates per steaming hour
    mecc_rate = mecc_consumption / steaming_hours
    aecc_rate = aecc_consumption / steaming_hours
    
    # Generate monthly projections
    monthly_projection = {
        "mecc_monthly": mecc_rate * monthly_steaming_hours,
        "aecc_monthly": aecc_rate * monthly_steaming_hours,
        "total_monthly": (mecc_rate + aecc_rate) * monthly_steaming_hours
    }
    
    return monthly_projection
```

### Fresh Water Management Module

**Purpose**: Advanced fresh water resource management with AI-powered forecasting

**Key Features**:
- **Production vs consumption analysis** with detailed breakdowns
- **ROB level monitoring** with capacity management
- **AI-powered forecasting** using Chronos time-series models
- **15-day prediction horizon** for operational planning
- **Sailing status integration** for consumption pattern analysis
- **Tank capacity optimization** and overflow prevention

**AI-Powered Forecasting**:
```python
def forecast_fresh_water_consumption():
    # Use Chronos model for time-series forecasting
    sailing_forecast = chronos_model.predict(sailing_pattern_data)
    
    # Calculate consumption based on sailing status
    consumption_forecast = []
    for day in forecast_period:
        if sailing_forecast[day] == "SAILING":
            daily_consumption = sailing_consumption_rate
        else:
            daily_consumption = port_consumption_rate
            
        consumption_forecast.append(daily_consumption)
    
    return {
        "15_day_forecast": consumption_forecast,
        "total_consumption": sum(consumption_forecast),
        "rob_projection": calculate_rob_projection(consumption_forecast)
    }
```

## Advanced Analytics & AI Integration

### Time-Series Forecasting
The system employs advanced AI models for predictive analysis:

- **Chronos Model Integration**: State-of-the-art time-series forecasting
- **Multi-horizon Predictions**: 15-day operational forecasts
- **Pattern Recognition**: Sailing vs port consumption patterns
- **Adaptive Learning**: Continuous model improvement based on actual data

### Predictive Maintenance Indicators
```bash
# Resource Management Alerts
IF remaining_days < 30: alert_level = "CRITICAL"
ELIF remaining_days < 60: alert_level = "WARNING"  
ELSE: alert_level = "NORMAL"

# Consumption Anomaly Detection
IF current_consumption > (average_consumption * 1.2):
    flag = "HIGH_CONSUMPTION_ANOMALY"
ELIF current_consumption < (average_consumption * 0.8):
    flag = "LOW_CONSUMPTION_ANOMALY"
```

## Dashboard Outputs

### Integrated Voyage Overview
- **Real-time voyage status** with comprehensive vessel information
- **Multi-module summary view** showing all critical metrics
- **Performance indicators** across all resource categories
- **Compliance status dashboard** with color-coded alerts

### Resource Management Center
- **Fuel oil status panel** with ROB levels and consumption rates
- **Lubricating oil monitoring** for all engine systems
- **Fresh water management** with production/consumption balance
- **Predictive alerts** for resource planning

### Compliance & Performance Analytics
- **Charter party compliance scorecard** with real-time status
- **Performance trend analysis** across multiple voyages
- **Efficiency metrics** and optimization recommendations
- **Weather impact assessments** and corrections

### Predictive Analytics Dashboard
- **15-day resource forecasts** using AI models
- **Consumption pattern analysis** with sailing status correlation
- **ROB projections** with safety margin calculations
- **Operational planning support** with predictive insights



## Benefits

### Operational Excellence
- **Comprehensive voyage visibility** across all operational aspects
- **Real-time compliance monitoring** preventing charter party violations
- **Proactive resource management** avoiding critical shortages
- **AI-powered forecasting** enabling strategic planning

### Cost Optimization
- **Fuel efficiency improvements** through consumption analysis
- **Optimal resource allocation** based on predictive forecasting
- **Charter party compliance** avoiding penalties and claims
- **Preventive maintenance scheduling** reducing emergency costs

### Risk Management
- **Early warning systems** for resource depletion
- **Compliance violation prevention** with real-time monitoring
- **Weather impact assessment** for performance evaluation
- **Predictive maintenance** reducing operational risks

### Decision Support
- **Data-driven insights** for operational decisions
- **Multi-scenario forecasting** for voyage planning
- **Performance benchmarking** against charter requirements
- **Resource optimization** recommendations based on AI analysis
